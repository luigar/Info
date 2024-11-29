
# WebdriverIO Project Structure

A well-structured WebdriverIO (WDIO) project ensures maintainability, scalability, and readability. Below is the recommended structure for your WDIO test automation framework.

## Project Structure
```
.
├── tests/                      # Test-related files
│   ├── specs/                  # Test specifications (test scripts)
│   ├── pageobjects/            # Page Object Model files
│   ├── data/                   # Test data (e.g., JSON files)
│   ├── utils/                  # Utilities (e.g., helper functions)
├── reports/                    # Test reports and screenshots
│   ├── allure-results/         # Allure results directory
│   ├── screenshots/            # Screenshots for failed tests
├── wdio.conf.js                # WebdriverIO configuration file
├── package.json                # NPM dependencies and scripts
├── package-lock.json           # NPM lock file for dependencies
├── .gitignore                  # Git ignore rules
├── README.md                   # Project documentation
```

## Folder Details

### `tests/specs/`
Contains all test specifications (test scripts) grouped logically by features or modules.
```
tests/specs/
├── login.spec.js
├── dashboard.spec.js
├── userManagement/
    ├── createUser.spec.js
    ├── deleteUser.spec.js
```

### `tests/pageobjects/`
Implements the **Page Object Model (POM)**, where each page has its corresponding class with locators and reusable actions.
Example `login.page.js`:
```javascript
class LoginPage extends Page {
    /**
     * define selectors using getter methods
     */
    get inputUsername () {
        return $('#username');
    }

    get inputPassword () {
        return $('#password');
    }

    get btnSubmit () {
        return $('button[type="submit"]');
    }

    /**
     * a method to encapsule automation code to interact with the page
     * e.g. to login using username and password
     */
    async login (username, password) {
        await this.inputUsername.setValue(username);
        await this.inputPassword.setValue(password);
        await this.btnSubmit.click();
    }

    /**
     * overwrite specific options to adapt it to page object
     */
    open () {
        return super.open('login');
    }
}

export default new LoginPage();


module.exports = new LoginPage();
```

### `tests/data/`
Stores reusable test data such as user credentials, API endpoints, or configuration values.
```
tests/data/
├── users.json                # User data
├── config.json               # Test-specific configurations
```

### `tests/utils/`
Contains utility functions such as:
- Date/time formatting
- API calls
- Custom assertions
- Environment configuration
```
tests/utils/
├── helpers.js
├── api.js
├── logger.js
```

### `reports/`
Stores generated reports and screenshots.
```
reports/
├── allure-results/
├── screenshots/
```

## Configuration Files

### `wdio.conf.js`
The main WebdriverIO configuration file defines the framework setup, capabilities, reporters, and services.
Example:
```javascript
exports.config = {
    runner: 'local',
    specs: ['./tests/specs/**/*.spec.js'],
    maxInstances: 5,
    capabilities: [{
        browserName: 'chrome'
    }],
    logLevel: 'info',
    baseUrl: 'https://example.com',
    waitforTimeout: 10000,
    framework: 'mocha',
    reporters: ['spec', ['allure', { outputDir: 'reports/allure-results' }]],
    services: ['chromedriver']
};
```

## NPM Scripts
Define scripts in `package.json` for common tasks:

```json
"scripts": {
    "test": "wdio wdio.conf.js",
    "allure:generate": "allure generate reports/allure-results --clean",
    "allure:serve": "allure serve reports/allure-results"
}
```

### Run Commands
- `npm run test`: Run all tests.
- `npm run allure:serve`: Generate and open the Allure report.

## Setting Up Dependencies
Install required dependencies:
```
npm install @wdio/cli --save-dev
npx wdio config
```

### Recommended Dependencies
- `@wdio/local-runner`: Run tests locally.
- `@wdio/mocha-framework`: Mocha testing framework.
- `@wdio/spec-reporter`: Command-line test reporting.
- `@wdio/allure-reporter`: Allure reporting.
- `@wdio/chromedriver-service`: ChromeDriver service.
- `dotenv`: For managing environment variables.

## Additional Recommendations
- Use a `.env` file for sensitive data like API keys or URLs.
- Follow a consistent naming convention for files and folders.
- Use a linter (e.g., ESLint) to maintain code quality.
- Implement CI/CD pipelines with tools like GitHub Actions or Jenkins.
