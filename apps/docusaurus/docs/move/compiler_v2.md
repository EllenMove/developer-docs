---
title: "Compiler v2 (beta)"
---

# Move Compiler v2 (beta)

Move compiler v2 is a new tool which translates Move source code into Move bytecode. It unifies the architectures of the Move compiler and the Move Prover, enabling faster innovation in the Move language. It also offers new tools for defining code optimizations which can be leveraged to generate more gas efficient code for Move programs.

## Beta Testing

Compiler v2 (version `2.0-unstable`) is currently in beta testing and the developer community is encouraged to try it. However, the code produced by this version of the compiler should **NOT** be deployed on `mainnet`. Using it for unit tests or for a local nodes, `devnet`, and `testnet` is fine. This page will be updated once this changes.

## Reporting an Issue

If you run into issues, please use [this link to create a github issue][bug]. If you are able to provide a small piece of Move code which reproduces the issue, debugging and fixing it will be easier for us.

[bug]: https://github.com/aptos-labs/aptos-core/issues/new?title=[compiler-v2]%20%3CPLEASE%20NAME%20IT%3E&body=%3CPLEASE%20DESCRIBE%20IT%3E&labels=compiler-v2&projects=aptos-labs/16

## Using Compiler v2

To run compiler v2, pass the flag `--compiler-version=2` to the Aptos CLI. Examples:

```bash filename="Terminal"
aptos move compile --compiler-version=2
aptos move test --compiler-version=2
aptos move prove --compiler-version=2
```

## Using Language Version 2

Compiler v2 implements some first features for Move language version 2. The scope of Move v2 is described in the blog [The Future of Move at Aptos](https://medium.com/aptoslabs/the-future-of-move-at-aptos-17d0656dcc31). The growing list of new features is documented below and will be extended as new features are added:

- Receiver style function calls (see the [Move Book](./book/functions.md) for more details)

One must currently explicitly opt-in to use language version v2 with the flag `--language-version=2`:

```bash filename="Terminal"
aptos move compile --compiler-version=2 --language-version=2
```

A new Move compiler and language version is currently in early beta testing. If you are interested to play with it, check [this page](./compiler_v2.md).
