Status: _idea_ → _lose draft_ → _draft_ → __proposal__ → _final review_ → _stable_

# Concatenate content from the blockchain

> Let's get a common way of providing large files by having data spread amongst transactions.

This document describes a protocol named "B://cat" (pronounced `B cat`). 
Please share [inputs and comments](https://github.com/bico-media/bcat/issues).

This protocol is only relevant if the data is larger than the limit of the current transaction size. If data fits within one transaction **always use the [B://](https://b.bitdb.network) format** for storing your data. With the current limit of 100KB per transaction, a regular B-cat transaction can represent a file with roughly 310MB of data (110 Gb if the content gateway supports the `nested-gzip` flag).

## Description

### Concatenating data

Transactions on the blockchain that includes the bitcom namespace of `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` at the first position after an `OP_RETURN` code shall be called a `B-cat` transactions. 

The `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` bitcom namespace must have 7 or more arguments to be valid:
1. A string providing the `MIME type` of the content of the file. No longer than 64 characters
2. A string providing the `charset`/encoding of the file. No longer than 16 characters (can be an empty string but defaults to 'UTF-8')
3. A string providing the `name` of the file. No longer than 256 characters (can be an empty string)
4. A string providing a `flag` indicating how to treat data. No longer than 16 characters (can be an empty string)
5  A string providing any unstructured `info` the sender finds relevant to share about how the B-cat transaction came to life. No longer than 128 characters (can be an empty string)
6. 32 bytes representing the (raw) transaction ID of the transaction with the first part of data of the file (`TX1`)
7. 32 bytes representing the (raw) transaction ID of the transaction with the second part of data of the file (`TX2`)

Any number of arguments can follow providing a sequence of transaction IDs (`TXn`) in the order of how data is to be represented in the file. To be a valid B-cat transaction all transaction IDs (`TX1`, `TX2`, ... `TXn`) must be unique, unless using the `creative` flag.

### Parts of the data

Transactions referenced from a B-cat transaction will be called a `B-cat reference`. Each B-cat reference transaction must follow the [B://](https://b.bitdb.network) specification or `B-cat part` transactions as described in the following:

Transactions on the blockchain that include the bitcom namespace of `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` at the first position after an `OP_RETURN` code shall be called a `B-cat part` transactions. 

The `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` bitcom namespace must have 1 argument only:
1. Raw data

Use B:// protocol to be creative with existing content. Avoid using the B:// format if the data does not represent a whole file as data is not meant to make sense outside of the B-cat context (like linking directly to the transaction). 

Please note that some flags expand the list of supported formats for B-cat references.

### Flags

The 3rd argument to a B-cat transaction is `flag` and contains a string with information about how to deal with data. Only one flag can be provided. A content provider is free to choose to implement support for each flag or not. It is recommended to provide a useful error messages if a valid but unsupported flag is used.



#### The `base64` flag

A content gateway can decide to support B-cat transactions with the `base64` flag by ensuring that 
the combined content is base64 decoded before processed further. 


#### The `gzip` flag

A content gateway can decide to support B-cat transactions with the `gzip` flag by ensuring that 
a B-cat transaction with this flag will have its content provided to the requesting client with **no further processing of the content** and the http response includes `Content-Encoding: gzip` as part of the header. Please note that the first argument `MIME type` must contain the MIME type of the original data.

Providing the `gzip` flag is recommended for all data that is not to be merged with other data. 


#### The `nested-gzip` flag

A content gateway can decide to support B-cat transactions with the `nested-gzip` flag by ensuring that 
a B-cat transaction with this flag will be treated as if it had the `gzip` flag with the addition that along with the B:// and B-cat part format for referenced transactions any B-cat transaction with the `gzip` flag set will also be valid. 


#### The `creative` flag

A content gateway can decide to support B-cat transactions with the `creative` flag by ensuring that 
a B-cat transaction with this flag is still valid even if the referenced transaction ID's are not unique. 




### Error handling

To ensure compatibility it is important that any B-cat transaction that is not valid is presented as an error to the client. This includes

- Any argument longer than specified
- Less than 7 arguments
- Reuse of referenced TX's (Except when using `creative` flag )
- Any referenced TX is not in B:// or B-cat part format (exempt addition described for `nested-gzip`)'



## Definition

If you provide content from the blockchain you are compatible with the B://cat protocol if

- A client requesting the content of a B-cat transaction will receive content based on the concatenated data from `TX1`, `TX2` ... `TXn`.

- The concatenation of data from `TX1`, `TX2` ... `TXn` is binary safe.

- The handling of the concatenated data honours the description of the (optional) `flag` provided. 


----

Please share [inputs and comments](https://github.com/bico-media/bcat/issues).
