## Playwright with Java
*Syntax reference from setup through running test suites*
> *see [[JS Playwright]] for js implementation*
### Project Setup
##### Maven Dependencies (`pom.xml`)
```xml
<dependencies>
    <dependency>
        <groupId>com.microsoft.playwright</groupId>
        <artifactId>playwright</artifactId>
        <version>1.46.0</version>
    </dependency>
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.10.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```
##### Installing Browsers
- Playwright bundles its own browser binaries (Chromium, Firefox, WebKit) - install them once after adding the dependency:
```
mvn exec:java -e -Dexec.mainClass=com.microsoft.playwright.CLI -Dexec.args="install"
```

### Basic Syntax

##### Playwright, Browser, Context, and Page Initialization
```java
Playwright playwright = Playwright.create();
Browser browser = playwright.chromium().launch(new BrowserType.LaunchOptions().setHeadless(false));
BrowserContext context = browser.newContext();
Page page = context.newPage();
page.navigate("https://example.com");
```
- `Playwright` -> manages the driver process
- `Browser` -> a single browser instance (chromium/firefox/webkit)
- `BrowserContext` -> an isolated session (like an incognito profile) - use one per test for isolation
- `Page` -> a single tab

##### Locators
*Playwright's Locator API auto-waits and auto-retries, reducing flaky tests*

| Locator         | Example                                                                          |
| ---------------- | --------------------------------------------------------------------------------- |
| By CSS            | `page.locator("#username")`                                                     |
| By text           | `page.getByText("Sign In")`                                                     |
| By role           | `page.getByRole(AriaRole.BUTTON, new Page.GetByRoleOptions().setName("Submit"))`|
| By label          | `page.getByLabel("Email")`                                                      |
| By placeholder    | `page.getByPlaceholder("Enter your email")`                                     |
| By test id        | `page.getByTestId("submit-btn")`                                                |
| By XPath          | `page.locator("xpath=//button[text()='Submit']")`                              |

##### Common Locator Actions
```java
Locator locator = page.locator("#username");

locator.click();
locator.fill("hello");
locator.clear();
locator.check();
locator.uncheck();
locator.selectOption("value1");
locator.press("Enter");
locator.textContent();
locator.getAttribute("value");
locator.isVisible();
locator.isEnabled();
locator.isChecked();
```

##### Browser Navigation
```java
page.navigate("https://example.com");
page.goBack();
page.goForward();
page.reload();

page.close();        // closes the current page/tab
context.close();     // closes the browser context
browser.close();     // closes the browser
playwright.close();  // stops the driver process
```

##### Waits
*Playwright auto-waits for elements to be actionable before every action - explicit waits are rarely needed*
```java
page.waitForSelector("#element");
page.waitForLoadState(LoadState.NETWORKIDLE);
page.waitForURL("**/dashboard");

locator.waitFor(new Locator.WaitForOptions()
    .setState(WaitForSelectorState.VISIBLE));
```

##### Handling Dialogs, Frames, and Multiple Pages
```java
// Dialogs (alert/confirm/prompt)
page.onDialog(dialog -> dialog.accept());

// Frames
FrameLocator frame = page.frameLocator("#frameId");
frame.locator("#innerElement").click();

// New tabs/windows
Page newPage = context.waitForPage(() -> {
    page.locator("a[target=_blank]").click();
});
```

##### Mouse & Keyboard
```java
page.mouse().move(100, 200);
page.mouse().click(100, 200);
page.keyboard().press("Control+A");
page.keyboard().type("hello");
```

##### Assertions (`PlaywrightAssertions`)
*Auto-retrying assertions - wait until the condition is met or the timeout expires*
```java
import static com.microsoft.playwright.assertions.PlaywrightAssertions.assertThat;

assertThat(page.locator("#message")).isVisible();
assertThat(page.locator("#message")).hasText("Success");
assertThat(page).hasTitle("Dashboard");
assertThat(page).hasURL("https://example.com/dashboard");
```

### TestNG Framework Basics
*Same TestNG framework used with Selenium - only the driver setup changes*

##### Annotations & Execution Order
```java
@BeforeSuite   -> once before the entire suite
@BeforeTest    -> once before each <test> tag in the XML
@BeforeClass   -> once before the first method in the class
@BeforeMethod  -> before every @Test method
@Test          -> the test method itself
@AfterMethod   -> after every @Test method
@AfterClass    -> once after the last method in the class
@AfterTest     -> once after each <test> tag in the XML
@AfterSuite    -> once after the entire suite
```

##### Example Test Class
```java
public class LoginTest {
    Playwright playwright;
    Browser browser;
    BrowserContext context;
    Page page;

    @BeforeMethod
    public void setup() {
        playwright = Playwright.create();
        browser = playwright.chromium().launch();
        context = browser.newContext();
        page = context.newPage();
    }

    @Test
    public void validLogin() {
        page.navigate("https://example.com/login");
        page.locator("#username").fill("user1");
        page.locator("#password").fill("pass1");
        page.locator("#submit").click();
        assertThat(page).hasURL("https://example.com/dashboard");
    }

    @AfterMethod
    public void teardown() {
        browser.close();
        playwright.close();
    }
}
```

##### Test Attributes & Parameterization
```java
@Test(priority = 1)
@Test(groups = {"smoke"})
@Test(dependsOnMethods = {"login"})

@Parameters({"browser"})
@Test
public void launchBrowser(String browser) { ... }
```

### Creating a Test Suite (`testng.xml`)
*Identical mechanism to Selenium - Playwright doesn't change how TestNG suites are structured*

##### Basic Structure
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="RegressionSuite" verbose="1">
    <test name="LoginTests">
        <classes>
            <class name="com.example.tests.LoginTest"/>
            <class name="com.example.tests.LogoutTest"/>
        </classes>
    </test>
</suite>
```

##### Passing Parameters
```xml
<suite name="RegressionSuite">
    <parameter name="browser" value="chromium"/>
    <test name="LoginTests">
        <classes>
            <class name="com.example.tests.LoginTest"/>
        </classes>
    </test>
</suite>
```

##### Running by Groups (include/exclude)
```xml
<test name="SmokeTests">
    <groups>
        <run>
            <include name="smoke"/>
            <exclude name="broken"/>
        </run>
    </groups>
    <classes>
        <class name="com.example.tests.LoginTest"/>
    </classes>
</test>
```

##### Multiple Tests & Parallel Execution
```xml
<suite name="FullSuite" parallel="tests" thread-count="2">
    <test name="ChromiumTests">
        <parameter name="browser" value="chromium"/>
        <classes>
            <class name="com.example.tests.LoginTest"/>
        </classes>
    </test>
    <test name="FirefoxTests">
        <parameter name="browser" value="firefox"/>
        <classes>
            <class name="com.example.tests.LoginTest"/>
        </classes>
    </test>
</suite>
```
- Playwright supports Chromium, Firefox, and WebKit from the same API, so cross-browser runs only require swapping `playwright.chromium()` for `playwright.firefox()` or `playwright.webkit()` based on the passed parameter

##### Separate Suite Files
- `smoke.xml` - critical path tests only
- `regression.xml` - full test coverage
- `cross-browser.xml` - same tests run against chromium/firefox/webkit

### Running Tests via CLI

##### Maven + Surefire Plugin
Point Surefire at a specific suite file in `pom.xml`:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.2.5</version>
    <configuration>
        <suiteXmlFiles>
            <suiteXmlFile>testng.xml</suiteXmlFile>
        </suiteXmlFiles>
    </configuration>
</plugin>
```

Common commands:
```
mvn test
mvn test -Dtest=LoginTest
mvn test -Dtest=LoginTest#validLogin
mvn test -DsuiteXmlFile=smoke.xml
mvn test -Dgroups=smoke
```

##### Running Suite Files Directly with TestNG (no Maven)
```
java -cp "lib/*;bin" org.testng.TestNG testng.xml
```
- On macOS/Linux, replace `;` with `:` in the classpath

##### Running Individual Suites
```
java -cp "lib/*;bin" org.testng.TestNG smoke.xml
java -cp "lib/*;bin" org.testng.TestNG regression.xml

# Run multiple suite files in one command
java -cp "lib/*;bin" org.testng.TestNG smoke.xml regression.xml
```

##### Running via IDE
- Right-click a `testng.xml` file -> **Run**
- Right-click a test class or method -> **Run** (executes without needing an XML suite)

### Quick CLI Cheat Sheet
| Goal                          | Command                                                                                 |
| ------------------------------ | ------------------------------------------------------------------------------------------ |
| Install Playwright browsers     | `mvn exec:java -e -Dexec.mainClass=com.microsoft.playwright.CLI -Dexec.args="install"` |
| Run all tests (Maven)           | `mvn test`                                                                              |
| Run a single class              | `mvn test -Dtest=LoginTest`                                                             |
| Run a single method             | `mvn test -Dtest=LoginTest#validLogin`                                                  |
| Run a specific suite file        | `mvn test -DsuiteXmlFile=smoke.xml`                                                     |
| Run by group                    | `mvn test -Dgroups=smoke`                                                               |
| Run suite without Maven         | `java -cp "lib/*;bin" org.testng.TestNG smoke.xml`                                      |
