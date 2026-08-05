---
title: Why Bitcoin Doesn't Use Probabilistic Encryption Functions
tags:
  - Encryption
  - Psychology
date: 2023-10-04 22:36:18
draft_date: 2023-10-04 10:32:25
---

### Probabilistic Encryption Functions

In common symmetric encryption, one plaintext corresponds to one ciphertext. There is a type of encryption called probabilistic encryption, which introduces randomness during the encryption process, resulting in one plaintext corresponding to multiple ciphertexts.

1. Why Probabilistic Encryption Functions Are Rarely Used

It's essential to distinguish between three concepts: encryption, signatures, and hashing. Probabilistic encryption functions are a type of symmetric encryption, but in reality, encryption—whether symmetric or asymmetric—is rarely used in blockchain systems. Bitcoin's white paper only mentions signatures and hashing; signatures are used to confirm asset ownership when initiating transactions, and hashing is used to confirm block creation during mining. It does not mention the use of encryption.

The main reason for the limited use of encryption is its limited application scenarios. Storage projects might use file encryption, but that falls under user behavior, and the blockchain system itself does not participate in data encryption protection.

Replacing signatures and hashing with probabilistic encryption functions is impossible since they do not have such capabilities.

2. The Probability Distribution of Probabilistic Encryption Functions

Where there is probability, there is a probability distribution, such as a normal distribution. For probabilistic encryption functions, a uniform probability distribution is ideal; otherwise, if the probability distribution is known, an attacker might infer the plaintext from the ciphertext, which is very dangerous. Only with consistent random strength can attackers be thwarted.

Therefore, probabilistic encryption functions have almost no choice but to strive for uniform distribution, which is a common approach.

### New Style Floor-to-Ceiling Windows

In a beautiful and harmonious animal forest, floor-to-ceiling windows suddenly became popular. These houses not only have good lighting but also boast beautiful and high-end designs. The little fox is a famous architect who can build these trendy houses with large floor-to-ceiling windows. The forest animals flock to the little fox to have houses built.

The little parrot, a jack-of-all-trades in the forest, is well-informed and eloquent. It is also the strategist beside the river's overlord, the hippo, often giving the hippo advice. Upon hearing about the trend of floor-to-ceiling windows, the hippo also wanted to build one. However, the hippo, due to its high status, did not want to use the common design. So, it asked the little parrot to come up with an idea.

The little parrot, after much inquiry, accidentally heard the term "fiberglass," a newly emerged synthetic material known for being shatterproof and highly resilient. Delighted, the little parrot hurriedly informed the hippo, claiming to have discovered a revolutionary new building material that could replace glass and lead the next generation of housing trends!

The hippo, satisfied, invested in and commissioned the little parrot to find someone to build the house, ensuring the construction plan remained confidential. The innovation had to be kept secret.

Hearing about this, the little fox became curious. Having built houses for years, the little fox had never heard of such a new material. It was surprising that the little parrot could make such a significant innovation. Coincidentally, the little parrot's construction team was recruiting, so the little fox eagerly joined to participate in the hippo's house construction team.

However, upon joining, the little fox was stunned. Fiberglass is not glass; it is opaque! How could it replace glass? If they insisted on using fiberglass, the house would be uninhabitable. If they didn't use fiberglass, they would have to use glass, which would mean no innovation!

Eventually, the little fox left the little parrot's construction team...

### Dunning-Kruger Effect

The Dunning-Kruger effect is a type of cognitive bias, particularly describing the phenomenon of underestimating or overestimating one's abilities. When experts in one field attempt to make revolutionary innovations in another field, it can be attributed to the Dunning-Kruger effect. However, there are nuances, and a more accurate description might be "overconfidence beyond one's domain," or more professionally, "professional illusion."

From a psychological perspective, this phenomenon is explainable, but in reality, there is another critical factor—a kind of invisible force...
