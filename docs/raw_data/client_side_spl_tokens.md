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