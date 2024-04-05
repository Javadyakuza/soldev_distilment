# Transactions

Any modifications to the on-chain data is done through transactions in the solana network.

Transactions are atomic like ethereum.

Transaction are just set of `Instructions` that will invoke the solana programs.

# Instructions

The instructions are containing:

- An array of accounts that the transaction is going to read from or write to. the reason for getting this accounts before infracting with them is because solana checks the read accounts are going to be modified or not in the transaction and if not the underlying data wont be touched, so the vm can process the read and write request at the same time and that's why the solana is blazing fast !

- The public key of the main program that the transaction is being sent to.

- The data related to the operations associated to the transaction in terms of a byte array.

# Transferring SOL

In the provided code sample we will try to send some `SOL` from our account to another account.

The key points to note here is that the transactions on solana are having fees just like any other chain.

The fee of the transaction is payed by the first account that is considered as the signer of the transaction.

The below code sample demonstrates how to transfer SOL tokens from one to another account in the solana dev or test net: 

> [Transferring SOL Code Sample](./code_samples/ts_code_samples.md#transferring-sol) 



# Interacting to the custom on-chain programs
