Playwright is an end-to-end testing library for React and other web applications that enables cross-browser automated testing of user interactions to ensure everything works 100% from the back end to the user.

TIL how to run Playwright tests using the `@playwright/test` runner, compose tests with `test` and `expect` APIs, and enable video recording for each test to assist with debugging.


**Basic test structure**:

```ts
import { test, expect } from '@playwright/test';
test('basic test example', async ({ page }) => {
	await page.goto('https://example.com');
	await expect(page).toHaveTitle(/Example Domain/);
 });
```

**Running tests:**

```bash
npx playwright test
```

**To enable video recording in `playwright.config.ts` :**

```ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  use: {
    video: 'on-first-retry', // records video only if test fails on first try
    // or use 'on' to always record
  },
});
```

Videos are saved per test and help in visualizing UI flows and failures, making debugging easier.