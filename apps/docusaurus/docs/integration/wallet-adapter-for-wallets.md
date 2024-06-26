---
title: "For Wallets"
id: "wallet-adapter-for-wallets"
---

# Wallet Adapter For Wallet Builders

To gain from dapps in the Aptos Ecosystem and provide your users the functionality they are looking for in a wallet, your wallet plugin should follow the [Aptos Wallet Standard](../standards/wallets.md) and be built from the Aptos Wallet Adapter.

The [wallet-adapter-plugin-template](https://github.com/aptos-labs/wallet-adapter-plugin-template) repository gives wallet builders a pre-made class with all required wallet functionality following the Aptos Wallet Standard for easy and fast development.

## Configuration

1. `git clone git@github.com:aptos-labs/wallet-adapter-plugin-template.git`
2. Open `src/index.ts` for editing.
3. Replace all `AptosWindow` references with: `<Your-Wallet-Name>Window`
4. Replace `AptosWalletName` with: `<Your-Wallet-Name>WalletName`
5. Replace `url` with your website URL.
6. Change `icon` to your wallet icon (pay attention to the required format).
7. Replace `window.aptos` with: `window.<your-wallet-name>`

- Make sure the `Window Interface` has `<your-wallet-name>` as a key (instead of `aptos`).

8. Open `__tests/index.test.tsx` and change `AptosWallet` to: `<Your-Wallet-Name>Wallet`
9. Run tests with `pnpm test` - all tests should pass.

At this point, you have a ready wallet class with all required properties and functions to integrate with the Aptos Wallet Adapter.

### Publish as a package

The next step is to publish your wallet as an NPM package so dapps can install it as a dependency. Use one of the options below:

[Creating and publishing scoped public packages](https://docs.npmjs.com/creating-and-publishing-scoped-public-packages)

[Creating and publishing unscoped public packages](https://docs.npmjs.com/creating-and-publishing-unscoped-public-packages)

:::tip
If your wallet provides functionality that is not included, you should open a pull request against `aptos-wallet-adapter` in the core package to have it support this functionality. See the `signTransaction` on the [wallet core package](https://github.com/aptos-labs/aptos-wallet-adapter/blob/main/packages/wallet-adapter-core/src/WalletCore.ts) for guidance.
:::

### Add your name to the wallets list

Once the package is published, create a pull request against the [aptos-wallet-adapter](https://github.com/aptos-labs/aptos-wallet-adapter) package and add your wallet name to the [supported wallet list](https://github.com/aptos-labs/aptos-wallet-adapter#supported-wallet-packages) on the README file as a URL to your NPM package.

## AIP-62 Wallet Standard

The AIP-62 Wallet Standard is a chain-agnostic set of interfaces and conventions that aim to improve how applications interact with injected wallets. Read more about it [here](https://github.com/aptos-foundation/AIPs/blob/main/aips/aip-62.md).

### Why integrating with the AIP-62 wallet standard?

:::note
In the near future and as wallets onboard to the new standard, the wallet adapter will deprecate the legacy standard and keep only the AIP-62 wallet standard support.
:::

The AIP-62 wallet standard eliminates the current issues wallets have:

- Relying solely on a dapp detecting process logic can create a race condition risk in the case the dapp loads before a wallet and the dapp is not aware of the new wallets
- The legacy standard is deeply integrated within the Aptos wallet adapter, and any change can cause breaking changes for dApps and wallets, creating endless maintenance work by requiring a dApp or wallet to implement these changes.
- The legacy standard supports only the legacy TS SDK input, types, and logic. That means that it doesn't enjoy the features and enhancements of the new TS SDK. In addition, the legacy TS SDK does not receive any more support or new features.

In addition, the new wallet standard described in AIP-62 provides many benefits to wallets:

- Wallet owns and controls its own interface and can easily update and provide new features without introducing any breaking change to the dapp
- The wallet integration code lives in the wallet codebase and does not require a wallet to create and maintain another wallet package
- Wallets dont need to rely on dapps to install and maintain its wallet package
- The AIP-62 wallet standard provides better validation and error handling support, and uses the new TS SDK which is more reliable, fast and actively maintained and developed with new features (the legacy sdk, i.e aptos, is no longer actively maintained and developed).

## How to integrate with the AIP-62 wallet standard?

:::tip
Aptos provides a [@aptos-labs/wallet-standard](https://github.com/aptos-labs/wallet-standard) package that holds the standard interface and class a wallet should implement. In addition, it provides helper functions to detect Aptos compatible wallets.
:::

Integrating with the AIP-62 wallet standard is pretty simple and straightforward. For more information, users can refer to [this example](https://github.com/aptos-labs/aptos-wallet-adapter/blob/main/apps/nextjs-example/utils/standardWallet.ts) to learn how to integrate with the new wallet standard.

:::note
To be compatible with AIP-62 wallet standard and for be able to be detected by dapps, wallets must implement and support [required features](https://github.com/aptos-labs/wallet-standard/blob/main/src/detect.ts#L16)
:::

:::note
Make sure you use the [@aptos-labs/ts-sdk](https://www.npmjs.com/package/@aptos-labs/ts-sdk) SDK package
:::

### AptosWallet interface implementation

A wallet must implement an `AptosWallet` interface with the wallet provider info and features:

```ts
import { AptosWallet } from "@aptos-labs/wallet-standard";

class MyWallet implements AptosWallet {
  url: string;
  version: "1.0.0";
  name: string;
  icon:
    | `data:image/svg+xml;base64,${string}`
    | `data:image/webp;base64,${string}`
    | `data:image/png;base64,${string}`
    | `data:image/gif;base64,${string}`;
  chains: AptosChain;
  features: AptosFeatures;
  accounts: readonly AptosWalletAccount[];
}
```

### AptosWalletAccount interface implementation

A wallet must implement a AptosWalletAccount interface that represents the accounts that have been authorized by the dapp.

```ts
import {
  AptosWalletAccount,
  IdentifierArray,
} from "@aptos-labs/wallet-standard";
import { SigningScheme } from "@aptos-labs/ts-sdk";

class MyWalletAccount implements AptosWalletAccount {
  address: string;

  publicKey: Uint8Array;

  chains: IdentifierArray;

  features: IdentifierArray;

  signingScheme: SigningScheme;

  label?: string;

  icon?:
    | `data:image/svg+xml;base64,${string}`
    | `data:image/webp;base64,${string}`
    | `data:image/png;base64,${string}`
    | `data:image/gif;base64,${string}`
    | undefined;

  constructor(account: Account) {
    this.address = account.accountAddress.toString();
    this.publicKey = account.publicKey.toUint8Array();
    this.chains = APTOS_CHAINS;
    this.features = ["aptos:connect"];
    this.signingScheme = SigningScheme.Ed25519;
  }
}
```

### Register Wallet

#### Web extension wallet

A wallet registers itself using the registerWallet method to notify the dapp it is ready to be registered.

```ts
import { registerWallet } from "@aptos-labs/wallet-standard";

const myWallet = new MyWallet();

registerWallet(myWallet);
```

## My wallet is compatible with the AIP-62 standard, now what?

If your wallet supports the AIP-62 Standard, you should be able to include it with the [aptos-wallet-adapter](https://github.com/aptos-labs/aptos-wallet-adapter) tool for a broad ecosystem visibility and support.

### Is your wallet a chrome extension wallet?

If your wallet is a chrome extension based wallet, please open up a PR against the [aptos-wallet-adapter](https://github.com/aptos-labs/aptos-wallet-adapter) repo and add your wallet to the [Adapter Wallet Registry](https://github.com/aptos-labs/aptos-wallet-adapter/blob/main/packages/wallet-adapter-core/src/AIP62StandardWallets/registry.ts#L15) so the tool can detect the wallet even if it is not istalled on the user machine

### Is your wallet a SDK (npm package) wallet?

If your wallet is a SDK based wallet, please open up a PR against the [aptos-wallet-adapter](https://github.com/aptos-labs/aptos-wallet-adapter) repo and

- Add the wallet npm package as a dependency in the [adapter-core package](https://github.com/aptos-labs/aptos-wallet-adapter/blob/main/packages/wallet-adapter-core/package.json)
- Import your wallet and add it to the wallets array in the [adapter-core-package](https://github.com/aptos-labs/aptos-wallet-adapter/blob/main/packages/wallet-adapter-core/src/AIP62StandardWallets/sdkWallets.ts#L6)

This will make sure you wallet is being added to the Adapter SDK Wallets list so the tool can add your wallet as part of the all suggested wallets
