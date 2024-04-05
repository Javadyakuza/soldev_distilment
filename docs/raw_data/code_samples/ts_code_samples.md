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