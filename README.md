# bffi-examples

Official examples and end-to-end (e2e) test suite for
[bffi-rs](https://github.com/z2net/bffi-rs) - the Bun-only native
binding framework written in Rust.

Every directory is a self-contained native module that exercises the
full `.bffi` pipeline: Rust sources -> `bffi` macros -> cdylib ->
loader JSON -> generated TypeScript -> dlopen -> typed API, with
`bun test` suites that verify the whole chain against the PUBLISHED
packages:

- Rust: `bffi >= 0.2.0` from crates.io
- JS: `@z2net/bffi >= 0.2.0` from npm

Writing a GUI / event-driven binding (wry, winit, tao, SDL, ...)?
The [Binding GUI guide](https://github.com/z2net/bffi-rs/blob/main/docs/BINDING-GUI.md)
in bffi-rs covers the threading model, `invoke_wait`, and the
wry walkthrough this repo ships.

## Examples

| Directory | Covers |
| --- | --- |
| `sqlite` | entry example: a small functional SQLite surface (open/exec/query/close) over rusqlite |
| `async` | Rust futures as JS Promises, cancellation, timeouts, the pump |
| `event-loop` | queue, blocking/non-blocking drains, marshal, sticky stop |
| `callbacks` | both callback directions, the JS-thread gate, marshal delivery |
| `workers` | two JS threads of one process, targeted callback delivery across isolates |
| `records` | composite types: records, enums, `Vec<T>` sequences |
| `streams` | pull/push streams as JS async iterators |
| `errors` | typed errors (`#[derive(BffiError)]`): codes, variant payloads |
| `wry` | a real webview window driven from Bun (window + IPC on a native thread) |

## Prerequisites

- [Bun](https://bun.sh) >= 1.4.2
- Rust 1.98.0 (pinned by `rust-toolchain.toml`; `rustup` installs it
  automatically)

## Run

```sh
bun install                 # npm dependencies (per-workspace @z2net/bffi)
cargo build --release --workspace   # builds every example cdylib
bun test                    # runs every example's e2e suite
```

Or with the bundled scripts: `bun install && bun run build && bun run test:e2e`.

Each example also works standalone: `cd <example> && bun test` (after
the workspace build produced its `target/<...>/release` cdylib).

## The wry example

`wry` links a real webview stack. On Linux it needs the GTK/webkit
development packages before any cargo step:

```sh
sudo apt-get install -y libwebkit2gtk-4.1-dev libgtk-3-dev
```

Windows needs the WebView2 runtime (preinstalled on Windows 11); macOS
needs nothing extra. The wry e2e suite opens a REAL OS window, so it is
env-gated: without `BFFI_WRY_E2E=1` every wry test is skipped (CI-safe
by default):

```sh
BFFI_WRY_E2E=1 bun test wry   # from the repo root, opens a window
```

## Repository layout

```
bffi-examples/
├── Cargo.toml            # cargo workspace over the 9 example crates
├── rust-toolchain.toml   # pinned Rust 1.98.0
├── package.json          # bun workspaces over the 9 example packages
├── tsconfig.json
└── <example>/            # Cargo.toml, package.json, src/, .bffi/, test/
```

## Related

- [bffi-rs](https://github.com/z2net/bffi-rs) - the framework itself
  (crates, the `@z2net/bffi` package, design docs)

## License

[MIT](LICENSE) - Copyright (c) 2026 z2net
