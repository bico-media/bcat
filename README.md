Status: draft

# Concatenate content from the blockchain

> Let's get a common way of providing large files by having data spread amongst transactions.

This document describes a protocol named "B://at" (pronounced _bat_). 
Please share [inputs and comments](https://github.com/bico-media/bat/issues).

Please note that this protocol is only relevant if the data larger than the limit of transactions size. If data fits within one transaction **always use the [B://](https://b.bitdb.network) format** for storing your file. 


## Description

**This is to be discussed - it might be smarter to chain B:// files**

### Concatenating data

Transactions on the blockchain that includes the bitcom namespace of `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` at the first position after a `OP_RETURN` code shall be called a `bat` transactions. 

The `15DHFxWZJT58f9nhyGnsRBqrgwK4W6h4Up` bitcom namespace must have 5 or more arguments:
1. A string providing the MIME type of the file
2. A string providing the name of the file
3. A string providing a hint about how to treat the data
4. Transaction ID of a transaction with the first chunk of data of the file (`TX1`)
5. Transaction ID of a transaction with the second chunk of data of the file (`TX2`)

Any number of arguments can follow providing a sequence of transaction IDs (`TXn`) in the order of how data is to be represented in the file. 

### Chunks of data

The transaction containing the chunks of data must follow the [B://](https://b.bitdb.network) specification or the following description:

Transactions on the blockchain that includes the bitcom namespace of `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` at the first position after a `OP_RETURN` code shall be called a `bat-chunk` transactions (pronounced _bachunk_). 

The `1ChDHzdd1H4wSjgGMHyndZm6qxEDGjqpJL` bitcom namespace must have 1 argument only:
1. data


## Definition

If you provide content from the blockchain you are compatible with the B://at protocol if

- A client requesting the content of a bat transaction will receive the concatenated data from `TX1`, `TX2` ... `TXn` after being treated by hints provided in the requested tx. 

- The concatenation of data from `TX1`, `TX2` ... `TXn` is binary safe



### Hints

The 3rd argument to a bat transaction is `hint` and contains a string with a comma-separated list of instructions in the sequence of how to deal with data after it has been concatenated. At the moment there is only one possible hint: `base64`.

#### Hinting `base64`

When a bat transaction gives the hint `base64` the content must be base64 decoded before processed further. 

----

Please share [inputs and comments](https://github.com/bico-media/bat/issues).
