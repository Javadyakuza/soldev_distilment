# Keypair
A keypair is a matching pair of the a `public` and a `private` key.  

The public key is used as an address that points to an account on the solana network. public kay can be and must be shared with anyone or the ones that wants to have some business going on with ya.


# cryptography

CryptoGraphy is just  basically the art of hiding the information, ther is two types of the cryptography: 

## Symmetric

In the symmetric cryptography the same key that used to encrypt the data is used to encrypt the data as well. the well known algorithms are including `AES` and `Chacha20` among others.

## Asymmetric

In this method we are having a keypair included in the process of the en/decryption of the data.

### Encryption:
We can encrypt a data using a public key and the encrypted data can be decrypted using the secret key of the same keypair that the public key is used to encrypt the data.

### Signature

If a data is encrypted with the secret key, the public key of the same keypair can be used to prove the the secret key that encrypted this data is from the same key pair that its public key is used to prove the that.


we can use them in combination and use the recipient public key to encrypt the data, in this way only recipient secret key can decrypt the data.
The well known algorithms of this method are `ECC` and  `RSA` among others.

> Solana uses the public keys as the addresses.

> [Creating a keypair Code Sample](./code_samples/ts_code_samples.md#creating-a-keypair) 

> [Loading a existing keypair Code Sample](./code_samples/ts_code_samples.md#creating-a-keypair) 