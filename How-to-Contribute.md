# Contributing to Azure Data Studio
There are many ways to contribute to the Azure Data Studio project: logging bugs, submitting pull requests, reporting issues, and creating suggestions.

## Build and Run From Source

If you want to understand how Azure Data Studio works or want to debug an issue, you'll want to get the source, build it, and run the tool locally.

### Getting the sources

```
git clone https://github.com/Microsoft/azuredatastudio.git
```

### Installing Prerequisites

- [Git](https://git-scm.com)
- [Node.JS](https://nodejs.org/en/), `>= 8.12.0, < 9.0.0`
- [Yarn](https://yarnpkg.com/en/), follow the [installation guide](https://yarnpkg.com/en/docs/install)
- [Python](https://www.python.org/downloads/), at least version 2.7 (version 3 is __*not*__ supported)
- C/C++ compiler tool chain
  - **Windows**
    - Set a `PYTHON` environment variable pointing to your `python.exe`. Eg: `C:\Python27\python.exe`
    - [Visual Studio 2013 for Windows Desktop](https://www.visualstudio.com/en-us/news/vs2013-community-vs.aspx?wt.mc_id=github_microsoft_vscode) or [Visual Studio 2015](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx?wt.mc_id=github_microsoft_vscode), make sure to select the option to install all C++ tools and the Windows SDK.
    - You can also use Felix Rieseberg's [Windows Build Tools npm module](https://github.com/felixrieseberg/windows-build-tools) instead of Visual Studio. The `--debug` flag is helpful if you encounter any problems during installation
    - Please note that building and debugging via the Windows subsystem for Linux (WSL) is currently not supported.
  - **OS X**
    - [Xcode](https://developer.apple.com/xcode/downloads/) and the Command Line Tools (Xcode -> Preferences -> Downloads), which will install `gcc` and the related toolchain containing `make`
  - **Linux**
    * `make`
    * [GCC](https://gcc.gnu.org) or another compile toolchain
    * [native-keymap](https://www.npmjs.com/package/native-keymap) needs `libx11-dev` and `libxkbfile-dev`.
      * On Debian-based Linux: `sudo apt-get install libx11-dev libxkbfile-dev`
      * On Red Hat-based Linux: `sudo yum install libX11-devel.x86_64 libxkbfile-devel.x86_64 # or .i686`.
    * [keytar](https://www.npmjs.com/package/keytar) needs `libsecret-1-dev`.
      * On Debian-based Linux: `sudo apt-get install libsecret-1-dev`.
      * On Red Hat-based Linux: `sudo yum install libsecret-devel`.
    * Building deb and rpm packages requires `fakeroot` and `rpm`, run: `sudo apt-get install fakeroot rpm`

Finally, install all dependencies using `Yarn`:

```
yarn
```

If you are on Windows or Linux 64 bit systems and would like to compile to 32 bits, you'll need to set the `npm_config_arch` environment to `ia32` before running `yarn`. This will compile all native node modules for a 32 bit architecture.

**Note:** For more information on how to install NPM modules globally on UNIX systems without resorting to `sudo`, refer to [this guide](http://www.johnpapa.net/how-to-use-npm-global-without-sudo-on-osx/).

### Build Azure Data Studio
After you have these tools installed, run the following commands to clone github repository, install dependencies, and compile source code:

```bash
git clone https://github.com/microsoft/azuredatastudio
cd azuredatastudio
yarn
yarn run watch
```

### Run Azure Data Studio in debug mode.

**OS X and Linux**

```bash
./scripts/sql.sh
```

**Windows**

```bash
.\scripts\sql.bat
```

### Packaging

> if you encounter too many files opened error on macOS, run sudo launchctl limit maxfiles 200000 200000 then retry. The default settings is sudo launchctl limit maxfiles 256 unlimited

```bash
# for macOS
gulp --max-old-space-size=8192 vscode-darwin
cd ../sqlops-darwin

# for windows
gulp –-max-old-space-size=8192 vscode-win32-x64
cd ../sqlops-windows-x64

# for linux
gulp –-max-old-space-size=8192 vscode-linux-x64
cd ../sqlops-linux-x64
```

### Clean up build, npm modules and reset your local git repository to a clean state

```bash
git clean -fxd
```

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