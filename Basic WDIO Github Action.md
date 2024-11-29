# Basic Structure 
This will run an action when a push or a pr request have been submitted. 
Taking account that the project has a .env file and the node version has been added to the package json.

```json
"engines": {
    "node": ">= 20.11.0"
```

Also your wdio.conf needs to have set the browser to run headless

```js
    capabilities: [{
        browserName: 'chrome',
        'goog:chromeOptions': {
            args: ['headless=new', 'disable-gpu']
        }
```


# Github Action


```yaml
name: Node.js CI

on:
  push:
    branches: ["master"]
  pull_request:
    branches: ["master"]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Extract Node.js version from package.json
        id: node-version
        run: echo "NODE_VERSION=$(jq -r .engines.node < package.json)" >> $GITHUB_ENV

      - name: Use Node.js ${{ env.NODE_VERSION }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: "npm"
          
      - run: node -v
      
      - run: npm install

      - name: Run Lint
        run: npm run lint

      - name: Run Smoke Tests
        if: github.event_name == 'pull_request'
        run: |
          source .env
          npm run test:smoke
          npm run test -- --suite login
          
      - name: Run Full Test Suite
        if: github.event_name == 'push' && github.ref == 'refs/heads/master'
        run: |
          source .env
          npm run wdio

      - name: Archive Allure Results
        if: always()  # Ensure this step runs even if previous steps fail
        uses: actions/upload-artifact@v2
        with:
          name: allure-results
          path: allure-results
```

# Explanation of the Github Action step by step

This workflow automates the Continuous Integration (CI) process for a Node.js project. Below is a detailed breakdown of the workflow.

---

## Workflow Name
```yaml
name: Node.js CI
```
- The workflow is named **"Node.js CI"**. It will appear in the **Actions tab** of your GitHub repository.

---

## Trigger Events
```yaml
on:
  push:
    branches: ["master"]
  pull_request:
    branches: ["master"]
```
- The workflow is triggered on:
  - A `push` to the `master` branch.
  - A `pull_request` targeting the `master` branch.

---

## Jobs

### Build Job
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```
- The job name is **`build`**.
- It runs on the latest Ubuntu virtual environment provided by GitHub.

---

### Steps in the Build Job

#### 1. Checkout Repository
```yaml
- uses: actions/checkout@v3
```
- This step checks out the repository’s code for subsequent steps.

---

#### 2. Extract Node.js Version
```yaml
- name: Extract Node.js version from package.json
  id: node-version
  run: echo "NODE_VERSION=$(jq -r .engines.node < package.json)" >> $GITHUB_ENV
```
- Reads the required Node.js version from the `engines.node` field in `package.json` using `jq`.
- Saves the version to an environment variable `NODE_VERSION`.

---

#### 3. Setup Node.js Environment
```yaml
- name: Use Node.js ${{ env.NODE_VERSION }}
  uses: actions/setup-node@v3
  with:
    node-version: ${{ env.NODE_VERSION }}
    cache: "npm"
```
- Sets up the Node.js environment using the extracted version.
- Enables dependency caching to speed up subsequent runs.

---

#### 4. Print Node.js Version
```yaml
- run: node -v
```
- Verifies the Node.js version by printing it to the console.

---

#### 5. Install Dependencies
```yaml
- run: npm install
```
- Installs project dependencies using `npm`.

---

#### 6. Run Linting
```yaml
- name: Run Lint
  run: npm run lint
```
- Executes the linting process to check code quality.

---

#### 7. Run Smoke Tests
```yaml
- name: Run Smoke Tests
  if: github.event_name == 'pull_request'
  run: |
    source .env
    npm run test:smoke
    npm run test -- --suite login
```
- **Conditional Execution**: Runs only for `pull_request` events.
- Commands:
  - `source .env`: Loads environment variables.
  - `npm run test:smoke`: Runs Smoke tests.
  - `npm run test -- --suite login`: Runs smoke tests for the login suite.

---

#### 8. Run Test Suite
```yaml
- name: Run Full Test Suite
  if: github.event_name == 'push' && github.ref == 'refs/heads/master'
  run: |
    source .env
    npm run wdio
```
- **Conditional Execution**: Runs only for:
  - A `push` event.
  - The `master` branch.
- Commands:
  - `source .env`: Loads environment variables.
  - `npm run wdio`: Runs the WebdriverIO test suite

---

#### 9. Archive Allure Results
```yaml
- name: Archive Allure Results
  if: always()  # Ensure this step runs even if previous steps fail
  uses: actions/upload-artifact@v2
  with:
    name: allure-results
    path: allure-results
```
- Archives the Allure results as a GitHub artifact for review.
- **Conditional Execution**: Runs regardless of success or failure of previous steps.

---

## Summary
This workflow ensures that:
1. Code changes to the `master` branch or pull requests automatically trigger CI.
2. It uses the Node.js version specified in `package.json` for consistency.
3. Linting, smoke tests, and full test suites are executed based on the event type.
4. Allure results are archived for further analysis.
