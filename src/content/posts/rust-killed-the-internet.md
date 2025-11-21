---
title: "Rust just killed the internet"
description: "A QA perspective on Cloudflare's outage and how testing could have prevented it"
published: 2025-11-18
updated: 2025-11-18
tags: [Incident Analysis, QA Engineering, Testing Strategy]
category: 'Engineering'
draft: false 
---

On November 18, 2025, Cloudflare experienced a six-hour outage affecting their core CDN services. The root cause was a Rust panic in their FL2 proxy system when a Bot Management configuration file exceeded expected limits. As a QA engineer, this incident highlights several testing gaps that could have caught this issue before production.

## What Happened

At 11:05 UTC, Cloudflare deployed a ClickHouse database permissions change. This change caused a SQL query to return duplicate rows, which pushed the Bot Management feature count from ~60 to over 200. The FL2 proxy had a hard limit of 200 features with preallocated memory, and when this limit was exceeded, the code called `unwrap()` on an `Err` value, causing a panic.

The problematic query:

```sql
SELECT name, type FROM system.columns 
WHERE table = 'http_requests_features' 
ORDER BY name;
```

This query lacked a database filter, assuming it would only return columns from the `default` database. After the permissions change, it also returned columns from the `r0` database.

The Rust code that panicked:

```rust
// Simplified version of the error
thread fl2_worker_thread panicked: called Result::unwrap() on an Err value
```

## Testing Gaps

### 1. Missing Integration Tests for Database Changes

The ClickHouse permissions change was deployed without integration tests that validated the Bot Management feature file generation. A proper test suite should have included:

```rust
#[test]
fn test_feature_file_generation_with_new_permissions() {
    // Simulate the new permission model
    let query_result = run_query_with_new_permissions();
    
    // Validate the feature count stays within limits
    assert!(query_result.features.len() <= 200);
    
    // Validate no duplicate features
    let unique_features: HashSet<_> = query_result.features.iter().collect();
    assert_eq!(unique_features.len(), query_result.features.len());
}
```

### 2. No Boundary Testing for Configuration Limits

The system had a hard limit of 200 features but was only using ~60. This 3x buffer seemed safe, but there were no tests validating behavior at or near the limit:

```rust
#[test]
fn test_feature_limit_boundary() {
    // Test at exactly 200 features
    let features = generate_features(200);
    assert!(load_features(features).is_ok());
    
    // Test at 201 features (should handle gracefully)
    let features = generate_features(201);
    match load_features(features) {
        Ok(_) => panic!("Should have returned an error"),
        Err(e) => {
            // Should log error and use fallback, not panic
            assert!(!e.is_panic());
        }
    }
}
```

### 3. Improper Error Handling

The code used `unwrap()` instead of proper error handling. This should have been caught in code review, but automated testing can also help:

```rust
// Bad: panics on error
let features = parse_features(data).unwrap();

// Good: handles error gracefully
let features = match parse_features(data) {
    Ok(f) => f,
    Err(e) => {
        log::error!("Failed to parse features: {}", e);
        return default_features();
    }
};
```

A linting rule or custom clippy lint could flag all `unwrap()` calls in production code paths.

### 4. Lack of Chaos Engineering

The intermittent nature of the failure (good file, then bad file every 5 minutes) suggests the system wasn't tested under gradual rollout conditions. Chaos engineering tests could simulate:

- Partial cluster updates
- Inconsistent data across nodes
- Configuration file corruption
- Resource exhaustion scenarios

### 5. Missing Contract Tests for SQL Queries

The SQL query had an implicit contract: "return columns only from the default database." This contract wasn't documented or tested. A contract test would look like:

```rust
#[test]
fn test_feature_query_returns_only_default_database() {
    let result = query_feature_columns();
    
    for row in result {
        assert_eq!(row.database, "default", 
            "Query should only return columns from default database");
    }
}
```

## Prevention Strategies

### 1. Validate All Configuration Inputs

Even internally generated configuration should be validated:

```rust
fn load_bot_features(config: &FeatureConfig) -> Result<Features, FeatureError> {
    // Validate before processing
    if config.features.len() > MAX_FEATURES {
        return Err(FeatureError::TooManyFeatures {
            count: config.features.len(),
            max: MAX_FEATURES,
        });
    }
    
    // Check for duplicates
    let unique_count = config.features.iter().collect::<HashSet<_>>().len();
    if unique_count != config.features.len() {
        return Err(FeatureError::DuplicateFeatures);
    }
    
    // Process features
    Ok(process_features(config))
}
```

### 2. Implement Gradual Rollout with Automated Rollback

The configuration file was deployed globally every 5 minutes. A better approach:

- Deploy to canary servers first
- Monitor error rates and latency
- Automatically rollback if thresholds are exceeded
- Only proceed to full deployment after canary validation

### 3. Add Property-Based Testing

Property-based testing could have caught the duplicate row issue:

```rust
#[quickcheck]
fn feature_query_returns_unique_rows(db_state: DatabaseState) -> bool {
    let result = query_feature_columns(&db_state);
    let unique_count = result.iter().collect::<HashSet<_>>().len();
    unique_count == result.len()
}
```

### 4. Enforce Error Handling Standards

Add CI checks to prevent `unwrap()` in critical paths:

```bash
# In CI pipeline
cargo clippy -- -D clippy::unwrap_used -D clippy::expect_used
```

Or use a more nuanced approach with `#[deny]` attributes on specific modules:

```rust
#![deny(clippy::unwrap_used)]
#![deny(clippy::expect_used)]
```

### 5. Load Testing with Realistic Data Variations

Load tests should include:

- Configuration files at various sizes (10%, 50%, 90%, 100%, 110% of limits)
- Malformed configuration data
- Unexpected data types or structures
- Concurrent configuration updates

### 6. Better Observability in Testing

The system had observability in production, but tests should also validate:

- Error logging works correctly
- Metrics are emitted for configuration loads
- Alerts trigger at appropriate thresholds

```rust
#[test]
fn test_feature_limit_exceeded_emits_metric() {
    let metrics = MetricsCollector::new();
    let features = generate_features(201);
    
    let _ = load_features(features);
    
    assert!(metrics.has_metric("bot_features.limit_exceeded"));
}
```

## Key Takeaways for QA Engineers

1. **Test database schema changes**: Any database migration or permissions change should have integration tests validating dependent queries.

2. **Boundary testing is critical**: Don't just test happy paths. Test at limits, beyond limits, and with malformed data.

3. **Validate error handling**: Ensure errors are handled gracefully, not with panics or unwraps.

4. **Test gradual rollouts**: Simulate partial deployments and inconsistent states across clusters.

5. **Automate configuration validation**: Treat configuration as code and validate it with the same rigor.

6. **Document implicit contracts**: If code assumes certain data shapes or limits, document and test those assumptions.

The Cloudflare incident wasn't caused by a lack of testing infrastructure. They have sophisticated monitoring, gradual rollouts, and multiple environments. The gap was in testing the interaction between systems during changes. As QA engineers, our job is to think about these edge cases and build test suites that catch them before they reach production.

*Based on Cloudflare's [incident report](https://blog.cloudflare.com/18-november-2025-outage/) published on November 18, 2025.*
