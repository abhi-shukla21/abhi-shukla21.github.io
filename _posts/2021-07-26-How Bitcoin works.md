### Transactions, Blocks, Mining, and the Blockchain

##### Bitcoin Overview

![image-20210624133629151](https://raw.githubusercontent.com/abhi-shukla21/Mastering_Bitcoin/master/img/image-20210624133629151.png)

The bitcoin system consists of:

- A user with wallet containing keys
- A transaction that propagates through the bitcoin network
- The miners who produce the consensus blockchain.

A *blockchain explorer* is a web app that operates as search engine of bitcoin. BlockchainCypher Explorer, blockchain.info,  BitPay Insight



#### Bitcoin Transactions

A transaction is simply an entry on the blockchain that indicates that the owner of that bitcoin has transferred the ownership of that value to another user who can spend it further by creating another transaction.

###### Transaction Input and output:

Input is the amount of BTC debited from one account and output is the amount of BTC credited to another account. Output is lesser than the output and the difference is the transaction fee which is collected by the miner for including the transaction in the ledger.

Transaction also contains a proof-of-ownership in the form of a digital signature.

###### Transaction Chain:

One transaction's output is input to the next transaction. Thereby forming a transaction chain. (Think ownership of the same BTC being transferred from person to person)

###### Making Change:

Sometimes, the output of a transaction contains both new users address and current user's address. Making it a *change*. Exactly, how you expect a change back when dealing with currency notes. For security reasons, the change address is often a different address pointing to owner's wallet.

###### Common Transaction Forms: 

- One input, two output (payment and change)
- Many input, one output. (Exchanging a pile of coin for a larger not) 
- One input, many output (e.g. payroll payments)



### Constructing a Transaction

A wallet application can construct transactions even if it's completely offline.

###### Getting right inputs 

The wallet application can have all the previous outputs of all the users (full-client) or can user related outputs or can retrieve it from bitcoin explorer using the user's address. It can then choose the one that fits for this transaction

###### Creating the outputs



#### Adding the Transaction to the Ledger

A txn is 258 bit long is transferred to the blockchain

###### Transmitting the transaction

It doesn't matter how and where the txn is transmitted to the BTC n/w. The BTC n/w is a P2P n/w. The purpose of the BTC n/w is to propagate txn to all participants.

###### How it propagates

Any BTC node that receives a txn it has not seen before will forward it to all other nodes it's connected to (*flooding*).



#### Bitcoin Mining

A txn does not become a part of *blockchain* until it is verified and included in a block by a process called *mining*.

- Validates the transaction using bitcoin's *consensus rules*.
- Adds new bitcoins to the block.

#### Mining transactions in Blocks

