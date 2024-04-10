# Creating a keypair

``` shell 
npm i @solana/web3.js
```

``` typescript

import { Keypair } from "@solana/web3.js";

const keypair = Keypair.generate();

console.log(`The public key is: `, keypair.publicKey.toBase58());
console.log(`The secret key is: `, keypair.secretKey);

```

# Loading an existing keypair

``` shell 
npm i @solana-developers/helpers
```

``` typescript
import "dotenv/config";

import { getKeypairFromEnvironment } from "@solana-developers/helpers";



const keypair = getKeypairFromEnvironment("SECRET_KEY");

```


# Reading from the network

``` typescript
import { Connection, PublicKey, clusterApiUrl, LAMPORTS_PER_SOL } from "@solana/web3.js";

const connection = new Connection(clusterApiUrl("devnet"));
const address = new PublicKey('CenYq6bDRB7p73EjsPEpiYN7uveyPUTdXkDkgUduboaN');
const balance = await connection.getBalance(address);
const balanceInSol = balance / LAMPORTS_PER_SOL;

console.log(`The balance of the account at ${address} is ${balanceInSol} SOL`); 
console.log(`✅ Finished!`)

```
# Transferring SOL

``` typescript

import { airdropIfRequired } from "@solana-developers/helpers";
import { LAMPORTS_PER_SOL, Connection, SystemProgram, Transaction, clusterApiUrl, sendAndConfirmTransaction, PublicKey, Keypair } from "@solana/web3.js";

const connection = new Connection(clusterApiUrl("devnet"));

const transaction = new Transaction()

const amount = 1;

const sender = Keypair.generate();
const recipient = Keypair.generate();

// air drop the newly created sender account to cover the fees and transfer from its sol to the recipient account.
await airdropIfRequired(
    connection,
    sender.publicKey,
    2 * LAMPORTS_PER_SOL,
    0.5 * LAMPORTS_PER_SOL,
);

// creating the transaction
const sendSolInstruction = SystemProgram.transfer({
  fromPubkey: sender.publicKey,
  toPubkey: recipient.publicKey,
  lamports: LAMPORTS_PER_SOL * amount
})

// adding the new tx data to the tx object, we can add more than one tx to the obj as well.
transaction.add(sendSolInstruction)

// sending and confirming the transaction
const signature = sendAndConfirmTransaction(
  connection,
  transaction,
  [sender]
)

```

# Read from custom program

``` typescript
    const connection = new Connection(clusterApiUrl("devnet"));

    // let conn = new Connection("http://localhost:8899", "confirmed");

    // loading the payer of the transaction
    let payer = await read_kp();

    await airdropIfRequired(
        connection,
        payer.publicKey,
        1 * LAMPORTS_PER_SOL,
        0.5 * LAMPORTS_PER_SOL,
    );
    // static addresses
    const PROGRAM_ADDRESS = new web3.PublicKey('ChT1B39WKLS8qUrkLvFDXMhEJ4F1XZzwUNHUt4AU9aVa')
    const PROGRAM_DATA_ADDRESS =  new web3.PublicKey('Ah9K7dQ8EHaZqcAsgBW8w37yN2eAy3koFmUn4x3CJtod');

    const transaction = new web3.Transaction()
    const programId = new web3.PublicKey(PROGRAM_ADDRESS)
    const ProgramDataId = new web3.PublicKey(PROGRAM_DATA_ADDRESS)

    // creating the instruction  
    const instruction = new web3.TransactionInstruction({
      keys: [
        {
          pubkey: ProgramDataId,
          isSigner: false,
          isWritable: true
        },
      ],
      programId
    })

    // adding the instruction to the transaction object
    transaction.add(instruction)
    


    const signature = await web3.sendAndConfirmTransaction(
      connection,
      transaction,
      [payer]
    )
    
    console.log(`✅ Transaction completed! Signature is ${signature}`)
    
```

# Serializing the custom data

``` typescript 
import * as borsh from '@coral-xyz/borsh'
import * as web3 from '@solana/web3.js'


export async function seriallize_test_data(): Promise<String> {

    // defining the struct of the accepting input
    const equipPlayerSchema = borsh.struct([
      borsh.u8('variant'),
      borsh.u16('playerId'),
      borsh.u256('itemId')
    ])

    // creating an array of bytes with a fixed length of 1000 bytes.
    const buffer = Buffer.alloc(1000)
    
    // encoding the values with the defined schema
    equipPlayerSchema.encode({ variant: 2, playerId: 1435, itemId: 737498 }, buffer)

    // separating the encoded bytes from the untouched ( 0 ) rest of the bytes
    const instructionBuffer = buffer.slice(0, equipPlayerSchema.getSpan(buffer))
    
    // the cluster
    const endpoint = web3.clusterApiUrl('devnet')
    
    // stablishing a connection
    const connection = new web3.Connection(endpoint)
    
    // creating a new transaction object
    const transaction = new web3.Transaction()
    
    // static addresses

    /// @dev the normal keypair of the player
    const player = web3.Keypair.generate();

    /// @dev the target and the platforms program must generate an account for the user and save the data in that, the user must retrieve that address from the program separately 
    const playerInfoAccount = web3.PublicKey.unique();

    // the target and starting programs address
    const PROGRAM_ID = web3.PublicKey.default;
    
    // creating a instruction with the encoded data, the key sets and the target and starting program id.
    const instruction = new web3.TransactionInstruction({
      keys: [
        {
          pubkey: player.publicKey,
          isSigner: true,
          isWritable: false,
        },
        {
          pubkey: playerInfoAccount,
          isSigner: false,
          isWritable: true,
        },
        {
          pubkey: web3.SystemProgram.programId, 
          isSigner: false,
          isWritable: false,
        }
      ],
      data: instructionBuffer,
      programId: PROGRAM_ID
    })
    
    transaction.add(instruction)
    
    web3.sendAndConfirmTransaction(connection, transaction, [player]).then((txid) => {
      console.log(`Transaction submitted: https://explorer.solana.com/tx/${txid}?cluster=devnet`)
    })
    return "";

}

```


# Deserializing the custom data

``` typescript 
import * as borsh from '@coral-xyz/borsh'
import * as web3 from '@solana/web3.js'


export async function seriallize_test_data(): Promise<String> {

    // defining the struct of the accepting input
    const equipPlayerSchema = borsh.struct([
      borsh.u8('variant'),
      borsh.u16('playerId'),
      borsh.u256('itemId')
    ])

    // creating an array of bytes with a fixed length of 1000 bytes.
    const buffer = Buffer.alloc(1000)
    
    // encoding the values with the defined schema
    equipPlayerSchema.encode({ variant: 2, playerId: 1435, itemId: 737498 }, buffer)

    // separating the encoded bytes from the untouched ( 0 ) rest of the bytes
    const instructionBuffer = buffer.slice(0, equipPlayerSchema.getSpan(buffer))
    
    // the cluster
    const endpoint = web3.clusterApiUrl('devnet')
    
    // stablishing a connection
    const connection = new web3.Connection(endpoint)
    
    // creating a new transaction object
    const transaction = new web3.Transaction()
    
    // static addresses

    /// @dev the normal keypair of the player
    const player = web3.Keypair.generate();

    /// @dev the target and the platforms program must generate an account for the user and save the data in that, the user must retrieve that address from the program separately 
    const playerInfoAccount = web3.PublicKey.unique();

    // the target and starting programs address
    const PROGRAM_ID = web3.PublicKey.default;
    
    // creating a instruction with the encoded data, the key sets and the target and starting program id.
    const instruction = new web3.TransactionInstruction({
      keys: [
        {
          pubkey: player.publicKey,
          isSigner: true,
          isWritable: false,
        },
        {
          pubkey: playerInfoAccount,
          isSigner: false,
          isWritable: true,
        },
        {
          pubkey: web3.SystemProgram.programId, 
          isSigner: false,
          isWritable: false,
        }
      ],
      data: instructionBuffer,
      programId: PROGRAM_ID
    })
    
    transaction.add(instruction)
    
    web3.sendAndConfirmTransaction(connection, transaction, [player]).then((txid) => {
      console.log(`Transaction submitted: https://explorer.solana.com/tx/${txid}?cluster=devnet`)
    })
    return "";

}

```

# SPL Token Full Operations

``` typescript
import * as web3 from "@solana/web3.js";
import * as token from '@solana/spl-token'
import { InitializeKeypairOptions, initializeKeypair } from "@solana-developers/helpers";

async function createNewMint(
    connection: web3.Connection,
    payer: web3.Keypair,
    mintAuthority: web3.PublicKey,
    freezeAuthority: web3.PublicKey,
    decimals: number
  ): Promise<web3.PublicKey> {
  
    const tokenMint = await token.createMint(
      connection,
      payer,
      mintAuthority,
      freezeAuthority,
      decimals
    );
  
    console.log(
      `Token Mint: https://explorer.solana.com/address/${tokenMint}?cluster=devnet`
    );
  
    return tokenMint;
  }

async function createTokenAccount(
    connection: web3.Connection,
    payer: web3.Keypair,
    mint: web3.PublicKey,
    owner: web3.PublicKey
  ) {
    const tokenAccount = await token.getOrCreateAssociatedTokenAccount(
      connection,
      payer,
      mint,
      owner
    )
  
    console.log(
      `Token Account: https://explorer.solana.com/address/${tokenAccount.address}?cluster=devnet`
    )
  
    return tokenAccount
}

async function mintTokens(
    connection: web3.Connection,
    payer: web3.Keypair,
    mint: web3.PublicKey,
    destination: web3.PublicKey,
    authority: web3.Keypair,
    amount: number
  ) {
    const transactionSignature = await token.mintTo(
      connection,
      payer,
      mint,
      destination,
      authority,
      amount
    )
  
    console.log(
      `Mint Token Transaction: https://explorer.solana.com/tx/${transactionSignature}?cluster=devnet`
    )
}

async function approveDelegate(
    connection: web3.Connection,
    payer: web3.Keypair,
    account: web3.PublicKey,
    delegate: web3.PublicKey,
    owner: web3.Signer | web3.PublicKey,
    amount: number
  ) {
    const transactionSignature = await token.approve(
      connection,
      payer,
      account,
      delegate,
      owner,
      amount
    )
  
    console.log(
      `Approve Delegate Transaction: https://explorer.solana.com/tx/${transactionSignature}?cluster=devnet`
    )
}

async function transferTokens(
    connection: web3.Connection,
    payer: web3.Keypair,
    source: web3.PublicKey,
    destination: web3.PublicKey,
    owner: web3.Keypair,
    amount: number
  ) {
    const transactionSignature = await token.transfer(
      connection,
      payer,
      source,
      destination,
      owner,
      amount
    )
  
    console.log(
      `Transfer Transaction: https://explorer.solana.com/tx/${transactionSignature}?cluster=devnet`
    )
}

async function revokeDelegate(
    connection: web3.Connection,
    payer: web3.Keypair,
    account: web3.PublicKey,
    owner: web3.Signer | web3.PublicKey,
  ) {
    const transactionSignature = await token.revoke(
      connection,
      payer,
      account,
      owner,
    )
  
    console.log(
      `Revote Delegate Transaction: https://explorer.solana.com/tx/${transactionSignature}?cluster=devnet`
    )
}

async function burnTokens(
    connection: web3.Connection,
    payer: web3.Keypair,
    account: web3.PublicKey,
    mint: web3.PublicKey,
    owner: web3.Keypair,
    amount: number
  ) {
    const transactionSignature = await token.burn(
      connection,
      payer,
      account,
      mint,
      owner,
      amount
    )
  
    console.log(
      `Burn Transaction: https://explorer.solana.com/tx/${transactionSignature}?cluster=devnet`
    )
}

export async function full_spl_ops() {

    const connection = new web3.Connection("http://localhost:8899", "confirmed")

    const alice = await initializeKeypair(connection);
    const bob = await initializeKeypair(connection);

    console.log(`test users initiated ✅ \nalice publickey: ${alice.publicKey.toString()} \nbob publickey: ${bob.publicKey.toString()}`)


    const mint = await createNewMint(
      connection,
      alice,
      alice.publicKey,
      alice.publicKey,
      2
    )
    

    const mintInfo: token.Mint = await token.getMint(connection, mint);

    console.log(`created the new mint account ✅ \n Mint Account address`, mintInfo.address);

    const aliceTokenAccount = await createTokenAccount(
      connection,
      alice,
      mint,
      alice.publicKey
    )

    let alice_token_account_info: token.Account = await token.getAccount(connection, aliceTokenAccount.address);

    console.log(`created the new token account for alice ✅ \n alice Token Account address: `, alice_token_account_info.address.toString());

    const bobTokenAccount = await createTokenAccount(
        connection,
        bob,
        mint,
        bob.publicKey
      )
  
      let bob_token_account_info: token.Account = await token.getAccount(connection, bobTokenAccount.address);
  
      console.log(`created the new token account for bob ✅ \n Bob Token Account address: `, bob_token_account_info.address.toString())

    await mintTokens(
      connection,
      alice,
      mint,
      aliceTokenAccount.address,
      alice,
      100 * 10 ** mintInfo.decimals
    )
    
    alice_token_account_info = await token.getAccount(connection, aliceTokenAccount.address);


    console.log("Minted tokens for alice, new amount: ", alice_token_account_info.amount.toString());

    const delegate = bob;
    
    await approveDelegate(
      connection,
      alice,
      aliceTokenAccount.address,
      delegate.publicKey,
      alice.publicKey,
      50 * 10 ** mintInfo.decimals
    )
    
        
    await transferTokens(
      connection,
      bob,
      aliceTokenAccount.address,
      bobTokenAccount.address,
      delegate,
      30 * 10 ** mintInfo.decimals
    )
    
    alice_token_account_info = await token.getAccount(connection, aliceTokenAccount.address);
    bob_token_account_info = await token.getAccount(connection, bobTokenAccount.address);



    console.log("transferred 50 tokens from alice to bob, alice balance : ", alice_token_account_info.amount.toString(), "bob balance: ", bob_token_account_info.amount.toString());

    
    // if we uncomment the following lines the burn tokens part will not work
    // await revokeDelegate(
    //   connection,
    //   alice,
    //   aliceTokenAccount.address,
    //   bob.publicKey,
    // )
    
    
    await burnTokens(
      connection, 
      bob, 
      aliceTokenAccount.address, 
      mint, bob, 
      20 * 10 ** mintInfo.decimals // if we increase the burn amount the exemption will accrue, because from the approved delegate amount is only 20 remained. 
    )
    
    alice_token_account_info = await token.getAccount(connection, aliceTokenAccount.address);
    bob_token_account_info = await token.getAccount(connection, bobTokenAccount.address);



    console.log("bob burned 20 tokens, new balances:", alice_token_account_info.amount.toString(), "bob balance: ", bob_token_account_info.amount.toString());

      
}
```
