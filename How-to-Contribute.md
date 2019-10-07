# Contributing to Azure Data Studio
There are many ways to contribute to the Azure Data Studio project: logging bugs, submitting pull requests, reporting issues, and creating suggestions.

[Read the developer getting started guide here](https://github.com/microsoft/azuredatastudio/wiki/Developer-Getting-Started-Guide)

## Development Workflow

### Incremental Build
From a terminal, where you have cloned the `azuredatastudio` repository, execute the following command to run the TypeScript incremental builder:

```bash
yarn run watch
```

It will do an initial full build and then watch for file changes, compiling those changes incrementally, enabling a fast, iterative coding experience.


### Debugging
You can either use VS Code or the Chrome Developer Tools to debug Azure Data Studio.

#### Using VSCode
* Install the [Debugger for Chrome](https://marketplace.visualstudio.com/items/msjsdiag.debugger-for-chrome) extension. This extension will let you attach to and debug client side code running in Chrome.
* Open the `azuredatastudio` repository folder
* Choose the `Launch azuredatastudio` launch configuration from the launch dropdown in the Debug viewlet and press `F5`.


#### Using the Chrome Developer Tools

* Run the `Developer: Toggle Developer Tools` command from the Command Palette in your development instance of Azure Data Studio to launch the Chrome tools.
* It's also possible to debug the released versions of Azure Data Studio, since the sources link to sourcemaps hosted online.

  <img src='./media/chrome_developer_tools.png'>

### Automated Testing
Run the unit tests directly from a terminal by running `./scripts/test.sh` from the `azuredatastudio` folder (`scripts\test` on Windows). The [test README](https://github.com/Microsoft/azuredatastudio/blob/master/test/README.md) has complete details on how to run and debug tests, as well as how to produce coverage reports.

Run the integration tests directly from a terminal by running `./scripts/sql-test-integration.sh` from the `azuredatastudio` folder (`scripts\sql-test-integration.bat` on Windows).
To debug the integration tests, run the script mentioned above and then attach to the extension host.

(Windows only for now) Run the smoke tests directly from a terminal by running `node test\index.js` from the `azuredatastudio\test\smoke\` folder.

To debug the smoke test, in VSCode, select `Launch Smoke Test` from the launch option.

### Linting
We use [tslint](https://github.com/palantir/tslint) for linting our sources. You can run tslint across the sources by calling `gulp tslint` from a terminal or command prompt.

To lint the source as you make changes you can install the [tslint extension](https://marketplace.visualstudio.com/items/eg2.tslint). 

## Work Branches
Even if you have push rights on the Microsoft/azuredatastudio repository, you should create a personal fork and create feature branches there when you need them. This keeps the main repository clean and your personal workflow cruft out of sight.

## Pull Requests
Before we can accept a pull request from you, you'll need to sign a [[Contributor License Agreement (CLA)|Contributor-License-Agreement]]. It is an automated process and you only need to do it once.

To enable us to quickly review and accept your pull requests, always create one pull request per issue and [link the issue in the pull request](https://github.com/blog/957-introducing-issue-mentions). Never merge multiple requests in one unless they have the same root cause. Be sure to follow our coding guidelines and keep code changes as small as possible. Avoid pure formatting changes to code that has not been modified otherwise. Pull requests should contain tests whenever possible. 

### Where to Contribute
Check out the [full issues list](https://github.com/Microsoft/azuredatastudio/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue) for a list of all potential areas for contributions. 

To improve the chances to get a pull request merged you should select an issue that is labelled with the [`help-wanted`](https://github.com/Microsoft/sqlopsstudio/issues?q=is%3Aopen+is%3Aissue+label%3A%22help+wanted%22) or [`bug`](https://github.com/Microsoft/sqlopsstudio/issues?q=is%3Aopen+is%3Aissue+label%3A%22bug%22) labels. If the issue you want to work on is not labelled with `help-wanted` or `bug`, you can start a conversation with the issue owner asking whether an external contribution will be considered.

## Suggestions
We're also interested in your feedback for the future of Azure Data Studio. You can submit a suggestion or feature request through the issue tracker. To make this process more effective, we're asking that these include more information to help define them more clearly. 

## Discussion Etiquette

In order to keep the conversation clear and transparent, please limit discussion to English and keep things on topic with the issue. Be considerate to others and try to be courteous and professional at all times.

## Microsoft Open Source Code of Conduct
This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.