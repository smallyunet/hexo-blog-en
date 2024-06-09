---
title: Why the Private Key Calculation of Ethereum is Irreversible
tags: Blockchain
date: 2023-02-20 23:42:24
draft_date: 2023-02-18 17:12:04
---

Have you ever wondered why the private key of Ethereum cannot be reverse-calculated from the account address? When you have a private key and want to get the account address corresponding to this private key, you can import the account into MetaMask, or use an SDK like ether.js to import an account into the wallet at the code level, and then print out the account address. Is there any black box operation in this account import process?

A few days ago, I accidentally saw an article on Medium, where the author wrote a very concise code to calculate the process from the private key to the address. The author's code is here [RareSkills/generate-ethereum-address-lower-level.py](https://gist.github.com/RareSkills/eb51623908f348663cd6a241d9dbf115).

I copied the code:

``` python
from ecpy.curves import Curve
from sha3 import keccak_256

private_key = 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

cv     = Curve.get_curve('secp256k1')
pu_key = private_key * cv.generator # just multiplying the private key by generator point (EC multiplication)

concat_x_y = pu_key.x.to_bytes(32, byteorder='big') + pu_key.y.to_bytes(32, byteorder='big')
eth_addr = '0x' + keccak_256(concat_x_y).digest()[-20:].hex()

print('private key: ', hex(private_key))
print('eth_address: ', eth_addr)
```

This code has only four or five lines, and there are two key points in the calculation: `private_key * cv.generator` and `keccak_256(concat_x_y)`. Apart from these two points, the other parts are constant calculations. At first glance, it seems complicated, but when you look at it carefully and break it down, it's all very simple string concatenation.

Among them, `private_key * cv.generator` is the calculation of the elliptic curve. The private key `private_key` defined above is a hexadecimal number, note it is of `int` type, and then the generator of the elliptic curve is used to calculate a value. This calculation process is irreversible, which is the process of circling on the elliptic curve. It can be understood by analogy that after a number is taken modulo, you cannot restore the number before the modulo. The elliptic curve just uses a more complex method to provide a safer calculation result than RSA.

The second irreversible calculation is `keccak_256(concat_x_y)`, which is a process of calculating the hash value. Keccak is a kind of sha3. Digest algorithms are irreversible, which is undoubtedly the case.

In other words, in the calculation process from the private key to the address, there are two irreversible points, so overall, it is impossible to reverse calculate the private key from the Ethereum account address.
