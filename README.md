# Github Actions & Workflows (BLUI)
## Build and Test with Node.js
### See the official doc [here](https://docs.github.com/en/actions/use-cases-and-examples/building-and-testing/building-and-testing-nodejs).

This repository contains the Brightlayer-UI React Routing template starter project and is the starting point for experimenting with basic Github Actions & Workflows. For this projects continuous integration (CI) we will focus only on the actions / workflow and the available scripts in package.json.

There are two ways to implement the (CI) .yml file for any project in github, the first way is to create and edit the workflow .yml file in the actions tab in the repository and the second way is to commit & push the .yml file. For this we will just commit the .yml in to your new repository via a pull request and the workflow will be triggered to run once everything is setup.

-   Clone this repository and <ins>create a new repository under your github profile.</ins>
```
git clone https://github.com/JeffGreiner-eaton/react-routing-action-workflow.git

```
-   Clone down your new repository that was created and in the root of the project create a hidden folder `.github`. After creating the .github folder create a sub-folder in the .github called `workflows`.

-   Create a new file in workflows folder called `mybuild.yml` or similar.

![github-folder](./images/github-folder.png)

-   To help author the .yml you can install the VS Code extension GitHub Actions v0.26.X
-   In the new .yml file, the first line will contain the workflow name that will be displayed in the github repository actions once the first workflow runs. On the first line of the .yml add ``` name: Build ```. The name value can be anything to represent what the workflow is.

![workflow-name](./images/workflow-name.png)

-   In the next section of the .yml workflow file we will setup the `on:` trigger events that will trigger the workflow to run. For Brightlayer-UI we tend to use three event types:
` on: push:` ` on: pull_request:` ` on: pull_request_target:`

-   On line 3 add the `on:` key and event types.
```yaml
on:
  push:
    branches: [ "dev", "master" ]
  pull_request:
    branches: [ "dev", "master" ]
  pull_request_target:
    types:
      - opened
    branches:
      - '*/*'
```

See the official docs [here](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#on) for additional event types.

-   The workflow .yml file is now setup with run name and on: trigger event types.

![triggers](./images/triggers.png)

-   In the next section of the workflow, assigning permissions for jobs. You can use permissions to modify the default permissions granted by Github and you can use permissions either as a top-level key, to apply to all jobs in the workflow, or within specific jobs. For Brightlayer-UI we tend to use this permission setup for the `on:` key event type `pull_request_target` and permits forked pull requests to run our workflow and jobs.

-   On line 14 add the `permissions:` key and the types.
```yaml
permissions:
  pull-requests: write
  contents: read
```

See the official docs [here](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/assigning-permissions-to-jobs) for additional permission types.

![permissions](./images/permissions.png)

-   The last section of the .yml workflow file is the jobs section. This section is where we define what actions will run when the workflow is triggered. For this repository the jobs section will contain the checkout action, setup-node action and the commands to run prettier, lint, build and test that are referenced in package.json.

- On line 18 add the `jobs:` key and then the job id `prettier_lint:` with `runs-on:` key using ubuntu-latest.

```yaml
jobs:
  prettier_lint:
    runs-on: ubuntu-latest
```

-   The `runs-on:` key with image runner `ubuntu-latest` is used by GitHub Actions and is the hosted virtual environment offering a quicker, simpler way to run your workflows. <ins>Self-hosted</ins> runners are highly configurable way to run workflows in your own custom environment that would have to be maintained by the team.

See the available hosted runners [here](https://github.com/actions/runner-images)

-   In the jobs section, continue on line 22 and add the `strategy:` id key with the `matrix:` key using `node-version`. The matrix strategy lets you use variables in a single job definition to automatically create multiple job runs that are based on the combinations of the variables. In this setup we use node-version 18.X and you can add other versions of node that will trigger multiple runs. Example `node-version: [16.x, 18.x, 20.x]`

See the official doc on matrix strategy [here](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#jobsjob_idstrategy).

```yaml
jobs:
  prettier_lint:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x]
```

![strategy](./images/strategy.png)

-   In the jobs section, continue on line 26 with the `steps:` key with `name:` id step name. This is the section where we define what the job will execute and using the `actions/checkout@v4` Github action that checks-out the repository under $GITHUB_WORKSPACE, so the workflow can be accessed. The `uses:` key id is the location and version of a reusable workflow file to run as the job.

```yaml
    steps:
    - name: Checkout
      uses: actions/checkout@v4
```
![step-checkout](./images/step-checkout.png)

See the `actions/checkout` repository readme [here](https://github.com/actions/checkout) for more information.

See the uses key official docs [here](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#jobsjob_iduses).


-   In the jobs section, continue on line 29 and add another `name:` id step name with `uses:` key for Node.js and using the actions/setup-node@v4 with node version.

```yaml
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'yarn'
```
![step-setup](./images/step-setup.png)

See the `actions/setup-node` repository readme [here](https://github.com/actions/setup-node).

- The job steps setup section should look like the image above and now the job has been defined to use actions/checkout with actions/setup-node using the value from the matrix with cache of yarn. Cache yarn at the time of testing workflows seemed to speed job runs a bit but isn't always needed.

-   In the jobs section, continue on line 34 and add the `run:` key with yarn (install), line 35 add another `run:` key with yarn prettier and lastly add `run:` key with yarn lint.

```yaml
    - run: yarn
    - run: yarn prettier
    - run: yarn lint
```

See the `run` key docs [here](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#defaultsrun)

-   The prettier_lint job and steps are complete and should look like this.
![prettier-job](./images/prettier-job.png)

-   This is the opportunity to check the workflow and job runs in your repository. Open a new pull request in your new repository. Once your PR is open, select the actions tab in the repository. Verify your new workflow name (Build) is reflected in the left side-nav and select it. Verify the prettier_lint job is listed and complete. Double click the job folder to view the content of the run. Note: if the job failed, double click on it to see error.

![action-prettier-job](./images/action-prettier.png)

![prettier-flow](./images/prettier-flow.png)

     


# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `yarn start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.\
You will also see any lint errors in the console.

### `yarn test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `yarn build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.