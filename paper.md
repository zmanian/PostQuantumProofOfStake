# PostQuantum Soundness in Proof of Stake Blockchains
#blockchain #consensus #Tendermint #PostQuantum

## Introduction
Proof of Stake blockchains are distributed state machines that use the internal state of ledger as Public Key infrastructure for a Byzantine Fault Tolerant consensus process.  One characteristics of Proof of Stake blocking chains that differentiates from Proof of Work chains is that they rely on the cryptographic signatures verified against the internal PKI. Proof of Work chains rely on anonymous signatures of work. Forgery or theft of keys from the internal PKI can undermine the soundness of a Proof of Stake system.

The Public Key infrastructure used in contemporary Proof of Stake blockchains is typically based on elliptic curve cryptography which affords small, quickly verified signatures. Forgery of these signatures is believed to a hard problem requiring a solution to the discrete logarithm problem over the elliptic curve group. 

In 1996, Peter Shor developed an algorithm that could exploit the unique properties of a quantum computer to solve the discrete logarithm problem and the related RSA problem. Recently, there has been considerable progress towards small, practical quantum computers. These computers far fall short of the immense scalable quantum computers[0] needed to execute Shor’s algorithm on commonly used cryptographic primitives but it appears that physics does not prohibit the existence of these quantum computers. If they can be built, they lie far in the future but it isn’t clear how long. 

The effect of a sufficiently large quantum computer on a Proof of Stake blockchain would be dramatic suddenly the entire history could be rewritten and all safety guarantees would be lost. 

Concern about the impact of quantum computers has led to the creation of the field of Post Quantum Cryptography as set of new cryptographic systems based on problems with an no known quantum attacks.

What role can Post Quantum primitives play in securing Proof of Stake blockchains? This paper discusses the trade of various approaches to integrating Post Quantum primitives .

## Post Quantum Soundness in Proof of Stake ledger

A casual definition of the soundness of a Proof of Stake ledger is the belief there is a unique valid ledger history that leads from genesis to the current state.  If the safety threshold of a ledger fail such as 2/3rd  or more of the stake being captured by a malicious or compromised party, the ledger becomes unsound and no particular history is authentic.

Quantum computers pose no threat today and for the next several decades.  But once it arrives, it will instantly make current Proof of Stake ledgers unsound. By that time, all operating Proof of Stake ledgers will adopt a Post Quantum cryptographic systems.  With the benefit of additional years of development and cryptanalysis, the protocol developers will be able to choose an appropriate cryptographic standards resistant to quantum attack and upgrade their protocols.

This leaves the problem of how to manage the integrity between the current classical era and the future quantum era.  The introduction of post quantum secure signatures should come years before large quantum computers are available.  In the time between the status quo and a future where post quantum cryptography is obviously necessary a careful trade off of costs is necessary.





## The Costs of Adopting Post Quantum Primitives

BFT protocols generally require accumulating signatures that verify that consensus protocol was correctly performed. In most Proof of Stake protocols, these signatures are retained in the block header to be verified

A cryptographic primitive is generic description of a cryptographic component like a signature, key exchange or threshold encryption with specifying the underlying system that provides this service. BFT consensus protocols typically only specify the primitive needed for the protocol.  The specific implementations is usually not a factor.

Let us focus on BFT protocols that rely on cryptographic signatures as their primary primitive. Post Quantum signatures are available. It is possible to construct a signature primitive with well known cryptographic symmetric primitives like hash functions whose security well understood in the post quantum environment. These signature schemes have their roots in cryptographic work going back almost 4 decades. The cost of post quantum security is a substantial increase in the size of public keys and signatures. On the other hand, we have novel signatures schemes based on lattice and module lattice problems. They offer much smaller increases in key and signature sizes but the underlying cryptography is much less understood.


| Algorithim | Signature Size | PubKey Size | Link|
|------------|----------------|-------------|-----|
|Sphincs | 41 KB | 1KB | https://sphincs.cr.yp.to/sphincs-20150202.pdf|
|XMSS | 13KB |10KB | https://eprint.iacr.org/2011/484.pdf|
|ZKBoo| 117 KB | 64KB | https://eprint.iacr.org/2017/279.pdf |
|Tesla| 1.25KB | 1096KB | https://cryptojedi.org/papers/tesla-20161005.pdf|
|Dillithium| 1.3KB | .89Kb | https://cryptojedi.org/papers/dilithium-20170627.pdf|

Elliptic curve signatures are usually on the approximately of 64 bytes.  10 times smaller than even the newest post quantum options. It is also possible to take advantage of signature aggregation in these protocols allowing the opportunity to have 100s of signatures combined into a single signature.  Thus 64 bytes can provide proof that hundred of validators validated a block. No systems for doing this exists yet in the post-quantum regime but seems reasonable to believe that post quantum zero knowledge proof systems or lattice based systems will eventually be available which can provide aggregate signatures. Adopting a hybrid classical and post quantum strategy means a significant increase in the size the data structures needed to execute a BFT protocol.

Size and network traffic are a concern for the design of consensus systems for 2 reasons, latency and size of block headers.  A significant concern for blockchain systems is the speed at which new state updates can be generated and propagated through the network. None of the proposed post quantum signatures are prohibitively time consuming to generate or verify but the time needed to transmit those signatures over the Internet becomes a constraint on throughput of the protocol. This burden grows with the size of the signatures.

Providing some kind of post quantum resilience makes enormous sense to provide assurances to future protocol developers that they will have certainty about what history to build up when and if a post quantum upgrade to the protocol becomes necessary.

## A Hybrid Scheme
Quantum attacks on a ledger represent a kind of very long term risk to a protocol. The sensible thing to do is include post quantum signatures sparsely on chain. 

While lattice approaches to signatures have extremely appealing size requirements , they are at best candidates for a future full post quantum protocol upgrade. The right thing to do is take post quantum signing out of the consensus process all together. Instead post quantum signatures should be ordinary transactions, validators can maintain a post-quantum public key in addition to their elliptic curve key.	Validators can submit transactions containing a post quantum signature on a past block. The should be a reward for submitting these transactions similar to the reward for completing generate a valid block when elected leader. The reward should be proportional the stake the validator has committed for block signings.

The inclusion of these occasional post quantum signatures will ensure that future validators will be able to verify they are on a correct chain even after quantum computers render all the signatures generated in the consensus process forgeable.

The ideal post quantum primitive for these signatures is one with small public keys and conservative cryptographic assumptions.  The SPHINCS signature system only requires the assumption that underlying permutation either a hash function or a block cipher remains second-preimage resistant.  Quantum computers are not expected to weaken any of these functions to second presage attacks. Public keys are only 1 kb and 41kb per transaction is suboptimal. These transactions will be fairly rare.

There is one subtle condition that needs to be guarded against. Post Quantum signing keys should become active until a currently valid key has signed.  The initial set of keys would need to be hard coded into the software.

### Validation rules for Post Quantum Soundness Transaction

1. Receive signature, block height and public key id.
2. Check public key id maps to Sphincs pub key signature in the current staking table. 
3. Check that key has been marked active by another soundness tx.
4. Validate signature against block hash at block height.
5. Transfer the reward to the signers account.


## Conclusion

With relatively minor costs and no impact on current consensus performance, it is possible to ensure that proof of stake ledgers retain soundness as we transitional from the classical to post quantum era of cryptography.  We can make use of conservative cryptographic functions to improve our confidence that these signatures will remain unforgeable and delay adoption more experimental post quantum cryptographic techniques 










Citations

[0] Quantum Resource Estimates for Computing Elliptic Curve
Discrete Logarithms [0] https://eprint.iacr.org/2017/598.pdf






