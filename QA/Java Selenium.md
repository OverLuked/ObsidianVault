## Selenium with Java
*Syntax reference from setup through running test suites*

### Project Setup
##### Maven Dependencies (`pom.xml`)
```xml
<dependencies>
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-java</artifactId>
        <version>4.21.0</version>
    </dependency>
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.10.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```
- Selenium 4.6+ has built-in driver management (Selenium Manager) - no need for WebDriverManager in most cases

### Basic Syntax

##### WebDriver Initialization
```java
WebDriver driver = new ChromeDriver();
driver.get("https://example.com");
```

##### Locators - `By` class
| Locator                  | Example                                    |
| ------------------------ | ------------------------------------------- |
| By.id                     | `By.id("username")`                        |
| By.name                   | `By.name("email")`                         |
| By.className              | `By.className("btn-primary")`              |
| By.tagName                | `By.tagName("input")`                      |
| By.linkText               | `By.linkText("Sign In")`                   |
| By.partialLinkText        | `By.partialLinkText("Sign")`                |
| By.cssSelector            | `By.cssSelector("div.card > a")`           |
| By.xpath                  | `By.xpath("//button[text()='Submit']")`    |

##### Finding Elements
```java
WebElement element = driver.findElement(By.id("username"));
List<WebElement> elements = driver.findElements(By.className("item"));
```

##### Common WebElement Actions
```java
element.click();
element.sendKeys("hello");
element.clear();
element.getText();
element.getAttribute("value");
element.isDisplayed();
element.isEnabled();
element.isSelected();
```

##### Browser Navigation
```java
driver.navigate().to("https://example.com");
driver.navigate().back();
driver.navigate().forward();
driver.navigate().refresh();

driver.close();  // closes current tab/window
driver.quit();   // closes all windows and ends the session
```

##### Waits
*Prevents flaky tests caused by timing issues*
```java
// Implicit wait - applies globally to all findElement calls
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));

// Explicit wait - waits for a specific condition
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.elementToBeClickable(By.id("submit")));

// Fluent wait - explicit wait with custom polling interval and ignored exceptions
Wait<WebDriver> fluentWait = new FluentWait<>(driver)
    .withTimeout(Duration.ofSeconds(20))
    .pollingEvery(Duration.ofMillis(500))
    .ignoring(NoSuchElementException.class);
```

##### Handling Alerts, Frames, and Windows
```java
// Alerts
Alert alert = driver.switchTo().alert();
alert.accept();
alert.dismiss();
alert.getText();

// Frames
driver.switchTo().frame("frameName");
driver.switchTo().defaultContent();

// Windows/Tabs
String parentWindow = driver.getWindowHandle();
for (String handle : driver.getWindowHandles()) {
    driver.switchTo().window(handle);
}
driver.switchTo().window(parentWindow);
```

##### Actions Class - Mouse & Keyboard
```java
Actions actions = new Actions(driver);
actions.moveToElement(menuElement).perform();
actions.dragAndDrop(source, target).perform();
actions.keyDown(Keys.CONTROL).sendKeys("a").keyUp(Keys.CONTROL).perform();
```

##### Assertions (TestNG)
```java
Assert.assertEquals(actual, expected);
Assert.assertTrue(condition);
Assert.assertFalse(condition);
Assert.assertNotNull(object);
```

### TestNG Framework Basics

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

##### Test Attributes
```java
@Test(priority = 1)
@Test(groups = {"smoke"})
@Test(dependsOnMethods = {"login"})
@Test(enabled = false)
```

##### Parameterization
```java
// From testng.xml
@Parameters({"browser"})
@Test
public void launchBrowser(String browser) { ... }

// From a DataProvider
@DataProvider(name = "loginData")
public Object[][] loginData() {
    return new Object[][] {
        {"user1", "pass1"},
        {"user2", "pass2"}
    };
}

@Test(dataProvider = "loginData")
public void login(String username, String password) { ... }
```

### Creating a Test Suite (`testng.xml`)
*Groups test classes together and controls how they're executed*

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
    <parameter name="browser" value="chrome"/>
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
    <test name="ChromeTests">
        <parameter name="browser" value="chrome"/>
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
- `parallel` can be `methods`, `classes`, `tests`, or `instances`

##### Separate Suite Files
- Keep different suites for different purposes, e.g:
	- `smoke.xml` - critical path tests only
	- `regression.xml` - full test coverage
	- `sanity.xml` - quick checks after a build

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
| Goal                                   | Command                                          |
| --------------------------------------- | ------------------------------------------------- |
| Run all tests (Maven)                   | `mvn test`                                        |
| Run a single class                      | `mvn test -Dtest=LoginTest`                       |
| Run a single method                     | `mvn test -Dtest=LoginTest#validLogin`            |
| Run a specific suite file                | `mvn test -DsuiteXmlFile=smoke.xml`               |
| Run by group                            | `mvn test -Dgroups=smoke`                         |
| Run suite without Maven                 | `java -cp "lib/*;bin" org.testng.TestNG smoke.xml`|
