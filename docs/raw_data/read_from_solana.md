# SOL

`SOL` is the name of the native coin of the solana network.

# Accounts

Accounts on solana can store: 

- SOL
- Tokens (USDC and etc) 
- NFTS
- programs(executable codes)
- program data



# lamports

lamports are the smallest part of each sol, like the `satoshi` for bitcoin, there is `lamports` for sol, every 1 million lamports will represent one `SOL`.

# Addresses

in the previous section we mentioned that the public keys are know as the addresses in the solana and each address will point to an account in the solana network.
The addresses are commonly shown as a base 58 encoded string: 

> `B4QE2HhrnaHJPGxnuiHEbX3tkvhdrfBXyeHdA3CncjhJ`


# Reading data from the solana network

Reading any data from the network will happen through a `Connection` Object from the `@solana/web3.js` library that we installed earlier.

The connection object object stablish a connection to certain cluster of the network.

The clusters are including `Devnet`, `Mainnet` and `Testnet`.

> [Reading from the network Code Sample](./code_samples/ts_code_samples.md#reading-from-the-network) 

