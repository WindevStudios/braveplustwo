# Brave Browser, plus two (Windev Browse)


## Overview

a better version of brave, downloads some packages n crap idfk anymore  

  - [Chromium](https://chromium.googlesource.com/chromium/src.git)
    - Fetches code via `depot_tools`.
    - sets the branch for Chromium (ex: 65.0.3325.181).
  - [brave-core](https://github.com/windevstudios/browse-core)
    - Mounted at `src/brave`.
    - Maintains patches for 3rd party Chromium code.
  - [adblock-rust](https://github.com/windevstudios/adblock-rust)
    - Implements Brave's ad-block engine.
    - Linked through [windevstudios/adblock-rust-ffi](https://github.com/windevstudios/adblock-rust-ffi).

## Downloads

You can [visit our website](https://windevbrowse.tk/download) to get the latest stable release.

## Other repositories

For other versions of our browser, please see:

* iOS - [windevstudios/browse-ios](https://github.com/windevstudios/browse-ios)

## Contributing

Please see the [contributing guidelines](./CONTRIBUTING.md).

## Community

[Join the Q&A community](https://community.windevbrowse.tk/) if you'd like to get more involved with Brave. You can [ask for help](https://community.windevbrowse.tk/c/support-and-troubleshooting),
[discuss features you'd like to see](https://community.windevbrowse.tk/c/feature-requests), and a lot more. We'd love to have your help so that we can continue improving Brave.

Help us translate Brave to your language by submitting translations at https://www.transifex.com/brave/brave/.

Follow [@windev_tweets](https://twitter.com/windev_tweets) on Twitter for important news and announcements.

## Install prerequisites

Follow the instructions for your platform:

- [macOS](https://github.com/windevstudios/braveplustwo/wiki/macOS-Development-Environment)
- [Windows](https://github.com/windevstudios/braveplustwo/wiki/Windows-Development-Environment)
- [Linux/Android](https://github.com/windevstudios/braveplustwo/wiki/Linux-Development-Environment)

## Clone and initialize the repo

Once you have the prerequisites installed, you can get the code and initialize the build environment.

```bash
git clone git@github.com:brave/brave-browser.git
cd brave-browser
npm install

# this takes 30-45 minutes to run
# the Chromium source is downloaded which has a large history
npm run init
```
brave-core based android builds should use `npm run init -- --target_os=android --target_arch=arm` (or whatever cpu type you want to build for)

You can also set the target_os and target_arch for init and build using

```
npm config set target_os android
npm config set target_arch arm
```

## Build Brave
The default build type is component.

```
# start the component build compile
npm run build
```

To do a release build:

```
# start the release compile
npm run build Release
```

brave-core based android builds should use `npm run build -- --target_os=android --target_arch=arm` or set the npm config variables as specified above for `init`

### Build Configurations

Running a release build with `npm run build Release` can be very slow and use a lot of RAM especially on Linux with the Gold LLVM plugin.

To run a statically linked build (takes longer to build, but starts faster)

```bash
npm run build -- Static
```

To run a debug build (Component build with is_debug=true)

```bash
npm run build -- Debug
```

You may also want to try [[using sccache|sccache-for-faster-builds]].

## Run Brave
To start the build:

`npm start [Release|Component|Static|Debug]`

# Update Brave

`npm run sync -- [--force] [--init] [--create] [brave_core_ref]`

**This will attempt to stash your local changes in brave-core, but it's safer to commit local changes before running this**

`npm run sync` will (depending on the below flags):

1. ???? Update sub-projects (chromium, brave-core) to latest commit of a git ref (e.g. tag or branch)
2. ???? Apply patches
3. ???? Update gclient DEPS dependencies
4. ??? Run hooks (e.g. to perform `npm install` on child projects)

| flag | Description |
|---|---|
|`[no flags]`|updates chromium if needed and re-applies patches. If the chromium version did not change it will only re-apply patches that have changed. Will update child dependencies **only if any project needed updating during this script run** <br> **Use this if you want the script to manage keeping you up to date instead of pulling or switching branch manually. **|
|`--create`|when used with `brave_core_ref` it will create a branch if one does not already exist|
|`--force`|updates both _Chromium_ and _brave-core_ to the latest remote commit for the current brave-core branch and the _Chromium_ ref specified in brave-browser/package.json (e.g. `master` or `74.0.0.103`). Will re-apply all patches. Will force update all child dependencies <br> **Use this if you're having trouble and want to force the branches back to a known state. **|
|`--init`|force update both _Chromium_ and _brave-core_ to the versions specified in brave-browser/package.json and force updates all dependent repos - same as `npm run init`|


Run `npm run sync brave_core_ref` to checkout the specified _brave-core_ ref and update all dependent repos including chromium if needed

### Scenarios

#### Create a new branch
```bash
brave-core> git checkout -b branch_name
```

or

```bash
brave-browser> npm run sync -- --create branch_name
```

### Checkout an existing branch or tag
```bash
brave-core> git fetch origin
brave-core> git checkout [-b] branch_name
brave-core> npm run sync
...Updating 2 patches...
...Updating child dependencies...
...Running hooks...
```

or

```bash
brave-browser> npm run sync --create branch_name
...Updating 2 patches...
...Updating child dependencies...
...Running hooks...
```

### Update the current branch to latest remote
```bash
brave-core> git pull
brave-core> npm run sync
...Updating 2 patches...
...Updating child dependencies...
...Running hooks...
```

#### Reset to latest brave-browser master, brave-core master and chromium
```bash
brave-browser> git checkout master
brave-browser> git pull
brave-browser> npm run sync -- --init
```

#### When you know that DEPS didn't change, but .patch files did (quickest)
```bash
brave-core> git checkout featureB
brave-core> git pull
brave-browser> npm run apply_patches
...Applying 2 patches...
```

# Enabling third-party APIs:

1. **Google Safe Browsing**: Get an API key with SafeBrowsing API enabled from https://console.developers.google.com/. Update the `GOOGLE_API_KEY` environment variable with your key as per https://www.chromium.org/developers/how-tos/api-keys to enable Google SafeBrowsing.

# Troubleshooting

See [Troubleshooting](https://github.com/windevstudios/braveplustwo/wiki/Troubleshooting) for solutions to common problems.
