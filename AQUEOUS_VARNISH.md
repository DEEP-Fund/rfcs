# AQUEOUS VARNISH ($AQVS)

AQVS aims to be a developer tool that allows developers, artists, studios
and agencies publish ecrypted content to the blockchain that can only be
unencrypted by owners of an ERC-1155 collectable.

For example: If Sanctuary Computer was to work with a famous hip-hop arist,
to publish a new track "drop" that was only accessible to users who've
purchased a limited edition collectible, this primitive would support that.

# System Components

## $AQVS Solidity Contract

### Requirements
- Should allow any wallet (Publisher) to mint an ERC1155 (that represents
an AQVS space)
- The amount of ETH passed when minting should map to the amount
of storage the Publisher can upload (via the proxy component below)
- Should allow publishers to set the cost & initialSupply of ERC1155
tokens for sale
- Should allow publishers to update attributes about their space, or
add more storage (by sending extra ETH to an existing ERC1155 token)
- Should define a given space's URI like `https://mainnet.aqueousvarni.sh/spaces/${space_token_id}`
(as per the ERC1155 URI Metadata standard)
- Should take some small nominal % of ERC1155 space access purchase prices
for the contract to keep itself

## $AQVS Serverless Proxy & CDN
- Should be able to provision a nonce for signing
- Should be able to validate that a given nonce was signed by a specific
wallet address, to prove authenticity of a given request
- Should be able to allow a publisher to use the $AQVS
- Should be able to proxy data request like:
`https://mainnet.aqueousvarni.sh/spaces/${minted_erc1155token_id}/audio/${some_file_path}.wav`
- Should be run in a globally load balanced edge network like [Fly.io](https://www.fly.io)
or Cloudflare Workers
- Should use the cheapest possible object storage & edge network for "behind-the-proxy"
authenticated file storage

### MVP Endpoints
```
POST /nonce
- Is rate limited
- Returns an unsigned nonce & stores it in Redis

PUT /spaces/${minted_erc1155token_id}/audio/${some_file_path}.wav
- Requires a Signed Nonce
- Validates address minted ${minted_erc1155token_id}
- Uploads some.cdn.net/${minted_erc1155token_id}/audio/${some_file_path}.wav

GET /spaces/${minted_erc1155token_id}/audio/${some_file_path}.wav
- Requires a Signed Nonce
- Validates user has bought ${minted_erc1155token_id}
- Proxies some.cdn.net/${minted_erc1155token_id}/audio/${some_file_path}.wav
back to the client
```

## $AQVS NPM CLI Tool (@aqueous-varnish/cli)
- Should be able to install via NPM or Yarn, and use via `npx aqvs [command]`
- Should be able to open the user's browser to sign a nonce via their web3
provider, and pass the signed nonce back into the CLI process
- Should be able to open the user's browser for them to provision a space like
`npx aqvs provision 256mb`, which in turn asks them to send a certain amount
of ETH to our ERC1155 contract
- Should allow a publisher to upload a folder structure glob to a previously
provisioned AQVS space

## $AQVS React (@aqueous-varnish/react)
- Should allow a React developer to install the `@aqueous-varnish/react` tooling
- Should allow them to do wild shit like this:
```
import React, { Suspense } from 'react';
import { aqvsRequire } from '@aqueous-varnish/react';

// baobaoRequire uses MetaMask or similar to a) verify the user
// owns the collectible, and b) decrypt the content (that NuCypher
// re-encrypted for us with this user's public key at the time of
// purchase).
const DecryptedComponent = React.lazy(() => aqvsRequire(
  'components/SecretComponent',
  {
    contract: '0x2af8cE3c56FD06AAF227869c1CD91DfA0A46fC7C',
    spaceId: '9968cddc-b535-45fc-ac98-1854d3420e53'
  }
));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>You've not purchased access to this space!</div>}>
        <DecryptedComponent />
      </Suspense>
    </div>
  );
}
```

