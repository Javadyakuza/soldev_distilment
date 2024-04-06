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