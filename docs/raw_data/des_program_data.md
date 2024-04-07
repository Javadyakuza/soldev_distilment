# Deserialize a program data 

the data account that is used to save the datand not the executable code of the programs are called `PDA` which stands for the `program derived address`.

the PDAs are not having the keypair for the authentication of the operations that they have to do.

only the associated program with that PDA can modify the data of that PDA.

we can implement the logic and the condition of the modification of the PDA data inside of the program code.

the PDAs addresses are derived from their associated program id and a seed.

The PDAs can be used to implement the account abstraction approach for any specific need. meaning an data account per each user.


# Deserialization

The following operation is much like the serialization, such a that we need a struct and the encoded data, the borsh will decode the data based on the given struct for us.

> [Deserializing a custom data Code Sample](./code_samples/ts_code_samples.md#) 
