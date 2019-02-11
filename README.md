Status: _idea_ → _lose draft_ → **draft** → _proposal_ → _final review_ → _stable_

# Concatenate content from the blockchain

> Let's get a common way of providing large files by having data spread amongst transactions.

This document describes a protocol named "B://cat" (pronounced `B-cat`). 
Please share [inputs and comments](https://github.com/bico-media/b-cat/issues).

This protocol is only relevant if the data is larger than the limit of the current transaction size. If data fits within one transaction **always use the [B://](https://b.bitdb.network) format** for storing your file. 


## Description

### Concatenating data

Transactions on the blockchain that includes the bitcom namespace of `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` at the first position after a `OP_RETURN` code shall be called a `B-cat` transactions. 

The `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` bitcom namespace must have 6 or more arguments:
1. A string providing the MIME type of the content in the file
2. a string providing the encoding/charset of the file.
3. A string providing the name of the file  (can be an empty string)
4. A string providing a hint about how to treat the data (can be an empty string)
5. Transaction ID of a transaction with the first part of data of the file (`TX1`)
6. Transaction ID of a transaction with the second part of data of the file (`TX2`)

Any number of arguments can follow providing a sequence of transaction IDs (`TXn`) in the order of how data is to be represented in the file. 

### Chunks of data

Transactions referenced from a B-cat transaction will be called `B-cat parts`. Each B-cat part transaction must follow the [B://](https://b.bitdb.network) specification or the following description:

Transactions on the blockchain that includes the bitcom namespace of `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` at the first position after a `OP_RETURN` code shall be called a `bat-chunk` transactions (pronounced _bachunk_). 

The `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` bitcom namespace must have 1 argument only:
1. data


Use B:// protocol to be creative with existing content. Avoid using the B:// format if the data does not represent a whole part i.e.: it does not make sense for others to treat data as a file of the B-cat context (like linking directly to the transaction). 


### Hints

The 3rd argument to a B-cat transaction is `hint` and contains a string with information about how to deal with data after it has been concatenated. At the moment there is only one possible hint: `base64`.

#### Hinting `base64`

When a B-cat transaction gives the hint `base64` the content must be base64 decoded before processed further. 


## Definition

If you provide content from the blockchain you are compatible with the B://cat protocol if

- A client requesting the content of a B-cat transaction will receive the concatenated data from `TX1`, `TX2` ... `TXn`.

- The concatenation of data from `TX1`, `TX2` ... `TXn` is binary safe.

- The concatenated data has been subject to any transformation indicated in (`hint`) of the requested transaction.



----

Please share [inputs and comments](https://github.com/bico-media/b-cat/issues).
