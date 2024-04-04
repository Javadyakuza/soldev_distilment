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