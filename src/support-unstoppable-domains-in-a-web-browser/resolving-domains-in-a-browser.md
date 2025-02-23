---
description: >-
  This page describes how to use UD Resolution to resolve blockchain domains
  using a traditional HTTP Web Browser or a Dapp Browser. It assumes the reader
  has a basic understanding of UD Resolution.
---

# Resolving Domains in a Browser

Resolution is a library for interacting with blockchain domain names. It can be used to retrieve [payment addresses](../send-and-receive-crypto-payments/crypto-payments.md), IPFS hashes for [decentralized websites](../build-a-decentralized-website/overview-of-ipfs-and-d-web.md), and GunDB usernames for [decentralized chat](https://unstoppabledomains.com/chat).

Resolution is built and maintained by Unstoppable Domains and supports decentralized domains across three main zones:

| Name Service | Supported Domains |
| :--- | :--- |
| Zilliqa Name Service \(ZNS\) | `.zil` |
| Ethereum Name Service \(ENS\) | `.eth`, `.kred`, `.xyz`, `.luxe` |
| Unstoppable Name Service \(UNS\) | `.crypto`, `.nft`, `.blockchain`, `.bitcoin`, `.coin`, `.wallet,` `.888`, `.dao`, `.x` |

{% hint style="info" %}
For more information on Unstoppable Domains Resolution, see [Resolving Domain Records](../domain-registry-essentials/resolving-domain-records.md) and the [Resolution API Reference](https://unstoppabledomains.github.io/resolution/).To make domain resolution easier, we've written libraries for web, Android, and iOS.
{% endhint %}

## ENS Support

Ethereum Name Service requires installing additional packages to avoid errors thrown by the library when trying to resolve ENS domain.

Required packages:

* `"bip44-constants": "^8.0.5"`
* `"@ensdomains/address-encoder": ">= 0.1.x <= 0.2.x"`
* `"content-hash": "^2.5.2"`

## Installing Resolution

Resolution can be installed with either `yarn` or `npm`.

```text
yarn add @unstoppabledomains/resolution
```

```text
npm install @unstoppabledomains/resolution --save
```

If you're interested in resolving domains via the command line, see the  [CLI section](resolving-domains-in-a-browser.md#command-line-interface) below.

## Using Resolution

Create a new project.

```text
mkdir resolution && cd $_
yarn init -y
yarn add @unstoppabledomains/resolution
```

### Look up a domain's crypto address

Create a new file in your project, `address.js`.

```text
const { default: Resolution } = require('@unstoppabledomains/resolution');
const resolution = new Resolution();

function resolve(domain, currency) {
  resolution
    .addr(domain, currency)
    .then((address) => console.log(domain, 'resolves to', address))
    .catch(console.error);
}

function resolveMultiChain(domain, currency, chain) {
  resolution
    .multiChainAddr(domain, currency, chain)
    .then((address) => console.log(domain, 'resolves to', address, version))
    .catch(console.error);
}

resolve('brad.crypto', 'ETH');
resolve('brad.zil', 'ZIL');
resolveMultiChain('brad.crypto', 'USDT', 'ERC20');
resolveMultiChain('brad.crypto', 'USDT', 'OMNI');
```

Execute the script.

```text
$ node address.js
brad.crypto resolves to 0x8aaD44321A86b170879d7A244c1e8d360c99DdA8
brad.zil resolves to zil1yu5u4hegy9v3xgluweg4en54zm8f8auwxu0xxj
```

### Find the IPFS hash for a decentralized website

Create a new file in your project, `ipfs_hash.js`.

```text
const { default: Resolution } = require('@unstoppabledomains/resolution');
const resolution = new Resolution();

function resolveIpfsHash(domain) {
  resolution
    .ipfsHash(domain)
    .then((hash) =>
      console.log(
        `You can access this website via a public IPFS gateway: https://gateway.ipfs.io/ipfs/${hash}`,
      ),
    )
    .catch(console.error);
}

resolveIpfsHash('homecakes.crypto');
```

Execute the script.

```text
$ node ipfs_hash.js
You can access this website via a public IPFS gateway: https://gateway.ipfs.io/ipfs/QmVJ26hBrwwNAPVmLavEFXDUunNDXeFSeMPmHuPxKe6dJv
```

### Find a custom record

Create a new file in your project, `custom-resolution.js`.

```text
const { default: Resolution } = require('@unstoppabledomains/resolution');
const resolution = new Resolution();

function resolveCustomRecord(domain, record) {
  resolution
    .records(domain, [record])
    .then((value) => console.log(`Domain ${domain} ${record} is: ${value}`))
    .catch(console.error);
}

resolveCustomRecord('homecakes.crypto', 'custom.record.value');
```

### Command Line Interface

To use resolution via the command line install the package globally.

```text
yarn global add @unstoppabledomains/resolution
```

```text
npm install -g @unstoppabledomains/resolution
```

By default, the CLI uses Infura as its primary gateway to the Ethereum blockchain. If you'd like to override this default and set another provider you can do so using the `--ethereum-url` flag.

For example:

```text
resolution --ethereum-url https://mainnet.infura.io/v3/${secret} -d udtestdev-usdt.crypto -a
```

Use the `-h` or `--help` flag to see all the available CLI options.

## Default Ethereum Providers

Resolution provides zero-configuration experience by using built-in production-ready [Infura](http://infura.io/) endpoint by default.  
Default Ethereum provider is free to use without restrictions and rate-limits for `CNS (.crypto domains)` resolution.  
To resolve `ENS` domains on production it's recommended to change Ethereum provider.  
Default provider can be changed by changing constructor options `new Resolution(options)` or by using one of the factory methods:

* `Resolution.infura()`
* `Resolution.fromWeb3Version1Provider()`
* `Resolution.fromEthersProvider()`
* etc.

To see all constructor options and factory methods check [Unstoppable API reference](https://unstoppabledomains.github.io/resolution).

## Autoconfiguration of Blockchain Network

In some scenarios system might not be flexible enough to easy distinguish between various Ethereum testnets on compile time. For this case Resolution library provide a special async constructor which should be waited for `await Resolution.autonetwork(options)`. This method makes a JSON RPC "net\_version" call to the provider to get the network id.

This method configures only ENS and CNS. ZNS is supported only on Zilliqa mainnet which is going to be used in any cases. You can provide a configured provider or a blockchain url as in the following example:

```text
await Resolution.autonetwork({
  cns: {provider},
  ens: {url}
});
```

## Error Handling

When resolution encounters an error it returns the error code instead of stopping the process. Keep an eye out for return values like `RECORD_NOT_FOUND`.

## Development

Use these commands to set up a local development environment \(**macOS Terminal** or **Linux shell**\).

1. Install `nvm`

   ```text
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.36.0/install.sh | bash
   ```

2. Install concrete version of `node.js`

   ```text
   nvm install 12.12.0
   ```

3. Install `yarn`

   ```text
   npm install -g yarn
   ```

4. Clone the repository

   ```text
   git clone https://github.com/unstoppabledomains/resolution.git
   cd resolution
   ```

5. Install dependencies

   ```text
   yarn install
   ```

### Internal Config

**To update:**

* Network config: `$ yarn network-config:pull`
* Supported keys: `$ yarn supported-keys:pull`
* Both configs: `$ yarn config:pull`

## Support

If you have any questions or need assistance with using UD Resolution CLI, join our [Discord channel](https://discord.gg/b6ZVxSZ9Hn) for real-time support from us and the community.

