# SPL Tokens

The spl tokens represent the non-native tokens on the solana network.

Both non_fungible tokens and fungible tokens on the solana are considered as the SPL tokens.

Solana provides a `Token Program` which is a native program that helps us with creation and interaction with existing and new spl tokens.

## Token Mints and Token Accounts

unlike the ethereum that the ERC-20 and ERC-721 tokens were having only one contract that would hold the data of the token, its owner and the share holders of the token, the solana prefers a account abstraction approach as its underlying infrastructure suggest.

The `Mint Accounts` hold the mint and freeze authorization information and the `Token Accounts` are the main accounts that are holding the token information account, they can hold token balances, they will hold their owner and only the owner of the token accounts is allowed to reduce the token balance o the account like burning or transferring the tokens.


# Creating a spl token 

We will first talk about the concepts onvolved in this process and then we will write the client side code of the creation and interaction with the spl tokens.

## 1. Creating the mint account

A spl token must have an mint account that will hold the following information about the token: 

- Mint Authority: its the public key that is allowed to issue new supplies for the token.

- Freeze Authority: its the public key that is allowed to freeze the token operations.

- Decimal precision: The decimals of the token

### Rent Exemption: 

Any type of account on the solana must pay for the hardware the its information is being saved on, the following approaches demonstrates the methodes that the solana networks has provided for the individual to do so: 

- Pay rent in specific intervals (deprecated)

- Deposit a certain amount of SOL at account creation time to be considered as the rent exemption account.

The  mint account are not separate from this rule and we have to deposit a certain amount of sol to the mint account in order to have an activated and rent exemption (mint) account.

> this certain amount of sol is retrievable by using the `getMinimumBalanceForRentExemptMint` method of the jrpc connection object.  

## 2. Creating Token Accounts

now that we have created the mint account we will be needin to mint tokens, but before minting token we nedd an token account to hold the minted tokens information.


## 3. Creating the Associated token accounts for transferring tokens between the users

An associated token in context of the spl tokens is an derived token account address using the owner of the token account and the token account program ids.

the associated token accounts are deterministic and help users to keep the tokens account pretty simple.

most of the time wat we need is an associated token account for example if wanna have a share of a already existing token we do not need to deploy the mint and the main token account, we just need a associated token account.

the associated token account prevents the confusion about the token account address custody.