Status: _idea_ → _rough draft_ → _draft_ → __proposal__ → _final review_ → _stable_

# Concatenate content from the blockchain

> Let's get a common way of providing large files by having data spread amongst transactions.

This document describes a protocol named "Bcat" (pronounced `B cat`).
Please share [inputs and comments](https://github.com/bico-media/bcat/issues).

This protocol is only relevant if the data is larger than the limit of the current transaction size. If data fits within one transaction **always use the [B://](https://b.bitdb.network) format** for storing your data. With the current limit of 100KB per transaction, a regular Bcat transaction can represent a file with roughly 310MB of data (110 Gb if the content gateway supports the `nested-gzip` flag).

## Description

### Concatenating data

Transactions on the blockchain that includes the bitcom namespace `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` at the first position after an `OP_RETURN` code shall be called a "Bcat" transaction.

The `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` bitcom namespace must have 7 or more arguments to be valid:

1.  A string providing any unstructured `info` the sender finds relevant to share about how the Bcat transaction came to life. No longer than 128 characters (can be an empty string in the transaction data - but please be aware that some frameworks for BSV to create and broadcast transactions will treat an empty string as no data and then not provide any string)

2. A string providing the `MIME type` of the content of the file. No longer than 128 characters

3. A string providing the `charset`/encoding of the file. No longer than 16 characters (can be `NULL`[1])

4. A string providing the `name` of the file. No longer than 256 characters (can be `NULL`[1])

5. A string providing a `flag` indicating how to treat data. No longer than 16 characters (can be `NULL`[1])

6. 32 bytes representing the bytes of the hash of the transaction with the first part of data of the file (`TX1`).

7. 32 bytes representing the bytes of the hash of the transaction with the second part of data of the file (`TX2`)

Any number of arguments can follow providing a sequence of transaction IDs (`TXn`) in the order of how data is to be represented in the file. To be a valid Bcat transaction all transaction IDs (`TX1`, `TX2`, ... `TXn`) must be unique, unless using the `creative` flag.

[1] Any string consisting solely of any combination of the following chars will also be regarded as NULL:
  - "\0" (NULL)
  - "\t" (tab)
  - "\n" (new line)
  - "\x0B" (vertical tab)
  - "\r" (carriage return)
  - " " (ordinary white space)


#### Example:

```
OP_RETURN
  15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up
  'BSV4ever'
  'image/jpeg'
  0x00
  'zebra.jpeg'
  0x00
  0xe7e3bf38c590d432dcbfa7569b7977093a35755f7e288bef6f678c488e25fb50
  0xb09088d68b78bec9b6b39fd5defda5df5963a233609477c0eb73ac54fcd6bee3
```

### Parts of the data

A Transaction referenced from a Bcat transaction will be called a `Bcat part`. Each Bcat part transaction must follow the [B://](https://b.bitdb.network) specification or the `Bcat part` specification as described in the following:

- Transactions on the blockchain that include the bitcom namespace `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` at the first position after an `OP_RETURN` code shall be called `Bcat part` transactions.

- The `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` bitcom namespace must have 1 argument only:
  1. Raw data representing a part of the original file.

Avoid referencing data in the B:// format if the data does not represent a meaningful context by it self ie. if it does not make sense to show a user only this part of the data, use a `Bcat part` formatted transaction.

Please note that some flags expand the list of supported formats for transactions referenced.

### Flags

The 3rd argument to a Bcat transaction is `flag` and contains a string with information about how to deal with data. Only one flag can be provided. A content provider is free to choose to implement support for each flag or not. It is recommended to provide a useful error message if a valid but unsupported flag is used.

#### The `gzip` flag

A content gateway can decide to support Bcat transactions with the `gzip` flag by ensuring that
a Bcat transaction with this flag will have its content provided to the requesting client with **no further processing of the content** and the http response includes `Content-Encoding: gzip` as part of the header. Please note that the first argument `MIME type` must contain the MIME type of the original data.

Providing the `gzip` flag is recommended for all data that is not to be processed or merged with other data on the fly.

#### The `nested-gzip` flag

A content gateway can decide to support Bcat transactions with the `nested-gzip` flag by ensuring that
a Bcat transaction with this flag will be treated as if it had the `gzip` flag with the addition that along with the B:// and Bcat part format for referenced transactions any Bcat transaction with the `gzip` flag set will also be valid.

### Error handling

An invalid Bcat transaction should return an error to the client. This includes

- Any argument longer than described in the protocol
- Less than 7 arguments provided to a the Bcat transaction
- Reuse of referenced TX's
- Any referenced TX is not in the B:// or Bcat part format (exempt addition described for `nested-gzip`)'

## Definition

If you provide content from the blockchain you are compatible with the Bcat protocol if

- A client requesting the content of a Bcat transaction will receive content based on the concatenated data from `TX1`, `TX2` ... `TXn`.

- The concatenation of data from `TX1`, `TX2` ... `TXn` is binary safe.

- The handling of the concatenated data honors the description of the `flag` provided or with information letting the client know that the flag is not supported.

----

Please share [inputs and comments](https://github.com/bico-media/bcat/issues).
