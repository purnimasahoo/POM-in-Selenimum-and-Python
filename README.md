# POM-in-Selenimum-and-Python
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
