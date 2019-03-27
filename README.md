# Open Wallet Protocol
This protocol defines a way of interaction between distributed Application and Wallet, for different platforms. It can be used for Hardware Wallets too. The protocol allows third-party extensions to it.

---
## Protocol Structure

### Messages
Request message in Open Wallet protocol is a JSON serialized object with this structure.
```json
{
  "version": "1.0", // Protocol version
  "id": "int", // Request id
  "request": "object" // Request object
}
```
Response message has this structure:
```json
{
  "version": "1.0", // Protocol version
  "id": "int", // Request id
  "response": "object | prmitive | null", // Response
  "error": "object | null" // Error object
}
```
`error` should be `null` in all non-error cases. `response` can be `null` for requests without response data.

Primitives that can be handled by JSON (integer, string, boolean, float, array, object) should be serialized in proper JSON types.

`Date` type by default should be serialized to ISO8601 string format.

`Binary data` by default should be sent in Base64 format.

API can provide own rules for serialization of JSON non-native types (network based)

### Mobile
For IPC on mobile devices protocol defines special UTI schemes in reverse domain notation.

Scheme should start from `one.openwallet` domain prefix. Each API should define own subdomain inside this domain.

#### API avialability checks
For API avialability checks we use URL shemes, based on API names. URL sheme prefix is `openwallet`, URL suffix is a kebab-cased string, unique for each API.

### Browser
In browser IPC should be implemented via `Window.postMessage()` mechanism. Origin of message is always `"*"` (they will be sent to own window). 

Request message will have slightly different format:
```json
{
  "type": "string", // New option. Message type
  "version": "1.0", // Protocol version
  "id": "int", // Request id
  "request": "object" // Request object
}
```
New `type` property is a string which consists of `OPENWALLET` prefix and snake-cased API suffix.

#### API avialability checks
Application can send special message to check is API available. Wallet should respond in this message immediatly. Timeout for response is 1 second. If Application can't get response in this timeframe, then it should suggest, that there is no wallet with Open Wallet Protocol support.

If wallet doesn't support requested API it should respond too, but with an error message.

Request message:
```json
{
  "type": "OPENWALLET_HAS_API",
  "version": "1.0",
  "id": "int", // Request id
  "request": { "type": "{API TYPE}" }
}
```

Response message:
```json
{
  "version": "1.0",
  "id": "int", // Request id
  "response": true,
  "error": null
}
```

If wallet doesn't support this API it should respond with:
```json
{
  "version": "1.0",
  "id": "int", // Request id
  "response": null,
  "error": { 
    "type": "NOT_SUPPORTED",
    "message": "API is not supported"
  }
}
```

---

## Protocol extension
If you want to propose changes to this protocol, create PR to this repository, with new markdown file into OWPs folder. File should be named `owp-X.md`, where `X` is a incremented number.

Proposal template can be found [here](owp-X.md)

---
## APIs
### Keychain
Keychain API is a blockchain network based API, which allows signing and authentication functionality for Applications.

Domain for UTI: `one.openwallet.keychain.{network}`

URL Scheme: `openwallet-keychain-{network}://`

Message type: `OPENWALLET_KEYCHAIN_{NETwORK}`

[Keychain API Documentation](keychain.md)
