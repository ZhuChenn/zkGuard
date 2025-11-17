# zkGuard zombienet tests

## TLDR

### Requirements for a local run

- node
- npm
- yarn

### Brief instructions for executing the tests

- Compile zkGuard program (debug or release mode).
- Run from within the `zombienet-tests` directory:
- - `yarn install`
- - `yarn test`

This will add the profile target folder to the path and will download the zombienet binary in the same directory.

It will then execute all the end-to-end test (all the `.zndsl` file listed in `test_runner.sh`) for the program.

---

## Description

This folder contains the `zkGuard` tests written for the Polkadot zombienet testing framework.

Each zombienet test requires the following files:

- A network configuration specification file, in `.toml` format; these are stored in `zombienet-tests/network_defs`
- The test description, written in zombienet DSL and stored in a `.zndsl` file.
- (optional) One or more Javascript / Typescript file containing complex tests whose logic cannot be expressed 
  in zombienet DSL language; these are stored in the `zombienet-tests/js_scripts` directory.

## Running the test

### Prerequisites

All tests are executed by running actual `zkGuard` nodes, so an instance of the `zkguard` executable must be present on the target system.
This can be obtained by compiling this repository, as the test runner script looks for the compiled binary in the `target/debug/` or `target/release/` directories. Optionally, it is possible to download the pre-compiled binary from our official `zkGuard` repository, and copy that in the `zombienet-tests/bin` directory.

The test runner script also automatically downloads the `zombienet` executable from the official GitHub repo: <https://github.com/paritytech/zombienet/releases> and places that in the `zombienet-tests/bin` as well.

Finally, for local execution, the following packages must be present in the system:

- node
- npm
- yarn

### Execute the test suite entirely
