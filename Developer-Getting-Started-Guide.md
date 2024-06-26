In order to download necessary tools, clone the repository, and install dependencies via `yarn` you need network access.

You'll need the following tools:

- [Git](https://git-scm.com)
- [Node.JS](https://nodejs.org/en/), **x64**, version `>=18.15.x and <19`
- [Yarn 1](https://classic.yarnpkg.com/en/), follow the [installation guide](https://classic.yarnpkg.com/en/docs/install)
- [Python v3.11](https://www.python.org/downloads/) (required for node-gyp; check the [node-gyp readme](https://github.com/nodejs/node-gyp#installation) for the currently supported Python versions)
  - NOTE: Due to [deprecation of `distutils`](https://peps.python.org/pep-0632/), Python v3.12 is currently not supported with current version of NodeJS and node-gyp.
- A C/C++ compiler tool chain for your platform:
  - **Windows 10/11**
    - Install the Windows Build Tools:
      - if you install Node on your system using the Node installer from the [Node.JS](https://nodejs.org/en/download/) page then ensure that you have installed the 'Tools for Native Modules'. Everything should work out of the box then.
      - if you use a node version manager like [nvm](https://github.com/coreybutler/nvm-windows) or [nvs](https://github.com/jasongin/nvs) then follow these steps:
        - Install the current version of Python using the [Microsoft Store Package](https://docs.python.org/3/using/windows.html#the-microsoft-store-package).
        - Install the Visual C++ Build Environment by either installing the [Visual Studio Build Tools](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools) or the [Visual Studio Community Edition](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community). The minimum workload to install is 'Desktop Development with C++'.
        - open a command prompt and run npm config set msvs_version {visual studio version}. (If you are using Visual Studio 2019 then you need to run npm config set msvs_version 2019)
    - **Warning:** Make sure your profile path only contains ASCII letters, e.g. *John*, otherwise it can lead to [node-gyp usage problems (nodejs/node-gyp/issues#297)](https://github.com/nodejs/node-gyp/issues/297)
    - **Note**: Building and debugging via the Windows subsystem for Linux (WSL) is currently not supported.

  - **macOS**
    - [Xcode](https://developer.apple.com/xcode/downloads/) and the Command Line Tools, which will install `gcc` and the related toolchain containing `make`
      - Run `xcode-select --install` to install the Command Line Tools
  - **Linux**
    * On Debian-based Linux: `sudo apt-get install build-essential g++ libx11-dev libxkbfile-dev libsecret-1-dev python-is-python3 libkrb5-dev`
    * On Red Hat-based Linux: `sudo yum groupinstall "Development Tools" && sudo yum install libX11-devel.x86_64 libxkbfile-devel.x86_64 libsecret-devel # or .i686`.
    * Others:
      * `make`
      * [pkg-config](https://www.freedesktop.org/wiki/Software/pkg-config/)
      * [GCC](https://gcc.gnu.org) or another compile toolchain
      * Dependencies:
    * Building deb and rpm packages requires `fakeroot` and `rpm`, run: `sudo apt-get install fakeroot rpm`


### Troubleshooting
In case of issues, here are some things to try. If the issue still persists, refer to https://github.com/microsoft/vscode/wiki/How-to-Contribute, the tools requirements might have changed, please update the requirements on this page accordingly after your issue is resolved.

1. Delete the contents of `~/.node-gyp` (alternatively `~/.cache/node-gyp` for Linux, `~/Library/Caches/node-gyp/` for macOS or `%USERPROFILE%\AppData\Local\node-gyp` for Windows) first and then run `yarn cache clean` and then try again.

    * On Windows, if you cannot find the AppData folder, go to the File Explorer and enable View > Sort > Hidden items.

1. If getting `/bin/sh: /usr/bin/python: No such file or directory` or other similar error

   Run `yarn config set python <path to python> -g` (see [node-gyp README](https://github.com/nodejs/node-gyp#installation) for details on supported versions of Python

1. Ensure you have the latest version of npm installed, run `npm update -g` to get the latest. If you have multiple versions of npm installed uninstall any older ones to ensure that node-gyp is using the correct one.

   Errors that were because of outdated npm : 

   ```
   error <PATH TO ENLISTMENT>/azuredatastudio/node_modules/keytar: Command failed.
   Exit code: 1
   Command: prebuild-install || npm run build
   ```
1. If getting error similar to `An unexpected error occurred: \"Cannot create property 'python27-python-exe' on string '{\\\"python27-python-exe\\\":true}'\"."}`

   Likely an issue with bad values in your `.yarnrc` file (`%USERPROFILE%/.yarnrc or `~/.yarnrc`). See https://github.com/yarnpkg/yarn/issues/8644 for ways to fix that. 

1. Uninstall and reinstall any Visual Studio installations you have, making sure to select `Desktop Development with C++` when reinstalling. 

> If you are on Windows or Linux 64 bit systems and would like to compile to 32 bit, you'll need to set the `npm_config_arch` environment variable to `ia32` before running `yarn`. This will compile all native node modules for a 32 bit architecture.

> **Note:** For more information on how to install NPM modules globally on UNIX systems without resorting to `sudo`, refer to [this guide](http://www.johnpapa.net/how-to-use-npm-global-without-sudo-on-osx/).

> If you have Visual Studio 2019 installed, you may face issues when using the default version of node-gyp. If you have Visual Studio 2019 installed, you may need to follow the solutions [here](https://github.com/nodejs/node-gyp/issues/1747)

# Recommended Tools

- [VSCode](https://code.visualstudio.com/) We have multiple integrations with VSCode's task system so it's highly recommended to use that as your Editor/IDE.

# Source Code

## Getting Authenticated

If you're a new Microsoft employee, you will need to connect your Github account to the Microsoft org. Follow the instructions at https://docs.opensource.microsoft.com/tools/github/accounts/linking.html and join the **Microsoft** org. 

If you're an outside contributor, simply fork the codebase and make a PR against ours with your changes.

## Compiling

```bash
git clone https://github.com/microsoft/azuredatastudio
cd azuredatastudio
yarn
yarn run watch
```
**Note:** When you run 'yarn' if you hit the error *gyp ERR! stack Error: spawn C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\15.0\Bin\MSBuild.exe ENOENT*, change the config to use version 2015:
```
npm config set msvs_version 2015
```

## Running

**VSCode**

Go to debugging -> Launch Azure Data Studio

This will run Azure Data Studio from the source enlistment, after which you can use the `Attach` targets to attach to the process for debugging.

  <img src='./media/launch_ads.PNG'>

There are multiple categories of launch targets, grouped by their functionality. 

* `Launch Azure Data Studio` - Run Azure Data Studio from sources
* `Launch ADS & Debug ...` - Run Azure Data Studio from sources and attach to the specified processes
* `Attach to ...` - Attaches the debugger to a currently running Azure Data Studio process. Note that the debugger ports must be opened on this process for this to work (off by default for non-dev versions)
* `Run ... Tests` - Runs the specified set of tests
* `Debug ... Tests` - Runs and attaches the debugger for the specified set of tests


**OS X and Linux**

```bash
./scripts/sql.sh
```

**Windows**

```bash
.\scripts\sql.bat
```

## Creating optimized builds

This step isn't necessary for development, but if you want to create a build with optimized code for production. Essentially a "release" quality build:

```batch
REM for windows
yarn run gulp vscode-win32-x64
cd ../azureadatastudio-windows-x64
```

```bash
# for macOS
yarn run gulp vscode-darwin
cd ../azureadatastudio-darwin

# for linux
yarn run gulp vscode-linux-x64
cd ../azureadatastudio-linux-x64
```

## Creating extension VSIX manually

Sometimes you may want to generate the VSIX for an extension manually. This is useful if you want to verify that the built VSIX works as expected or want to give it to someone else to try.

There are a couple ways to achieve this : 

1. For MS devs only - use the Ad Hoc pipeline and queue a build against your branch. This will generate the VSIX's and add them as artifacts to the build. Note that this does take a while, but it's also the cleanest (no chance of "extra" files being accidently added from your local enlistment)
2. Run `vsce package` from the extension directory (i.e. `extensions/<name>`). This will only work by default for non-webpacked extensions. If the extension folder has an `extension.webpack.config.js` file in the root then the VSIX that is generated will not contain the compiled files so will not work. You can work around this by deleting the `out/**` entry from the `.vscodeignore` file in the same directory and then re-running the command.
3. Run these commands
```bash
yarn gulp package-rebuild-extensions
yarn gulp compile-extensions
yarn gulp package-external-extensions
```
This will result in the extension VSIX's being generated in the `.build` folder in the root of the enlistment. Note that you may get an error similar to `Error: EMFILE: too many open files...`. If this happens then edit [extensions.js](https://github.com/microsoft/azuredatastudio/blob/main/build/lib/extensions.js#L205) and comment out the extensions that you don't care about bundling, then re-run the command. 

## VS Code extension packages

For some extensions in the Azure Data Studio repo we want to create a separate package that is able to run in VS Code as well as ADS. Some extensions may be able to do this without any changes - for example if you don't use any of the "azdata" extension API. But for most extensions they will need to have changes made to be able to run in VS Code.

In particular the package.json for such extensions will usually need some changes - such as modifying the `dependentExtensions` field to reference other extensions running in VS Code (e.g. `ms-mssql.mssql`).

To make this easier and reduce duplication a step was added to the extension packaging step that will do the necessary replacements in the package.json for a given extension and then rebuild the extension to produce a new package.

This step is ran for any extensions that have a file named `package.vscode.json` in the root of the extension folder - next to the `package.json`. For externally packaged extensions (not built-in to Azure Data Studio) this is all that's needed. But for built-in extensions the extension name should be added to [vsCodeExternalExtensions](https://github.com/microsoft/azuredatastudio/blob/main/build/lib/extensions.ts#L274), ONLY extensions that are built-in to ADS but need to be packaged externally for VS Code should be added to this list.

The `package.vscode.json` file contains the properties that will be replacing the corresponding properties in the original `package.json`. This can be any property in the `package.json` and it will replace the entire property with the new value - nested property replacement is not supported at this time.

[package.vscode.json example](https://github.com/microsoft/azuredatastudio/blob/main/extensions/data-workspace/package.vscode.json)


## Cleaning the repo

Sometimes we have breaking changes that require the entire code to be rebuilt. For example, if we upgrade the electron version of the code, everyone's local development environment will break. To handle this scenario, we do a git clean. This restores the locally cloned repository to its original form:

```bash
git clean -fxd
```

Additionally sometimes we need to clean the cache manually which is located at C:\Users\{userName}\AppData\Roaming\AzureDataStudio. By doing this all saved servers and their logins will be cleared.

