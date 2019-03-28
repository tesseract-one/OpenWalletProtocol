---
owp: 2
title: Invoice Transactions
author: Robert Kornacki
status: Draft
created: 2019-03-28
---

# Invoice Transactions

## Simple Summary
On-chain Invoice protocol facilitated via appending data to transactions.

## Motivation
Providing cryptocurrency users Transaction Surety by limiting the amount of funds at risk, improving the user experience substantially, removing the chance of user input error, and doing so with a moderately simple and secure protocol. No smart contract or a centralized service is required, thus lowering both implementation and usability costs.

Through the use of public key encryption, the blockchain itself, and a message, this protocol should provide:
* Safety from MitM attacks (such as Replay attacks)
* Guarantee that the `Invoice Fulfillment Address` is owned & controlled by the Invoice sender
* Saftey from Foretelling attacks

## Specification

### Invoice Transaction

Invoice Transactions are normal transactions sent from one cryptocurrency wallet to another with encrypted appended data which adheres to the following format.

* `Data Identifier`: String to identify what the data is (Ex. "Invoice")
* `Requested Amount`: Amount of coins being requested (Ex. 2500 ada)
* `Message`: A string used to identify the correct Invoice for the Invoice receiver. (Ex. "Yard Work")
* `Invoice Fulfillment Address`: The Invoice creator's address. (Ex. fa2d2a70c0b5fd45cb6c3989f02813061f9d27f15f30ecddd38780c59f413c62)

## How It Works

The data above is encrypted by the Invoice creator with the receiver's public key.

#### Invoice Creation Process
The Invoice creator has to provide the following data to their wallet:

* `Requested Amount`
* `Message`
* `Invoice Receiver Address`

The Invoice creator's wallet automatically will:

* Fill out the `Data Indentifier`
* Fill out the `Invoice Fulfillment Address`
* Encrypt all of the data with the Invoice receiver's public key
* Append the data to a (optimally) 0 value transaction (to prevent dust for UTXO-based blockchains)
* Send the transaction

#### Invoice Fulfillment Process
The Invoice receiver's wallet will:

* Decrypt the Invoice data
* Verify that `Invoice Fulfillment Address` is equal to the address which the Invoice Tx was sent from.
* Display the Invoice data to the user in their wallet with the option to `Fulfill` the Invoice
   * If the user chooses to `Fulfill` the Invoice, then the wallet will automatically create the payment transaction for the user to confirm


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
