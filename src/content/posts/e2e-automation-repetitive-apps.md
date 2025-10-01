---
title: "My favorite flavor of POM"
description: "Patterns for creating maintainable long repetitive end-to-end flows"
published: 2025-09-29
updated: 2025-09-29
tags: [Playwright, Test Automation, TypeScript, Design Patterns, Best Practices]
category: 'Test Automation'
draft: true 
---

This is my first blog post, I'm currently working as a QA Engineering working towards an SDET title. I figured what better way to start a blog as an automation specialist than one of the most fundamental parts of automation engineering, POM, or page object model.

It's not entirely necessary to follow this practice to a tee, especially in smaller or less repetitive suites, but historically I find this isn't the case.

When you aren't the one creating the test cases, I find many companies ask QAE teams to automate long, repetitive end-to-end flows. For example hundreds of regression test cases filling out eApplications. These flows are often based off of manual test cases, not necessarily designed with automation in mind.

I've automated regression suites for teams such as CivicServe eApp and worked on existing eApp frameworks such as Aspida eApp, as well as built frameworks for our third-party eApp vendors such as Firelight and AnnuityNet, and Affirm. 

From my experiences working in other frameworks, building my own, and designing them with others I now have a pretty opinionated set of patterns and practical tricks that make those large E2E suites resilient to change I like to enforce.

Not saying these rules are perfect, but they absolutely have worked for me, and have made maintaining these suites pretty easy.

### Page Object Model with Intent-Based Actions

One thing I've realized is key to maintainable page objects is focusing on business actions rather than UI implementations. Public methods should describe what the user wants to achieve, not how the UI makes it happen.

So many times a function will be very specific to the implementation, and has to be changed in nearly every single spec file in order to be accurate to the actions being completed.

#### Why avoid action verbs in public methods?

Instead of exposing UI mechanics like `clickSubmit()` or `fillFirstName()`, create methods that represent complete actions or logical units of information. This makes tests more resilient to UI changes and keeps them focused on business scenarios.

:::caution[What to Avoid]
UI-mechanics focused
::: 
```ts
await page.clickNameField();
await page.typeFirstName('John');
await page.clickNextButton();
await page.selectFromDropdown('Owner');
```

:::tip[Best Practice]
Intent-focused
:::
```ts
await page.addOwnerDetails({
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

  // Private field setters
  private setFirstName = this.withOptionalValue(async (value: string) => {
    await this.firstNameField.fill(value);
  });

  private selectRole = this.withOptionalValue(async (role: string) => {
    await this.roleDropdown.click();
    await this.getRoleOption(role).click();
  });

  // Public methods express business intent
  async addOwnerInformation(info: { 
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
Keep all selectors within page objects, treating them as the single source of truth for UI interactions. When elements move or change, updates only need to happen in one place
:::

### Centralized Data Factories and Fixtures

Another crucial pattern is centralizing test data generation in factories and fixtures. This ensures that when data requirements change (e.g., new validation rules), you only need to update one place instead of hundreds of tests.

A nightmare I ran into was someone storing all their auth data (email, id, username, password) at the start of every test. Inevitably when the password requirements changed and this password needed to be changed, we were stuck refactoring hundreds of spec files to refactor how we were pulling the auth data.

This isn't too terrible because it's a few static strings, but I can imagine there's going to be scenarios where this waste days of effort to refactor. Especially validation logic changes that make you go back and analyze your test data across all the tests

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
  const appPage = new ExampleApplicationStepPage(page);
  await appPage.fillOwnerInformation(owner);
  // ... rest of test
});

// Need different data? Override just what you need
test('handles missing contact info', async ({ page, owner }) => {
  const appPage = new ExampleApplicationStepPage(page);
  await appPage.fillOwnerInformation({
    ...owner,
    contact: undefined
  });
});
```

Data factories provide a single source of truth for test data, letting you build complex objects from smaller factories while maintaining type safety. This keeps tests focused on behavior and avoids using literals you'll need to change across multiple tests.

## Closing
Automating repetitive, long E2E flows isn’t usually a good idea. But with careful design, strong OOP practices, and defensive helpers, it's possible to make these flows not an absolute nightmare to maintain.