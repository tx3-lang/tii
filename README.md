# Transaction Invocation Interface (TII)

The **Transaction Invocation Interface (TII)** is the declarative format used by the [Tx3](https://github.com/tx3-lang/tx3) toolchain to describe how transactions in a Tx3 protocol can be invoked. A TII document is a single JSON file that lists the available transactions, the parameters each one accepts, the environments (profiles) those parameters can be evaluated under, and the compiled TIR bytecode that the runtime needs in order to resolve them.

## Spec versions

- `v1beta0/tii.json` — current stable JSON Schema. Self-contained (no external `$ref`s).

## Producing TII

TII documents are produced by `tx3c` (the Tx3 compiler) during a `trix build`. They are not authored by hand. The compiler reads the project's `.tx3` source, encodes the TIR for each declared transaction, and emits a single `<name>.tii` file alongside parameter schemas inferred from the source.

## Consuming TII

TII is consumed by tools that need to invoke or expose Tx3 transactions:

- `trix codegen` (in [tx3-lang/trix](https://github.com/tx3-lang/trix)) — generates TypeScript / Rust / Go / Python bindings from a TII document.
- CLIs and UIs — drive form-based invocation directly from a TII's parameter schemas.
- Backends that need to forward transaction invocations to a TRP resolver (see [tx3-lang/trp](https://github.com/tx3-lang/trp)).

## What a TII document contains

- `tii.version` — the TII schema version the document targets.
- `protocol` — name, version, optional description of the protocol the document describes.
- `environment` — optional JSON Schema describing the environment variables a profile must provide.
- `parties` — declared parties (e.g. `sender`, `receiver`), each with an optional description.
- `transactions` — keyed by transaction name; each entry carries a `tir` envelope (the compiled bytecode) and a `params` JSON Schema describing valid invocation arguments.
- `profiles` — named deployment contexts (e.g. `cardano-preview`, `mainnet`) that supply concrete values for `environment` and `parties`.
- `components.schemas` — optional shared JSON Schema fragments referenced by transaction params.

See [`examples/transfer.tii`](./examples/transfer.tii) for a minimal end-to-end example.

## Relationship to TRP

TII describes *what* transactions a protocol exposes; the [Transaction Resolver Protocol (TRP)](https://github.com/tx3-lang/trp) describes the wire protocol used to actually resolve and submit them. A consumer typically reads a TII document, asks the user for parameter values, and then calls `trp.resolve` on a TRP-speaking backend with the encoded TIR and arguments.

## Design principles

- **Schema-first.** Every parameter is described by JSON Schema; the format itself is JSON Schema.
- **No runtime values in transactions.** Transactions describe shapes; profiles bind values.
- **Profile-driven defaults.** Environment / party bindings live in profiles, not in transactions.
- **Tooling-friendly.** The format is designed for validation, codegen, CLIs, and UIs — not for human authoring.
- **Self-contained.** The spec inlines all type definitions it needs; no cross-document `$ref` resolution required.

## License

Apache 2.0. See [`LICENSE`](./LICENSE).
