---
title: "Design Patterns for 100% Critical Coverage"
description: "The page object and test data patterns that kept a large Playwright suite of insurance eApp flows easy to maintain"
published: 2025-09-29
updated: 2025-09-29
tags: [Test Automation, Page Object Model, Playwright]
category: 'Test Automation'
draft: false 
---

After a focused push, we took automated coverage of our critical capabilities from 15% to 100%, and the suite is still easy to maintain.

When QA teams aren't the ones designing the test cases, they're often asked to automate long, repetitive end-to-end flows, like hundreds of regression cases that fill out insurance eApplications. Those flows usually start life as manual test cases, so how you design them for automation makes a big difference.

I've built and worked on suites like this for several eApp platforms, internal and third-party, and these are the patterns I've landed on.

## Name methods after what the user wants to do

The most important thing I've found for maintainable page objects is naming public methods after what the user is trying to do. The page object keeps the details of how the UI does it.

Methods like `clickSubmit()` or `fillFirstName()` mirror the UI, so when the UI changes, nearly every spec file changes with it. A method like `fillOwnerInformation()` describes a whole step:

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

Now the test reads like the business scenario. Even a big change to the form, like adding or removing fields, shouldn't change which methods your tests call.

## Keep the UI details private

Here's what that looks like in a Playwright page object. The locators and field setters are private, and the public methods describe business actions:

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

## Accept partial data

Fill methods take partial payloads, so each test passes only the fields its scenario cares about, and the form can change without touching every test:

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
Keep every selector inside a page object. When an element moves, you change it in one place.
:::

## Put test data in factories and fixtures

The other pattern I rely on is building test data in factories and fixtures, so a new validation rule means one change in one place. If credentials live in a factory, for example, a new password requirement is a one-line fix.

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

Tests then use the fixture, and override only what they need:

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

If you're weighing how much belongs at the E2E level in the first place, I wrote about that in [Getting the Most Value From Your E2E Tests](/posts/stop-writing-e2e/).
