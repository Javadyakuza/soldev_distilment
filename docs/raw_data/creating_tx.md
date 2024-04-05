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

The solana native and default programs are having a simple and a pre-defined instruction, how ever working with the custom programs requires the custom transactions instructions which must be prepared pretty wisely.

How ever the the instruction structure will remain the same as following:

- keys (the account that the transaction or the calling program will interact to , those means that you must predict the possible intractable accounts) 
- ProgramId ( the target program public key)
- data (an array of bytes called buffer)

> the data field will be disused later in this course. 

## keys

The keys array is containing the interacting program ids as objects, each object must contain and fill the following fields:
- pubkey
- isSigner
- isWritable

## data account

Later in this course we will dive deeper in the accounts architecture of the solana network, but for now lets just have a big picture of the accounts in the solana network.

In the simple words the each program has two accounts in most cases: 
- program account 
- data account
The program account will save the executable code and the logic of the program and the data accounts will save the state of the program.
imagine a counter program, is the simplest case there is a program account that will hold a function that is having the duty of increasing a variable and the variable is held in the data account.

as a result we know that if want to increment a number using that program we need to include the data account address in the account array of the instruction as well because the program account will interact with that account as well.


# Creating a read instruction for a counter program 

in the following code sample we will create a custom instruction that os containing nothing but a empty request.

> [Reading from a custom program Code Sample](./code_samples/ts_code_samples.md#read-from-custom-program) 

