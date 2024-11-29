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
   
      - name: Run Full Test Suite
        if: github.event_name == 'push' && github.ref == 'refs/heads/master'
        run: |
          source .env
          npm run wdio
```
