Status: _idea_ → _lose draft_ → _draft_ → __proposal__ → _final review_ → _stable_

# Concatenate content from the blockchain

> Let's get a common way of providing large files by having data spread amongst transactions.

This document describes a protocol named "B://cat" (pronounced `B cat`). 
Please share [inputs and comments](https://github.com/bico-media/bcat/issues).

This protocol is only relevant if the data is larger than the limit of the current transaction size. If data fits within one transaction **always use the [B://](https://b.bitdb.network) format** for storing your data. With the current limit of 100KB per transaction, a B-cat transaction can represent a file with roughly 310MB of data.

## Description

### Concatenating data

Transactions on the blockchain that includes the bitcom namespace of `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` at the first position after an `OP_RETURN` code shall be called a `B-cat` transactions. 

The `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` bitcom namespace must have 7 or more arguments to be valid:
1. A string providing the `MIME type` of the content of the file no longer than 64 characters
2. A string providing the `charset`/encoding of the file no longer than 16 characters (can be an empty string but defaults to 'UTF-8')
3. A string providing the `name` of the file no longer than 256 characters (can be an empty string)
4. A string providing a `flag` indicating how to treat data no longer than 16 characters (can be an empty string)
5  A string providing any `info` that the sender finds relevant to the creation of the B-cat transaction no longer than 128 characters (can be an empty string)
6. 32 bytes representing the (raw) transaction ID of the transaction with the first part of data of the file (`TX1`)
7. 32 bytes representing the (raw) transaction ID of the transaction with the second part of data of the file (`TX2`)

Any number of arguments can follow providing a sequence of transaction IDs (`TXn`) in the order of how data is to be represented in the file. To be a valid B-cat transaction all transaction IDs (`TX1`, `TX2`, ... `TXn`) must be unique, unless using the `creative` flag.

### Parts of the data

Transactions referenced from a B-cat transaction will be called a `B-cat reference`. Each B-cat reference transaction must follow the [B://](https://b.bitdb.network) specification or the following description:

Transactions on the blockchain that includes the bitcom namespace of `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` at the first position after an `OP_RETURN` code shall be called a `B-cat part` transactions. 

The `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` bitcom namespace must have 1 argument only:
1. Raw data


Use B:// protocol to be creative with existing content. Avoid using the B:// format if the data does not represent a whole file as data is not meant to make sense outside of the B-cat context (like linking directly to the transaction). 


### Flags

The 3rd argument to a B-cat transaction is `flag` and contains a string with information about how to deal with data. Only one flag can be provided.


#### The `base64` flag

When a B-cat transaction has the hint `base64` the combined content must be base64 decoded before processed further. 


#### The `inflate-gzip` flag

When a B-cat transaction has the hint `inflate-gzip` the combined content must be inflated using the gzip before processed further.


#### The `gzip` flag

When a B-cat transaction has the hint `gzip` the combined content must represent a gzip'ed version of the original content. The combined content must be provided to the requesting client with no further processing of the content and the http response must include `Content-Encoding: gzip` as part of the header. Please note that the first argument `MIME type` must contain the MIME type of the original data.

Providing the `gzip` flag is recommended for all data that is not to be merged with other data. 



## Definition

If you provide content from the blockchain you are compatible with the B://cat protocol if

- A client requesting the content of a B-cat transaction will receive content based on the concatenated data from `TX1`, `TX2` ... `TXn`.

- The concatenation of data from `TX1`, `TX2` ... `TXn` is binary safe.

- The handling of the concatenated data honours the description of the (optional) `flag` provided. 



----

Please share [inputs and comments](https://github.com/bico-media/bcat/issues).
