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