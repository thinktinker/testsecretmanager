# cd-serverless
CD-serverless - updated on 15 Nov


#### 1. Make the first commit 
```shell
  git clone
  git add .
  git commit -m "commit message"
  git push
```
#### 2. To initialize node for the working directory (incl. package.json), run the following command:
```shell
  npm init
```
#### 3. Install serverless and serverless-offline after #2
#### Note: To reduce deprecation issues, switch to a more recent node.js version 20 via node version manager
```shell
  npm install serverless
  npm install serverless-offline --save-dev
```
#### 4. Add the following files for the following reasons:
- index.js (api endpoint that returns a message)
- serverless.yml (listens for the 'get' event and calls index.js handler to handle the request)
- .gitignore (include node_modules/ and .serverless so these folders and it contents are not pushed to git hub)

#### 5. To test the serverless application, use the installed plugin serverless-offline via the following command:
#### Note: Ensure that, in serverless.yml, the frameworkVersion's value is wrapped between single quote '...'
```shell
  sls offline start
```
#### 6. Test the serverless package via the following command (this checks if the app is ready to be deployed):
```shell
  serverless package
```
#### 7. Deploy the serverless service to AWS Lamba via the following commend:
```shell
  sudo serverless deploy
```
#### 8. Check that the deployment is succesfully deployed to Lambda based via AWS console:
a. In AWS Console
- Cloud Formation > Se the infrastructure
- AWS Lamda > deployed serverless application

b. In AWS Lamda:
- Function
- Application Name
- Configuration
- Triggers
- API Endpoint
#### 9. Create a .github/workflows folder locally and create new file in it called **main.yml**
#### Note: Update main.yml to run the jobs in github actions (to pre-deploy the serverless app and install additional dependencies thereafter)
```yml
name: CICD for Serverless Application
run-name: ${{ github.actor }} is doing CICD for serverless application

on:
  push:
    branches: [ main, "*"]

jobs:
  pre-deploy:
    runs-on: ubuntu-latest
    steps:
      - run: echo "🎉 The job was automatically triggered by a ${{ github.event_name }} event"
      - run: echo "🐧 This job is now running on a ${{ runner.os }} server hosted by GitHub!"
      - run: echo "🔎 The name of your branch is ${{ github.ref }} and your repository is ${{ github.repository }}."

  install-dependencies:
    runs-on: ubuntu-latest
    needs: pre-deploy
    steps:
      - name: Check out repository code
        uses: actions/checkout@v3
      - name: Run Installation of Dependencies Commands
        run: npm install
```
#### 10. Append main.yml with the github command actions to deploy the solution as well:
```yml
name: CICD for Serverless Application
run-name: ${{ github.actor }} is doing CICD for serverless application

on:
  push:
    branches: [ main, "*"]

jobs:
  pre-deploy:
    runs-on: ubuntu-latest
    steps:
      - run: echo "🎉 The job was automatically triggered by a ${{ github.event_name }} event"
      - run: echo "🐧 This job is now running on a ${{ runner.os }} server hosted by GitHub!"
      - run: echo "🔎 The name of your branch is ${{ github.ref }} and your repository is ${{ github.repository }}."

  install-dependencies:
    runs-on: ubuntu-latest
    needs: pre-deploy
    steps:
      - name: Check out repository code
        uses: actions/checkout@v3
      - name: Run Installation of Dependencies Commands
        run: npm install

  deploy:
    name: deploy
    runs-on: ubuntu-latest
    needs: install-dependencies
    strategy:
      matrix:
        node-version: [18.x]
    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
    - run: npm ci
    - name: serverless deploy
      uses: serverless/github-action@v3.2
      with:
        args: deploy
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```
#### 11. When ready, remove the provisioned serverless services on AWS by run the following locally: 
#### NOTE: The following command should ONLY be run if the services is no longer required on AWS.
```shell
  serverless romove
```
#### 12. Additional references for further reading:

- [https://docs.github.com/en/actions](https://docs.github.com/en/actions)
- [https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-nodejs](https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-nodejs)


