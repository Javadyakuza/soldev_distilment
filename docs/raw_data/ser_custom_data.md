# Serializing a custom instruction data 

Lets talk about the instruction inside of the transactions, the instructions are just a array of bytes inside of a transaction object.

a transaction can have any number of instructions, each one of them targeting its own program.

and as mentioned earlier the transaction in the solana network are atomic.

on the other hand the transactions are the the way that we send the information to the blockchain in order for the vm to be able to proceed them.

# Serialize

The solana programs are written in rust.

the instruction will trigger the functions defined within that program.

The functions in that program will accept a struct which is a data type that stores in a key value like method.

the `borsh` provides the functionality of creating the schema of the accepting struct and serializing its related value into a buffer.

the borsh is available both in the rust language as a crate and in the js as a lib.

> [Serializing a custom data Code Sample](./code_samples/ts_code_samples.md#serializing-the-custom-data) 

