# BaoBao

BaoBao aims to be a developer tool that allows developers, artists, studios
and agencies publish ecrypted content to the blockchain that can only be
unencrypted by owners of an ERC-721 or ERC-1155 collectable.

For example: If Sanctuary Computer was to work with a famous hip-hop arist,
to publish a new track "drop" that was only accessible to users who've
purchased a limited edition collectible, this primitive would support that.

## Example Use Cases

### React.js Collectible Component
```
import React, { Suspense } from 'react';
import { baobaoRequireComponent } from 'baobao-react';

// baobaoRequire uses MetaMask or similar to a) verify the user
// owns the collectible, and b) decrypt the content (that NuCypher
// re-encrypted for us with this user's public key at the time of
// purchase).
const DecryptedComponent = React.lazy(() => baobaoRequireComponent('0x2af8cE3c56FD06AAF227869c1CD91DfA0A46fC7C'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>You've not purchased this collectible!</div>}>
        <DecryptedComponent />
      </Suspense>
    </div>
  );
}
```

### Following the above logic...

Anywhere you can access the user's cypto wallet, you can publish systems
that run with collectible content. Think:
- Video games where you pay for each mission to unlock them
- A bandcamp-like music store & player that is unlocked by purchases
- A clubhouse like app that requires a ETH donation to enter rooms

## System Diagram

`https://whimsical.com/baobao-KR7194heSh1b3fE46kM9V6`

## How it Works

### Publishing
- Publisher publishes NuCypher-friendly encyrpted data to IPFS
- Publisher publishes a NuCypher AccessPolicy for the data
- Publisher publishes ERC-721 contract that points to IPFS hash

### Purchasing
- Customer purchases ERC-721 with ETH
- BaoBao triggers a NuCypher rencryption worker against the Customer's public key
- Customer can now load the originally encrypted data through the NuCypher
rencryption proxy and decrypt it with their own private key

## Challenges & What I Don't Know
- When publishing content to IPFS, you can use a CLI, but working with web3
requires a browser addon or similar (at least for MetaMask). Can publishing
this a terminal-driven flow? Like:

```
baobao publish track.mp3 collectible-meta.json
```

Or do we build a browser app like "baobao-publisher.com" to handle the encryption,
upload, and deployment of ERC-721 from a UI? Personally, I'd want this to be a CLI.

- Can the purchase of an ERC-721 actually trigger a NuCypher re-encryption? Or
do we need a (centralized) app server for this?

- How does BaoBao encourage publishers to build on it? Can they earn BAOBAO
ERC-20 tokens for every purchase on a collectible that they helped launch?

- Can publishers determine a profit share or comission structure when
publishing a collectible?

- How can BaoBao make money? Is there an inflationary network effect we'll be uniquely
privy to? Do we take a ~5% commision on all sales?

## How is this different to...

### [Cargo](https://cargo.build/)
- I don’t wanna list my collectibles on a marketplace
- I want to limit my sale quantity
- Cargo UI is aggressively bad
- Cargo's "files" just use S3
- We need a lower level API for downloading encrypted files, accessible via
(at least) a browser based SDK (as per the example above).
- We want to own the protocol

### [Foundation.app](https://foundation.app/)
- No SDK. Just a marketplace

### [Open Sea](https://docs.opensea.io/)
- Doesn't appear to support encrypted, truly private data

## Prior Art
- https://thegraph.com/explorer/subgraph/wighawag/eip721-subgraph
- https://www.nucypher.com/proxy-re-encryption
- https://consensys.net/blog/press-release/metamask-mobile-ethereum-wallet-web3-browser-now-available-on-ios-and-android/
