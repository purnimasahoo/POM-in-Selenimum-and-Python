# POM-in-Selenium-and-Python
page-object-model-in-selenium-python

Page Object Model (POM) is a design pattern in Selenium, and has the following benefits:

1. Reduce code duplication.
2. Easy to maintain code and update any code changes.
3. POM provides an efficient object repository to store and manage web elements.
4. Makes the code more modular since the locators/elements used by test suites/scenarios are stored in separate class files.
5. The test cases which has the core test logic are stored in different class files.
6. Any changes in the web UI elements will require minimum changes since locators, elements and test scripts are stored separately.

Project-Directory
     |--------- Src
                    |--------- PageObject
                    
                                       |--------- Pages
                                       
                                                    |--------- *Page.py (Implementation of methods that make use of the respective Locators declared in Locators.py)
                                                    
                                       |--------- Locators.py
                                       
                    |--------- TestBase
                    
                                       |--------- WebDriverSetup.py
                                       
     |--------- Test
     
                    |--------- Scripts
                    
                                       |--------- test_*.py (Implementation of test code)(There should be 1:1 mapping of *Page.py and test_*.py as it helps in making the code more modular)

                                       
                    |--------- TestSuite
                    
                                       |--------- TestRunner.py (contains TestSuite, which is a collection of test cases)

# Discuss your experience with development using Selenium APIs.

1. The Page Object Model (POM) is a design pattern used to create an object repository for web UI elements in test automation. It promotes code reusability, maintainability, and reduces code duplication by encapsulating the functionalities and locators of web elements in separate classes or pages.

2. Use locator strategies such as XPath, CSSSelectors to uniquely identify web element of the page.

   import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;

public class LoginPage {
    
    private WebDriver driver;

    // Locators using @FindBy annotation
    @FindBy(id = "username")
    private WebElement usernameTextBox;

    @FindBy(xpath = "//input[@id='username']")
    private WebElement usernameTextBox;
    
    @FindBy(id = "password")
    private WebElement passwordTextBox;

    @FindBy(css = "input#password")
   private WebElement passwordTextBox;

    @FindBy(id = "loginBtn")
    private WebElement loginButton;

    @FindBy(xpath = "//button[@id='loginBtn']")
   private WebElement loginButton;

    // Constructor to initialize WebDriver and PageFactory
    public LoginPage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    // Method to set username
    public void setUsername(String username) {
        usernameTextBox.sendKeys(username);
    }

    // Method to set password
    public void setPassword(String password) {
        passwordTextBox.sendKeys(password);
    }

    // Method to click on login button
    public void clickLoginButton() {
        loginButton.click();
    }

    // Method to perform login
    public void performLogin(String username, String password) {
        setUsername(username);
        setPassword(password);
        clickLoginButton();
    }
}

4. You can utilize Selenium Grid with Docker to execute your tests on different browsers and operating systems simultaneously. Below is an example that demonstrates how to integrate Selenium Grid with Docker and read the desired capabilities from a configuration file:

   docker-compose.yml

   version: '3'
services:
  selenium-hub:
    image: selenium/hub:latest
    ports:
      - "4444:4444"
  
  chrome-node:
    image: selenium/node-chrome:latest
    depends_on:
      - selenium-hub
    environment:
      - HUB_HOST=selenium-hub
      - HUB_PORT=4444
    volumes:
      - /dev/shm:/dev/shm

  firefox-node:
    image: selenium/node-firefox:latest
    depends_on:
      - selenium-hub
    environment:
      - HUB_HOST=selenium-hub
      - HUB_PORT=4444
    volumes:
      - /dev/shm:/dev/shm

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;

import java.io.FileInputStream;
import java.io.IOException;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.Properties;

public class LoginTest {
    
    private WebDriver driver;
    private LoginPage loginPage;

    @BeforeClass
    public void setUp() throws MalformedURLException {
        // Read configuration from config.properties file
        Properties prop = new Properties();
        try {
            FileInputStream fis = new FileInputStream("config.properties");
            prop.load(fis);
        } catch (IOException e) {
            e.printStackTrace();
        }

        String browser = prop.getProperty("browser");
        String os = prop.getProperty("os");

        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setBrowserName(browser);

        // Set additional capabilities based on OS
        if ("linux".equalsIgnoreCase(os)) {
            capabilities.setCapability("platform", "Linux");
        } else if ("windows".equalsIgnoreCase(os)) {
            capabilities.setCapability("platform", "Windows");
        } else if ("mac".equalsIgnoreCase(os)) {
            capabilities.setCapability("platform", "macOS");
        }

        // Initialize Remote WebDriver with Selenium Grid
        driver = new RemoteWebDriver(new URL("http://localhost:4444/wd/hub"), capabilities);
        driver.get("https://example.com/login");
        loginPage = new LoginPage(driver);
    }

    @Test
    public void testValidLogin() {
        loginPage.performLogin("exampleUser", "password123");
        // Add assertions and validations here
    }

    @AfterClass
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
The test will run against the Selenium Grid nodes configured in the Docker Compose file, enabling parallel execution across different environments.

