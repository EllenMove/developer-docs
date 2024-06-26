---
title: "Go SDK"
---

# Aptos Go SDK

## Installing the Go SDK

:::tip
The Go SDK is currently in a Beta, and its interfaces are subject to change
:::

Aptos provides an official Go SDK in
the [Aptos-core GitHub](https://github.com/aptos-labs/aptos-go-sdk) repository.
To use the Go SDK, get the main package here:

```bash
go get github.com/aptos-labs/aptos-go-sdk
```

You can additionally add the `bcs` or `crypto` libraries

```bash
go get github.com/aptos-labs/aptos-go-sdk/bcs
go get github.com/aptos-labs/aptos-go-sdk/crypto
```

## Using the Go SDK

### Creating a client

You can create a client by importing the aptos-go-sdk, and creating a `Client`

```go
package example

import (
    github.com/aptos-labs/aptos-go-sdk
)

func test() {
  client := aptos.NewClient(aptos.DevnetConfig)
}
```

You can configure the network with the `aptos.NetworkConfig`, or use a
preexisting `aptos.DevnetConfig`, `aptos.TestnetConfig`,
or `aptos.MainnetConfig`

### Creating a private key

You can create a new `Ed25519` account's private key by
calling `NewEd25519Account()`.

```go
account, err := aptos.NewEd25519Account()
if err != nil {
  return err
}
```

### Funding accounts

You can create and fund an account with a faucet on any network that is not
mainnet

```go
account, err := aptos.NewEd25519Account()
err = client.Fund(account.Address, 100_000_000)
```

### Sending a transaction

You can send a AptosCoin via a transaction

```go
  account, err := aptos.NewEd25519account()

  // Build transaction
	signed_txn, err := aptos.APTTransferTransaction(client, account, AccountOne, 100)

	// Submit transaction
	result, err := client.SubmitTransaction(signed_txn)
	hash := result["hash"].(string)

	// Wait for the transaction
	_, err = client.WaitForTransaction(hash)

  // Read transaction by hash
	txn, err := client.TransactionByHash(hash)
```

### More examples

You can see more examples in
the [`examples/` folder](https://github.com/aptos-labs/aptos-go-sdk/tree/main/examples)
of the Go SDK repository
