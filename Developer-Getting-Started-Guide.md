# Pre-Requisites:
- ## [Git](https://git-scm.com)
- ## Node Version Manager
  - [Windows](https://github.com/coreybutler/nvm-windows)
  - [Linux and Mac](https://github.com/nvm-sh/nvm)
- ## Yarn: 
   - Install after you have node version manager with `npm install -g yarn` - we go over this [here](#setting-up-nodejs--yarn) as well
- ## [Python](https://www.python.org/downloads/) anything between 2.7 and 3.0 (version 3 is __*not*__ supported) 
   - ### Linux Notes:
      - on a brand new linux vm, [C/C++ compiler may need to be installed before Python can be installed](#linux-please-note-we-do-not-support-the-windows-subsystem-for-linux). So install those first and then Python.
      - [instructions]
          - On Debian-based Linux:`sudo apt install python`
          or
          - (https://tecadmin.net/install-python-2-7-on-ubuntu-and-linuxmint/)
- ## C/C++ compiler tool chain
  - ### **Windows**
    - Set a `PYTHON` environment variable pointing to your `python.exe`. E.g.: `C:\Python27\python.exe`
	- Install a compiler for the native modules Azure Data Studio depends on
		- Option 1 (recommended): Use Windows Build Tools npm module
			- Start Powershell as Administrator and install [Windows Build Tools npm module](https://github.com/felixrieseberg/windows-build-tools) ([documentation](https://github.com/felixrieseberg/windows-build-tools#visual-studio-2017-vs-visual-studio-2015)).
				```
				npm install --global windows-build-tools --vs2015
				```
				**Note:** The `--debug` flag is helpful if you encounter any problems during installation.

				**Note:** if you encounter an error *The build tools for v141 (Platform Toolset = 'v141') cannot be found."* you might have a version of Visual Studio installed. Either uninstall that version or make sure to have *VC++ 2015.3 v14.00 (v140) toolset for desktop* installed (see below)
		- Option 2: Use Visual Studio 2017 (This is recommended if you're joining the team)
			- Install [Visual Studio 2017 Community Edition](https://visualstudio.microsoft.com/downloads/)
			- Select *Desktop Development with C++*
			- Select *VC++ 2015.3 v14.00 (v140) toolset for desktop* on the right hand side

				**Note:** if you encounter an error *The build tools for v141 (Platform Toolset = 'v141') cannot be found."* make sure you installed the *VC++ 2015.3 v14.00 (v140) toolset for desktop* from the previous step
	- **Restart** your computer
    - **Warning:** Make sure your profile path only contains ASCII letters, e.g. *John*, otherwise it can lead to [node-gyp usage problems (nodejs/node-gyp/issues#297)](https://github.com/nodejs/node-gyp/issues/297)
    - **Note**: Building and debugging via the Windows subsystem for Linux (WSL) is currently not supported.
  - ### **macOS**
    - [Xcode](https://developer.apple.com/xcode/downloads/) and the Command Line Tools, which will install `gcc` and the related toolchain containing `make`
      - Run `xcode-select --install` to install the Command Line Tools
    - [MIT Kerberos library]. This should be installed with Xcode, but if this fails install homebrew and run `brew install krb5` to install this.
  - ### **Linux (please note: we do not support the [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/about))**
    * `make` 
       *On Debian-based Linux:`sudo apt install make`
    * [GCC](https://gcc.gnu.org) or another compile toolchain
       *On Debian-based Linux:`sudo apt install gcc`
    * [native-keymap](https://www.npmjs.com/package/native-keymap) needs `libx11-dev` and `libxkbfile-dev`.
      * On Debian-based Linux: `sudo apt-get install libx11-dev libxkbfile-dev`
      * On Red Hat-based Linux: `sudo yum install libX11-devel.x86_64 libxkbfile-devel.x86_64 # or .i686`.
    * [keytar](https://www.npmjs.com/package/keytar) needs `libsecret-1-dev`.
      * On Debian-based Linux: `sudo apt-get install libsecret-1-dev`.
      * On Red Hat-based Linux: `sudo yum install libsecret-devel`.
    * [MIT Kerberos library]
      * On Debian-based Linux: `sudo apt-get install libkrb5-dev`.
      * On Red Hat-based Linux: `sudo yum install -y krb5-devel`.
    * Building deb and rpm packages requires `fakeroot` and `rpm`, run: `sudo apt-get install fakeroot rpm`
    * **So for Ubuntu, a full installation would be `sudo apt install make gcc libx11-dev libxkbfile-dev libsecret-1-dev libkrb5-dev fakeroot rpm`**

# Recommended Tools

- [VSCode](https://code.visualstudio.com/) We have multiple integrations with VSCode's task system so it's highly recommended to use that as your Editor/IDE.

# Setting up NodeJS & Yarn:

Right now we're using Node 10. You can do `nvm install 10.16.3` to install that version. This creates a virtual node installation handled and managed by nvm.
 
The recommended way of installing yarn is through npm. So `npm install -g yarn`.

If you are on Windows or Linux 64 bit systems and would like to compile to 32 bits, you'll need to set the `npm_config_arch` environment to `ia32` before running `yarn`. This will compile all native node modules for a 32 bit architecture.

**Note:** For more information on how to install NPM modules globally on UNIX systems without resorting to `sudo`, refer to [this guide](http://www.johnpapa.net/how-to-use-npm-global-without-sudo-on-osx/).


# Source Code

## Getting Authenticated

If you're a new Microsoft employee, you will need to connect your github account to the Microsoft org. Ask a co-worker what the process on that is.

If you're an outside contributor, simply fork the codebase and make a PR against ours with your changes.

## Compiling

```bash
git clone https://github.com/microsoft/azuredatastudio
cd azuredatastudio
yarn
yarn run watch
```

## Running

**VSCode**

Go to debugging -> Launch azuredatastudio

**OS X and Linux**

```bash
./scripts/sql.sh
```

**Windows**

```bash
.\scripts\sql.bat
```

## Creating optimized builds

This step isn't necessary in development, but if you want to create a build with optimized source code for production. Essentially a "release" quality build:

```batch
REM for windows
yarn run gulp vscode-win32-x64
cd ../sqlops-windows-x64
```

```bash
# for macOS
yarn run gulp vscode-darwin
cd ../sqlops-darwin

# for linux
yarn run gulp vscode-linux-x64
cd ../sqlops-linux-x64
```

## Cleaning the repo

Sometimes we have breaking changes that require the entire code to be rebuilt. For example if we upgrade the electron version of the code, everyone's local development environment will break. To handle this scenario, we do a git clean. This restores the local cloned repository to its original form:

```bash
git clean -fxd
```


