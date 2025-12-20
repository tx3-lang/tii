# Transaction Invoke Interface (TII)

## What is TII?

The **Transaction Invoke Interface (TII)** is a declarative specification format used by the **Tx3 framework** to describe *how transactions can be invoked* in a protocol-agnostic way.

A TII document defines:

- Which **transactions** exist
- What **parameters** each transaction requires
- How those parameters are **typed and validated** (via JSON Schema)
- Which **environments** exist (e.g. testnet, mainnet)
- What **default values** an environment provides for transaction parameters

TII is an **interface description**, not a transaction builder and not an execution format.

---

## Design principles

1. **Schema-first**  
   All types that appear in the interface are expressed using **JSON Schema**.

2. **No concrete values in transactions**  
   Transactions describe *shapes and constraints*, never runtime values.

3. **Environment-driven defaults**  
   Environments may provide default values for a subset of transaction parameters.

4. **Separation of concerns**  
   - TII → interface and validation  
   - Tx3 runtime → semantics and execution

5. **Tooling-friendly**  
   The format is designed to support validation, code generation, CLIs, and UIs.

---

## Core concepts

### Transactions

A **transaction** describes a callable operation and the parameters required to invoke it.

Each transaction defines:

- `params` — a JSON Schema object describing valid invocation parameters
- `ir` — metadata describing the expected transaction IR format

Example (simplified):

```json
"transactions": {
  "transfer": {
    "params": { "type": "object", "properties": { ... } }
  }
}
```

### Environments

An environment represents a deployment or execution context (e.g. preview, mainnet).

Environments may define defaults for transaction parameters. These defaults act as a partial application of the transaction interface.

Each environment defines:

- `defaults.schema` — which parameters it may provide defaults for
- `defaults.values` — the concrete default values

### Invocation model

When invoking a transaction using TII:

1. A transaction is selected
2. An environment may be selected
3. Environment default values are applied
4. User-supplied values override defaults
5. The final parameter object is validated against the transaction schema
6. The transaction is invoked using the Tx3 runtime

This model ensures predictable behavior and strong validation guarantees.

---

## What TII is not

TII intentionally does not:

- Encode runtime execution logic
- Define transaction semantics
- Include concrete transaction values
- Replace protocol-specific tooling

Instead, TII acts as a stable interface layer between protocols and tooling.

---

## Inspiration

TII is conceptually inspired by interface description formats such as OpenRPC and OpenAPI, but is specialized for transaction-centric systems rather than request/response APIs.

---

## Typical use cases

- Generating CLIs for transaction invocation
- Powering UI forms with schema-driven validation
- Sharing transaction interfaces across tools
- Enabling multi-environment transaction workflows