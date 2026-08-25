## Playwright with JavaScript
*Syntax reference from setup through running test suites*

### Project Setup
##### Quick Install
```
npm init playwright@latest
```
- Scaffolds `playwright.config.js`, a `tests/` folder, and installs browser binaries automatically

##### Manual Install
```
npm install -D @playwright/test
npx playwright install
```

### Basic Syntax

##### Browser, Context, and Page (raw API)
```javascript
const { chromium } = require('playwright');

const browser = await chromium.launch({ headless: false });
const context = await browser.newContext();
const page = await context.newPage();
await page.goto('https://example.com');
```
- With `@playwright/test` (the standard test runner), `page`/`context`/`browser` are provided automatically as fixtures - no manual setup needed inside a test

##### Locators
*Playwright's Locator API auto-waits and auto-retries, reducing flaky tests*

| Locator         | Example                                                |
| ---------------- | --------------------------------------------------------|
| By CSS            | `page.locator('#username')`                            |
| By text           | `page.getByText('Sign In')`                             |
| By role           | `page.getByRole('button', { name: 'Submit' })`          |
| By label          | `page.getByLabel('Email')`                              |
| By placeholder    | `page.getByPlaceholder('Enter your email')`             |
| By test id        | `page.getByTestId('submit-btn')`                        |
| By XPath          | `page.locator("xpath=//button[text()='Submit']")`       |

##### Common Locator Actions
```javascript
const locator = page.locator('#username');

await locator.click();
await locator.fill('hello');
await locator.clear();
await locator.check();
await locator.uncheck();
await locator.selectOption('value1');
await locator.press('Enter');
await locator.textContent();
await locator.getAttribute('value');
await locator.isVisible();
await locator.isEnabled();
await locator.isChecked();
```

##### Browser Navigation
```javascript
await page.goto('https://example.com');
await page.goBack();
await page.goForward();
await page.reload();

await page.close();     // closes the current page/tab
await context.close();  // closes the browser context
await browser.close();  // closes the browser
```

##### Waits
*Playwright auto-waits for elements to be actionable before every action - explicit waits are rarely needed*
```javascript
await page.waitForSelector('#element');
await page.waitForLoadState('networkidle');
await page.waitForURL('**/dashboard');

await locator.waitFor({ state: 'visible' });
```

##### Handling Dialogs, Frames, and Multiple Pages
```javascript
// Dialogs (alert/confirm/prompt)
page.on('dialog', dialog => dialog.accept());

// Frames
const frame = page.frameLocator('#frameId');
await frame.locator('#innerElement').click();

// New tabs/windows
const [newPage] = await Promise.all([
    context.waitForEvent('page'),
    page.locator('a[target=_blank]').click(),
]);
```

##### Mouse & Keyboard
```javascript
await page.mouse.move(100, 200);
await page.mouse.click(100, 200);
await page.keyboard.press('Control+A');
await page.keyboard.type('hello');
```

##### Assertions (`expect`)
*Auto-retrying assertions from `@playwright/test` - wait until the condition is met or the timeout expires*
```javascript
import { expect } from '@playwright/test';

await expect(page.locator('#message')).toBeVisible();
await expect(page.locator('#message')).toHaveText('Success');
await expect(page).toHaveTitle('Dashboard');
await expect(page).toHaveURL('https://example.com/dashboard');
```

### `@playwright/test` Framework Basics
*The built-in test runner - equivalent to TestNG for the Java version*

##### Hooks & Execution Order
```javascript
test.beforeAll(async () => { ... });          // once before all tests in the file
test.beforeEach(async ({ page }) => { ... }); // before every test
test('test name', async ({ page }) => { ... });
test.afterEach(async ({ page }) => { ... });  // after every test
test.afterAll(async () => { ... });           // once after all tests in the file
```

##### Example Test File
```javascript
const { test, expect } = require('@playwright/test');

test.describe('Login', () => {
    test.beforeEach(async ({ page }) => {
        await page.goto('https://example.com/login');
    });

    test('valid login', async ({ page }) => {
        await page.locator('#username').fill('user1');
        await page.locator('#password').fill('pass1');
        await page.locator('#submit').click();
        await expect(page).toHaveURL('https://example.com/dashboard');
    });
});
```

##### Tags, Skipping, and Parameterization
```javascript
test('smoke test @smoke', async ({ page }) => { ... });
test.skip('not ready yet', async ({ page }) => { ... });
test.only('run just this one', async ({ page }) => { ... });

for (const user of [{ name: 'user1' }, { name: 'user2' }]) {
    test(`login as ${user.name}`, async ({ page }) => { ... });
}
```

### Creating a Test Suite (`playwright.config.js`)
*JS Playwright has no XML suite file - suites are defined via config **projects** and test tags instead*

##### Basic Config
```javascript
// playwright.config.js
module.exports = {
    testDir: './tests',
    projects: [
        { name: 'chromium', use: { browserName: 'chromium' } },
        { name: 'firefox', use: { browserName: 'firefox' } },
        { name: 'webkit', use: { browserName: 'webkit' } },
    ],
};
```
- Each `project` behaves like a `<test>` block in `testng.xml` - a named group of tests run with its own settings, and all projects run in parallel by default

##### Grouping Tests Like Suites
- Use tags in test titles (`@smoke`, `@regression`) and filter with `--grep` at runtime
- Or maintain separate config files per suite, similar to keeping separate `smoke.xml`/`regression.xml` files:

```javascript
// smoke.config.js
module.exports = {
    testDir: './tests/smoke',
    projects: [{ name: 'chromium', use: { browserName: 'chromium' } }],
};
```

### Running Tests via CLI
```
npx playwright test
npx playwright test tests/login.spec.js
npx playwright test --project=chromium
npx playwright test --grep @smoke
npx playwright test --grep-invert @broken
npx playwright test --config=smoke.config.js
npx playwright test --headed
npx playwright show-report
```

##### Recording & Setup Helpers
```
npx playwright install    # installs browser binaries
npx playwright codegen    # records actions and generates test code
```

### Quick CLI Cheat Sheet
| Goal                            | Command                                        |
| --------------------------------- | ------------------------------------------------ |
| Install browsers                   | `npx playwright install`                       |
| Run all tests                      | `npx playwright test`                          |
| Run a single file                   | `npx playwright test tests/login.spec.js`      |
| Run a specific project/browser       | `npx playwright test --project=chromium`       |
| Run by tag                          | `npx playwright test --grep @smoke`            |
| Run a specific suite config          | `npx playwright test --config=smoke.config.js` |
| Generate a test via recording        | `npx playwright codegen`                       |
| View last HTML report               | `npx playwright show-report`                   |
