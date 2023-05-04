# GCM: Turning Block Ciphers into Stream Ciphers

If you're using Firefox, you can click the lock next to the URL, and click the first option that appears below. Clicking on "More information" should reveal the algorithm used to encrypt this. For me when writing this, it says `TLS_AES_128_GCM_SHA256`. AES (also called Rijndael) is an incredibly popular block cipher, which is being used here with 128-bit keys. This is about the mode of operation of this block cipher (`GCM`). 

There are two main category of ciphers: block ciphers and stream ciphers. Block ciphers take a small, fixed-length block of ciphertext, and encrypt it all at once. We can describe a function $E_k(b)$ that performs this encryption on a block $b$ using a key $k$. Decryption with $k$ is $E_k^{-1}$. Stream ciphers encrypt the data one bit at a time. Usually, this is done by generating a keystream $S_k$ one bit at a time, and calculating $A \oplus S_k$ for a plaintext $A$. 

However, we often need some way to securely encrypt more than one block with a single key. Just using different keys for each block isn't practical, because we want to encrypt an arbitrary number of blocks, and not have to negotiate generating or sharing a large number of keys. Thus, we need a _mode of operation_ for block ciphers. The simplest one is to break up the plaintext $A$ into blocks $\{b_0, b_1, \cdots\}$, and encrypt them all with the same key. This is known as the Electronic Codebook (ECB) mode, and is almost never used, because blocks that are identical in the plaintext remain identical in the ciphertext as well. This makes it very easy to recognize patterns, but can also cause other problems. 

One option is to use the ciphertext $C_{n-1}$ (what we get when encrypt $b_{n-1}$) to encrypt $b_n$. A simple way to do this is to perform $C_n = E_k(b_n \oplus C_{n-1})$, where we consider $C_{-1}$ to be a randomly-chosen (but public) value. This is called the Initialization Vector (IV), although in this case it just acts as a nonce. Here, IVs will be denoted as $V_I$. This mode is called Cipher Block Chaining (CBC), and is actually very common and usually secure. However, it's very slow, because there's no way to parallelize the encryption process. You have to process each block in sequence. 

Instead, let's try something a little strange - using a block cipher to generate a keystream, and then just xoring it with the plaintext. We'll do this by combining our initialization vector with the index of our block, and encrypting that as $S_i = E_k(V_I \mathbin\Vert (i + 1))$. We can then treat this as the keystream for a stream cipher, and generate the ciphertext $C_i$ for a block of plaintext $A_i$ as $C_i = A_i \oplus S_i$. This is called Counter Mode (CTR), and does work well, but we aren't quite done. 

Stream ciphers, like CTR mode, almost always require some form of _authentication_. Authentication just means some way to ensure that what you recieve hasn't been tampered with. For stream ciphers with no authentication, like CTR mode when used by itself, this tampering is very, very easy. If you know some part of plaintext $A$, you can take the ciphertext $C = A \oplus S_k$, and calculate $C \oplus A = A \oplus S_k \oplus A = S_k$. Now that you have the keystream, you can just encrypt whatever you want to send as a substitute, since $C \oplus A \oplus A_{\text{evil}} = A_{\text{evil}} \oplus S_k$. 

The GCM algorithm requires a brief detour to explain Galois fields (specifically extension fields) if you aren't familiar with them. You can find that here if you need it: [::/galois-fields.md]. 

We need some sort of special hash-like function, that can only be generated with knowledge of the underlying key $k$ (not the keystream $S_k$), and will generate a verifiable signature value. Only the sender will have knowledge of the key $k$, and therefore only the sender will be able to generate the signature value correctly. First, we encrypt $0 \cdots 0$ to obtain a key-dependent value $H_k$. Then, we define our hash-like function $M_k(X) = X \cdot H_k$. We multiply these bitstrings by treating them as elements of the Galois field $GF(2^m)$. 

We'll store the authentication data in a changing vector denoted $T$, which can be initialized with any additional data that we want to authenticate (outside of what is necessary for encryption). We authenticate all aspects of the encryption process by performing the following updates:

- $T \leftarrow M_k(T) \oplus C_i$ for each ciphertext block $C_i$
  - Authenticate the ciphertext
- $T \leftarrow M_k(T) \oplus (\operatorname{len}(A) \mathbin\Vert \operatorname{len}(C))$
  - Authenticate the lengths of the plaintext and ciphertext
- $T \leftarrow M_k(T) \oplus E_k(V_I \mathbin\Vert 0)$
  - Authenticate the initialization vector

The final state of $T$ after all of this is an _authentication tag_, which can be recalculated during decryption in order to check whether the data has been tampered with. Without knowledge of the underlying key $k$, you can't define the $M_k$ operation, so it isn't possible for someone tampering with messages to create a fake authentication tag. 

That's all there is to it. I personally find GCM a really cool little algorithm, and it's widespread use is a testament to the true utility of these kinds of clever mathematical ideas. Although, if I'm being honest, the entire field of cryptography is too. 
