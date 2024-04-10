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

> to mint the tokens, only th authority of the mint specified in the mint account is allowed, check out the rust code below to see how the ownership is checked

``` rust
    // @param the accounts is the key array passed from the off-chain program.
    // @dev the is_signer param specifies the associated account has the relevant secret key signature in the tx.
  for account in accounts.iter() {
        if account.is_signer && account.key == signer_pubkey { // Replace with your logic to identify required signer
            found_signer = true;
            break;
        }
    }
```

## 4. Transferring the tokens

The transferring operation is pretty straight forward and is done through a simple api but the only thing to note here is that the source and the destination to transfer from and to are the token accounts and not the wallets public keys.

## 5. approve delegate

the owner of the token account can delegate the allowance of the transfer and the burn operations of the certain amount of the token to another person through this functionality.

notice that the certain amount that is previously approved as the delegate can later be revoked and any remaining amount of token wont be accessible since then.

another thing to notice is that there can only be one delegate approved account at any given time per each token account.


> [SPL token full operations Code Sample](./code_samples/ts_code_samples.md#spl-token-full-operations) 


# Non-Fungible Tokens

The non-fungible tokens on the solana, also known as spl tokens(yes, both fungible and non-fungible tokens on solana are called spl tokens) offer their main functionality with a different approach, in a way that they store the information of the assets associated with their ownership using an external tool and standard which is using the metaplex standard. 

The non-fungible tokens are non-divisible in contrary of the fungible tokens.

each nft token can have maximum supply of one.

The nfts are not having any decimals.

The Nfts mint authority are set to null to grant that its total supply never changes.

Each Nft has an separate metadata account which stores the metadata information.



## Metaplex

`Metaplex offers a collection of tools that will help us to create, distribute and interact with the Nfts on the solana blockchain.`

the metaplex will also help us with the fungible tokens metadata storage and representation.

The meta plex also offers a specific program, called `Token Metadata` which standardize the process of attaching the metadata to the nft.
The `Sugar CLI` simplifies the process of the uploading the medai/metadata files and creating a candy machine.

The `Candy Machine` helps the developers to deploy and interact with the NFTs through a user friendly api.

The `Metaplex SDK` is a tool that offers user-friendly APIs to assist developers in utilizing the onchain tools provided by Metaplex.


## Metadata

The metadata is simply an json object that will indicate the information of the associated asset with the specific nft, it can be a link to the related IPFS link of an digital art. it can be a signature of the ownership of a any type of digital or physical asset.

The metadata can include both on and off chain components.


## Metaplex instances

The metaplex instances acts as a entry point between the user and SDKs apis, the SDK accepts a connection to send requests to the cluster.

The developer can customize the interactions with specifying as an `Identifying Driver` or a `Storage Deriver`.

The Identifying Deriver is essentially an keypair for singing the transaction associated with the nft, like transferring it.'

The Storage Deriver is specifying the storage services that you want to use for uploading the assets.

> The `bundlrStorage` driver is the default option and it uploads assets to Arweave, a permanent and decentralized storage service.


Check out the code samples below which represent the manual process of preparing and deploying a collection of nft:

> [Manual NFT Creation And Interaction data Code Sample](./code_samples/ts_code_samples.md#serializing-the-custom-data) 

Check out the code sample below to see how the candy machine sugar cli comes to play when we are talking about scale !: 

> [Utilizing the Candy machine and Sugar CLI data Code Sample](./code_samples/ts_code_samples.md#serializing-the-custom-data) 
