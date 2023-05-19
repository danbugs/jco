<div align="center">
  <h1><code>jco</code></h1>

  <p>
    <strong>JavaScript component toolchain for working with <a href="https://github.com/WebAssembly/component-model">WebAssembly Components</a></strong>
  </p>

  <strong>A <a href="https://bytecodealliance.org/">Bytecode Alliance</a> project</strong>

  <p>
    <a href="https://github.com/bytecodealliance/jco/actions?query=workflow%3ACI"><img src="https://github.com/bytecodealliance/jco/workflows/CI/badge.svg" alt="build status" /></a>
  </p>
</div>

## Overview

`jco` is a fully native JS tool for working with the emerging [WebAssembly Components](https://github.com/WebAssembly/component-model) specification in JavaScript.

Features include:

* "Transpiling" Wasm Component binaries into ES modules that can run in any JS environment.
* Optimization helpers for Components via Binaryen.
* Component builds of [Wasm Tools](https://github.com/bytecodealliance/wasm-tools) helpers, available for use as a library or CLI commands for use in native JS environments.
* "Componentize" for WebAssembly Components from JavaScript sources and a WIT world

For creating components in other languages, see the [Cargo Component](https://github.com/bytecodealliance/cargo-Component) project for Rust and [Wit Bindgen](https://github.com/bytecodealliance/wit-bindgen) for various guest bindgen helpers.

> **Note**: This is an experimental project, no guarantees are provided for stability or support and breaking changes may be made in future.

## Installation

```shell
npm install @bytecodealliance/jco
```

jco can be used as either a library or as a CLI via the `jco` CLI command.

## Example

See the [example workflow](EXAMPLE.md) page for a full usage example.

## CLI

```shell
Usage: jco <command> [options]

jco - WebAssembly JS Component Tools
      JS Component Transpilation Bindgen & Wasm Tools for JS

Options:
  -V, --version                         output the version number
  -h, --help                            display help for command

Commands:
  componentize [options] <js-source>    Create a component from a JavaScript module
  transpile [options] <component-path>  Transpile a WebAssembly Component to JS + core Wasm for JavaScript execution
  opt [options] <component-file>        optimizes a Wasm component, including running wasm-opt Binaryen optimizations
  wit [options] <component-path>        extract the WIT from a WebAssembly Component [wasm-tools component wit]
  print [options] <input>               print the WebAssembly WAT text for a binary file [wasm-tools print]
  metadata-show [options] [module]      extract the producer metadata for a Wasm binary [wasm-tools metadata show]
  metadata-add [options] [module]       add producer metadata for a Wasm binary [wasm-tools metadata add]
  parse [options] <input>               parses the Wasm text format into a binary file [wasm-tools parse]
  new [options] <core-module>           create a WebAssembly component adapted from a component core Wasm [wasm-tools component new]
  embed [options] [core-module]         embed the component typing section into a core Wasm module [wasm-tools component embed]
  help [command]                        display help for command
```

### Componentize

To componentize a JS file run:

```
jco componentize app.js --wit wit -n world-name -o component.wasm
```

Creates a component from a JS module implementing a WIT world definition, via a Spidermonkey engine embedding.

Currently requires an explicit install of the componentize-js engine via `npm install @bytecodealliance/componentize-js`.

See [ComponentizeJS](https://github.com/bytecodealliance/componentize-js) for more details on this process.

> Additional engines may be supported in future via an `--engine` field or otherwise.

## API

#### `transpile(component: Uint8Array, opts?): Promise<{ files: Record<string, Uint8Array> }>`

Transpile a Component to JS.

**Transpilation options:**

* `name?: string` - name for the generated JS file.
* `instantiation?: bool` - instead of a direct ES module, output the raw instantiation function for custom virtualization.
* `map?: Record<string, string>` - remap component imports
* `validLiftingOptimization?: bool` - optimization to reduce code size
* `noNodejsCompat?: bool` - disables Node.js compatible output
* `tlaCompat?: bool` - enable compat in JS runtimes without TLA support
* `base64Cutoff?: number` - size in bytes, under which Wasm modules get inlined as base64.
* `js?: bool` - convert Wasm into JS instead for execution compatibility in JS environments without Wasm support.
* `minify?: bool` - minify the output JS.
* `optimize?: bool` - optimize the component with Binaryen wasm-opt first.
* `optArgs?: string[]` - if using optimize, custom optimization options (defaults to best optimization, but this is very slow)

#### `opt(component: Uint8Array, opts?): Promise<{ component: Uint8Array }>`

Optimize a Component with the [Binaryen Wasm-opt](https://www.npmjs.com/package/binaryen) project.

#### `componentWit(component: Uint8Array, document?: string): string`

Extract the WIT world from a component binary.

#### `print(component: Uint8Array): string`

Print the WAT for a Component binary.

#### `metadataShow(wasm: Uint8Array): Metadata`

Extract the producer toolchain metadata for a component and its nested modules.

#### `parse(wat: string): Uint8Array`

Parse a compoment WAT to output a Component binary.

#### `componentNew(coreWasm: Uint8Array | null, adapters?: [String, Uint8Array][]): Uint8Array`

"WIT Component" Component creation tool, optionally providing a set of named adapter binaries.

#### `componentEmbed(coreWasm: Uint8Array | null, wit: String, opts?: { stringEncoding?, dummy?, world?, metadata? }): Uint8Array`

"WIT Component" Component embedding tool, for embedding component types into core binaries, as an advanced use case of component generation.

#### `metadataAdd(wasm: Uint8Array, metadata): Uint8Array`

Add new producer metadata to a component or core Wasm binary.

## Contributing

Development is based on a standard `npm install && npm run build && npm run test` workflow.

Tests can be run without bundling via `npm run build:dev && npm run test:dev`.

Specific tests can be run adding the mocha `--grep` flag, for example: `npm run test:dev -- --grep exports_only`.

# License

This project is licensed under the Apache 2.0 license with the LLVM exception.
See [LICENSE](LICENSE) for more details.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in this project by you, as defined in the Apache-2.0 license,
shall be licensed as above, without any additional terms or conditions.