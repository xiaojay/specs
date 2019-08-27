# Status Account Specification

> Version: 0.1 (Draft)
>
> Authors: Corey Petty <corey@status.im> (alphabetical order)

## Summary

The core concept of an account in Status is a set of cryptographic keypairs.  Namely, the combination of the following:
1. a whisper chat identity keypair
1. a set of cryptocurrency wallet keypairs

Everything else associated with the contact is either verified or derived from the above items, including:
- Ethereum address (future verification, currently the same base keypair)
- 3 word mnemonic name
- identicon
- message signatures

## 1 Initial Key Generation
### 1.1 Public/Private Keypairs 
- An ECDSA (secp256k1 curve) public/private keypair MUST be generated via a [BIP43](https://github.com/bitcoin/bips/blob/master/bip-0043.mediawiki) derived path from a [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) mnemonic seed phrase.
- The default paths are defined as such:
    - Whisper Chat Key (`IK`): `m/43'/60'/1581'/0'/0`  (post Multiaccount integration)
        - following [EIP1581](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1581.md)
    - DB encryption Key (`DBK`): `m/43'/60'/1581'/1'/0` (post Multiaccount integration)
        - following [EIP1581](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1581.md)
    - Status Wallet paths: `m/44'/60'/0'/0'/i` starting at `i=0`
        - following [BIP44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki)
        - NOTE: this (`i=0`) is also the current (and only) path for Whisper key before Multiaccount integration

### 1.2 X3DH Prekey bundle creation
- Status follows the X3DH prekey bundle scheme that Open Whisper Systems outlines [in their documentation](https://signal.org/docs/specifications/x3dh/#the-x3dh-protocol) with the following exceptions:
    - Because there are no central servers, we do not publish one-time keys `OPK` or perform DH including them. 
- A client MUST create X3DH prekey bundles, each defined by the following items:
    - Identity Key: `IK`
    - Signed prekey: `SPK`
    - Prekey signature: `Sig(IK, Encode(SPK))`
    - Timestamp
- These bundles are made available in a variety of ways, as defined in section 2.1.

### 1.3 Register at push notification system
- TODO: Add this.

## 2 Account Broadcasting
- A user is responsible for broadcasting certain information publicly so that others may contact them.

### 2.1 X3DH Prekey bundles
- A client SHOULD regenerate a new X3DH prekey bundle every 24 hours.  This MAY be done in a lazy way, such that a client that does not come online past this time period does not regenerate or broadcast bundles.
- The current bundle MUST be broadcast on a whisper topic specific to his Identity Key, `{IK}-contact-code`, intermittently.  This MAY be done every 6 hours.
- A bundle MUST accompany every message sent.
- TODO: retreival of long-time offline users bundle via `{IK}-contact-code` 

## 3 Optional Account additions
### 3.1 ENS Username
- A user MAY register a public username on the Ethereum Name System (ENS).  This username is a user-chosen subdomain of the `stateofus.eth` ENS registration that maps to their whisper identity key (`IK`). 

### 3.2 User Chosen Name
- An account MAY create a display name to replace the $IK$ generated 3-word pseudonym in chat screens.  This chosen display name will become part of the publicly broadcasted profile of the account.

### 3.3 User Profile Picture
- An account MAY edit the `IK` generated identicon with a chosen picture.  This picture will become part of the publicly broadcasted profile of the account.

### 3.4 Tribute to Talk
- TODO - Couched until later

### 3.5 Wallet Accounts
- TODO (based in multiaccount)

## Security Implications