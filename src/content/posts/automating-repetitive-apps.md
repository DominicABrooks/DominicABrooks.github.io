---
title: "Design Patterns for 100% Critical Coverage"
description: "Page object model with intent-based actions"
published: 2025-09-29
updated: 2025-09-29
tags: [Test Automation, Page Object Model, Playwright]
category: 'Test Automation'
draft: false 
---

After a focused effort, we lifted automated coverage of our critical capabilities from 15% to 100%, without the suite becoming a maintenance burden.

When QA teams aren't the ones designing the test cases, they're often asked to automate long, repetitive end-to-end flows. For example, hundreds of regression test cases that fill out insurance eApplications. These flows usually start life as manual test cases, so designing them well for automation makes a big difference.

I've automated regression suites for internal eApp platforms, contributed to existing eApp frameworks, and built frameworks for several third-party eApp vendors.

From working in other frameworks, building my own, and designing them with others, I've landed on an opinionated set of patterns that keep these large E2E suites resilient to change.

These patterns have worked really well for me and have made maintaining these suites much easier.

### Page Object Model with Intent-Based Actions

One thing I've realized is key to maintainable page objects is focusing on business actions rather than UI implementations. Public methods should describe what the user wants to achieve, and leave the UI details inside the page object.

So many times a function will be very specific to the implementation, and has to be changed in nearly every single spec file in order to be accurate to the actions being completed.

#### Why use intent-based names for public methods?

Instead of exposing UI mechanics like `clickSubmit()` or `fillFirstName()`, create methods that represent complete actions or logical units of information. This makes tests more resilient to UI changes and keeps them focused on business scenarios.

:::caution[Before]
UI-mechanics focused
::: 
```ts
await page.clickNameField();
await page.typeFirstName('John');
await page.clickNextButton();
await page.selectFromDropdown('Owner');
```

:::tip[After]
Intent-focused
:::
```ts
await page.fillOwnerInformation({
  firstName: 'John',
  role: 'Owner'
});
```

Tests become more descriptive, focusing on what should happen rather than how it happens. When UI changes occur, the tests remain stable because they're detached from specific UI implementations. Even complete form restructuring such as adding/removing fields shouldn't change what functions need to be called in your tests.

#### Strong typing and encapsulation

Playwright example:

```ts
// Page object extends the base class
export class ApplicationPage extends PageComponent {
  // Private UI implementation details
  private readonly firstNameField: Locator;
  private readonly roleDropdown: Locator;
  private readonly submitButton: Locator;

  constructor(page: Page) {
    super(page);
    this.firstNameField = page.getByRole('textbox', { name: 'First Name' });
    this.roleDropdown = page.getByRole('combobox', { name: 'Role' });
    this.submitButton = page.getByRole('button', { name: 'Submit' });
  }

  // Private locator helpers
  private getRoleOption(role: string): Locator {
    return this.page.getByRole('option', { name: role });
  }

  // Private field setters. withOptionalValue (defined on PageComponent)
  // skips the setter when the value is undefined, so callers can pass partial data
  private setFirstName = this.withOptionalValue(async (value: string) => {
    await this.firstNameField.fill(value);
  });

  private selectRole = this.withOptionalValue(async (role: string) => {
    await this.roleDropdown.click();
    await this.getRoleOption(role).click();
  });

  // Public methods express business intent
  async fillOwnerInformation(info: {
    firstName?: string;
    role?: string;
  }) {
    await this.setFirstName(info.firstName);
    await this.selectRole(info.role);
  }

  // High-level business action
  async submitApplication(): Promise<void> {
    await this.submitButton.click();
  }
}
```

### High-level fill methods accept partial data

Accepting partial payloads lets tests pass only the fields relevant to a scenario and reduces churn when forms evolve.

```ts
async fillOwnerInformation(payload: {
  firstName?: string;
  lastName?: string;
  representative?: { type?: string; id?: string };
}) {
  await this.setFirstName(payload.firstName);
  await this.setLastName(payload.lastName);
  // more setters...
}
```

:::tip
Keep all selectors within page objects, treating them as the single source of truth for UI interactions. When elements move or change, updates only need to happen in one place.
:::

### Centralized Data Factories and Fixtures

Another crucial pattern is centralizing test data generation in factories and fixtures. This ensures that when data requirements change (e.g., new validation rules), you only need to update one place instead of hundreds of tests.

Auth data is a great example. When credentials (email, ID, username, password) live in a factory, a change to password requirements becomes a one-line update instead of a change across hundreds of spec files.

The payoff grows with every validation rule change: you update the factory once, and every test picks it up automatically.

```ts
// fixtures/data-factories.ts
export function generateOwnerInformation() {
  return {
    firstName: validFirstName(),
    lastName: validLastName(),
    ssn: validSSN(),
    address: generateAddress(),
    contact: generateContactInfo()
  };
}

// Can compose smaller, focused factories
export function generateAddress() {
  return {
    street: '123 Main St',
    city: 'Springfield',
    state: 'IL',
    zip: '62701'
  };
}

// fixtures/test-data.ts
import { test as base } from '@playwright/test';
import { generateOwnerInformation } from './data-factories';

// Type for all our data factories
interface DataFactories {
  owner: ReturnType<typeof generateOwnerInformation>;
}

// Extend the test fixture with our data
export const test = base.extend<DataFactories>({
  owner: async ({}, use) => {
    await use(generateOwnerInformation());
  }
});
```

Then your tests stay clean and use the fixture data:

```ts
test('can submit owner application', async ({ page, owner }) => {
  const appPage = new ApplicationPage(page);
  await appPage.fillOwnerInformation(owner);
  // ... rest of test
});

// Need different data? Override just what you need
test('handles missing contact info', async ({ page, owner }) => {
  const appPage = new ApplicationPage(page);
  await appPage.fillOwnerInformation({
    ...owner,
    contact: undefined
  });
});
```

Data factories provide a single source of truth for test data, letting you build complex objects from smaller factories while maintaining type safety. This keeps tests focused on behavior and keeps your data easy to update in one place.

## Closing

The best test strategies balance every layer of the pyramid (I wrote more about that in [Getting the Most Value From Your E2E Tests](/posts/stop-writing-e2e/)). When the business needs broad E2E coverage, intent-based page objects, partial-data methods, and centralized data factories make even large suites easy to maintain.