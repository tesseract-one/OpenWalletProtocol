---
owp: 1
title: Keychain API
author: Yehor Popovych <yehor@gettes.io>
status: Draft
created: 2019-03-27
---

# Keychain API

## Simple Summary
Blockchain network based APIs for signing/verification of data.

## Motivation
To allow Applications sign and verify data, without private key exporting. API should allow all signing types and methods, required by blockchain network. For this API defines a set of network related sub-APIs.

## Specification
Domain for UTI: `one.openwallet.keychain.{network}`

URL Scheme: `openwallet-keychain-{network}://`

Message type: `OPENWALLET_KEYCHAIN_{NETwORK}`

Common Errors:
* `WALLET_IS_NOT_INITIALIZED`: sent when Wallet doesn't have keys inside (not initialized yet).

### iOS
IPC on iOS is implemented as `Action` application extension. Action should handle API UTI (based on network). Body is sent as `String` value with serialized JSON inside. Response sent the same way.

Wallet application also should handle proper URL schemes, which allow client application to test support of the needed API.

Extension predicate format:
```
SUBQUERY(
  extensionItems,
  $item, 

  SUBQUERY(
    $item.attachments,
    $att,
    ANY $att.registeredTypeIdentifiers UTI-CONFORMS-TO "one.openwallet.keychain.ethereum"
    OR $att.registeredTypeIdentifiers UTI-CONFORMS-TO "one.openwallet.keychain.cardano"
  ).@count == $item.attachments.@count

).@count == 1
```

### Android
IPC on Android is implemented as External Activity for API UTI. Client application can check API support by UTI or URL. Wallet should implement URL schemes too.

Body is sent as serialized JSON `String` value.

### Ethereum API

#### Message structure
Request object of Open Wallet message has this structure:
```ts
{
  "method": String, // Ethereum signing method
  "networkId": Number, // Ethereum network id
  /* Other Request Parameters */
}
```

#### Methods

##### get_account

This metod will return active Ethereum account. 

* Parameters: `none`
* Response: account in hex form `0x....`.

#### sign

This metod will sign data for Ethereum network. Data will be signed with `\x19Ethereum Signed Message:\n` prefix.

* Parameters:
  * `account`: hex form of account
  * `data`: Base64 encoded data for signing
* Response: Base64 encoded signature data.
* Additional Errors:
  * `WRONG_ACCOUNT`: sent when wallet doesn't have provided `account`.

#### sign_transaction

This method will serialize and sign transaction.

* Parameters:
  * `nonce`: quantity in hex form
  * `from`: hex form of account
  * `to`: hex form of account. optional
  * `gas`: quantity in hex form
  * `gasPrice`: quantity in hex form
  * `value`: quantity in hex form
  * `data`: Base64 encoded data
* Response: Base64 encoded signature data
* Additional Errors:
  * `WRONG_ACCOUNT`: sent when wallet doesn't have provided `from` account.

#### sign_typed_data

This method will serialize and sign typed data as described in [EIP-712](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-712.md)

* Parameters: (check EIP-712)
  * `account`: hex form of account
  * `primaryType`: string name of primary type
  * `domain`: EIP712 domain object
  * `types`: EIP712 type map
  * `message`: message object for signing
* Response: Base64 encoded signature data
* Additional Errors:
  * `WRONG_ACCOUNT`: sent when wallet doesn't have provided `account`.
  * `TYPED_DATA_MALFORMED`: sent when typed data is malformed

#### Mobile
Domain for UTI: `one.openwallet.keychain.ethereum`

URL Scheme: `openwallet-keychain-ethereum://`

#### Browser

Message type: `OPENWALLET_KEYCHAIN_ETHEREUM`

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).