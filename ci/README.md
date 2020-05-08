# ci

This directory contains scripts used for code-server's continuous integration infrastructure.

Many of these scripts contain more detailed documentation and options in comments at the top.

Any file and directory added into this tree should be documented here.

## Publishing a release

1. Change the version of code-server in `package.json` and push this commit.
1. CI will run and generate an NPM package and release packages.
1. You must wait for this CI run to complete.
1. Create a new draft release with the built release packages.
1. Run some basic sanity tests on one of the released packages.
1. Publish the release.
1. CI will automatically grab the artifacts from the last job against the published commit and
   1. Publish the NPM package.
   1. Publish the AMD64 docker image.
   1. Publish the ARM64 docker image.

## dev

This directory contains scripts used for the development of code-server.

- [./dev/container](./dev/container)
  - See [CONTRIBUTING.md](../doc/CONTRIBUTING.md) for docs on the development container
- [./dev/ci.sh](./dev/ci.sh) (`yarn ci`)
  - Runs formatters, linters and tests
- [./dev/fmt.sh](./dev/fmt.sh) (`yarn fmt`)
  - Runs formatters
- [./dev/lint.sh](./dev/lint.sh) (`yarn lint`)
  - Runs linters
- [./dev/test.sh](./dev/test.sh) (`yarn test`)
  - Runs tests
- [./dev/vscode.sh](./dev/vscode.sh) (`yarn vscode`)
  - Ensures `lib/vscode` is cloned, patched and dependencies are installed
- [./dev/vscode.patch](./dev/vscode.patch)
  - Our patch of VS Code to enable remote browser access
  - Generate it with `yarn vscode:diff` and apply with `yarn vscode:patch`
- [./dev/watch.ts](./dev/watch.ts) (`yarn watch`)
  - Starts a process to build and launch code-server and restart on any code changes
  - Example usage in [CONTRIBUTING.md](../doc/CONTRIBUTING.md)

## build

This directory contains the scripts used to build code-server.
You can disable minification by setting `MINIFY=`.

- [./lib.sh](./lib.sh)
  - Contains code duplicated across these scripts.
- [./build/build-code-server.sh](./build/build-code-server.sh) (`yarn build`)
  - Builds code-server into ./out and bundles the frontend into ./dist.
- [./build/build-vscode.sh](./build/build-vscode.sh) (`yarn build:vscode`)
  - Builds vscode into ./lib/vscode/out-vscode.
- [./build/build-release.sh](./build/build-release.sh) (`yarn release`)
  - Bundles the output of the above two scripts into a single node module at `./release`.
- [./build/build-static-release.sh](./build/build-static-release.sh) (`yarn release:static`)
  - Requires a release already built in `./release`.
  - Will build a static release with node and node_modules into `./release-static`
- [./build/clean.sh](./build/clean.sh) (`yarn clean`)
  - Removes all git ignored files like build artifacts.
  - Will also `git reset --hard lib/vscode`
  - Useful to do a clean build.
- [./build/code-server.sh](./build/code-server.sh)
  - Copied into static releases to run code-server with the bundled node binary.
- [./build/test-release.sh](./build/test-static-release.sh)
  - Ensures code-server in the `./release-static` directory runs
- [./build/build-packages.sh](./build/build-static-pkgs.sh) (`yarn package`)
  - Packages `./release-static` into an archive in `./release-packages`
  - If on linux, [nfpm](https://github.com/goreleaser/nfpm) is used to generate .deb and .rpm
- [./build/nfpm.yaml](./build/nfpm.yaml)
  - Used to configure [nfpm](https://github.com/goreleaser/nfpm) to generate .deb and .rpm
- [./build/code-server-nfpm.sh](./build/code-server-nfpm.sh)
  - Entrypoint script for code-server for .deb and .rpm

## release-container

This directory contains the release docker container.

## container

This directory contains the container for CI.

## steps

This directory contains a few scripts used in CI.
Just helps avoid clobbering .travis.yml.

- [./steps/test.sh](./steps/test.sh)
  - Runs `yarn ci` after ensuring VS Code is patched
- [./steps/release.sh](./steps/release.sh)
  - Runs the full release process
  - Generates the npm package at `./release`
- [./steps/static-release.sh](./steps/static-release.sh)
  - Takes the output of the previous script and generates a static release and packages
- [./steps/lib.sh](./steps/lib.sh)
  - Contains helpers to download artifacts from github actions workflow runs
- [./steps/publish-npm.sh](./steps/publish-npm.sh)
  - Grabs the `npm-package` release artifact for the current commit and publishes it on NPM
- [./steps/publish-docker.sh](./steps/publish-docker.sh)
  - Grabs the `release-packages` release artifact for the current commit and builds a docker
    image with it and publishes that onto docker hub
