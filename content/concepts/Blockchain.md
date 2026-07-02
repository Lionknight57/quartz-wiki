---
title: Blockchain
tags: [concepts, blockchain, cryptography, distributed-systems, bitcoin, ethereum]
related: [Authentication-Security, Databases-NoSQL]
sources: [BlockChain/BlockChain.md, BlockChain/Principles and Practices.md, BlockChain/summary.md]
updated: 2026-04-19
---

# Blockchain

> "An incorruptible digital ledger of economic transactions that can be programmed to record not just financial transactions but virtually everything of value." — Don & Alex Tapscott

Blockchain is a **decentralized, distributed ledger** — a continuously growing list of records (blocks), linked and secured using cryptography. Created as the underlying technology for Bitcoin (Satoshi Nakamoto, 2008).

---

## Core properties

| Property | What it means |
|---|---|
| **Decentralization** | No central authority — network of nodes each holding a full copy of the chain |
| **Distributed ledger** | Every node has the complete chain; no single point of failure |
| **Immutability** | Altering a block changes its hash, invalidating all subsequent blocks — tamper-evident by design |
| **Transparency** | All transactions visible to network participants — clear audit trail |
| **Security** | Cryptographic hashing + digital signatures + consensus protect the chain |

---

## Block structure

Each block contains:

| Field | Description |
|---|---|
| **Block Hash** | SHA-256 hash of this block's contents |
| **Block Number** | Position in the chain |
| **Creation Date** | Timestamp |
| **Previous Block Hash** | Links this block to the prior one — forms the chain |
| **Transactions** | The data payload (e.g. financial transactions, contract data) |

**Why immutability is hard to break:** changing any data in a block changes that block's hash. Since each subsequent block contains the previous hash, every block after it also becomes invalid. An attacker would need to recalculate the entire chain *and* control >50% of the network's computing power simultaneously.

Bitcoin example: ~10 minutes per block; 1000-block chain ≈ 7 days to recalculate. On a live network with thousands of nodes all adding blocks, this is computationally infeasible.

---

## Cryptographic foundations

### Hashing

A hash function takes arbitrary input and produces a fixed-size output (the **message digest**). Properties:

- **Deterministic** — same input always produces same hash
- **One-way** — cannot reverse a hash to get the input (unlike encryption)
- **Collision-resistant** — infeasible to find two different inputs with the same hash
- **Avalanche effect** — tiny change in input produces completely different hash

| Algorithm | Family | Status |
|---|---|---|
| MD5 | — | Broken (collisions found) — avoid for security |
| SHA-1 | SHA-1 | Deprecated |
| SHA-256 | SHA-2 | Standard — used in Bitcoin |
| SHA-512 | SHA-2 | Stronger variant |
| SHA-3 | SHA-3 | Latest family (different construction) |

**Hashing ≠ encryption:** hashing is one-way; encryption is two-way (reversible with a key).

### HMAC — Hashed Message Authentication Code

Combines hashing with a shared secret key. Provides:
- **Integrity** — message was not altered
- **Authentication** — message came from someone who knows the key

### Digital signatures

Asymmetric cryptography (RSA) applied to message signing. Provides:
- **Authentication** — verifies the sender's identity
- **Non-repudiation** — sender cannot deny having sent the message

**Flow:**
```
Alice (sender):
  1. Encrypts her data with recipient's public key
  2. Hashes the encrypted data
  3. Signs the hash with her own private key
  → sends: encrypted data + hash + digital signature

Bob (recipient):
  1. Decrypts data with his private key
  2. Calculates hash of received encrypted data
  3. Verifies Alice's digital signature using her public key
  4. Compares computed hash to received hash — if they match, data is authentic and unaltered
```

| Key | Encryption | Signatures |
|---|---|---|
| Public key | Encrypt | Verify signature |
| Private key | Decrypt | Sign message |

---

## Proof of Work

The mechanism that makes adding a new block computationally expensive. Nodes (miners) must solve a **cryptographic puzzle** — finding a nonce value that produces a block hash below a target difficulty. This:

- Makes adding blocks slow and expensive (Bitcoin: ~10 min/block)
- Makes tampering prohibitively expensive — you'd need to redo PoW for every subsequent block
- Creates the economic incentive structure (mining rewards)

---

## Consensus mechanisms

All nodes must agree on the state of the chain:

| Mechanism | How it works | Used by |
|---|---|---|
| **Proof of Work (PoW)** | Computational puzzle — most work wins | Bitcoin |
| **Proof of Stake (PoS)** | Validators chosen proportional to their stake (coins held) | Ethereum (post-Merge) |
| **Delegated Proof of Stake (DPoS)** | Token holders vote for delegates who validate | EOS, Tron |

PoS uses far less energy than PoW — Ethereum's switch from PoW to PoS reduced energy use by ~99.95%.

---

## Public vs private blockchains

| | Public | Private |
|---|---|---|
| **Who holds a copy** | Every node — full copy | Selected nodes only |
| **Who can write** | Anyone | Enterprise controls access |
| **Security model** | Decentralized consensus — very strong | Controlled — weaker decentralization |
| **Speed** | Slower (PoW puzzles) | Faster |
| **Examples** | Bitcoin, Ethereum | Hyperledger Fabric, Corda |
| **Controversy** | — | Enterprises like control but lose key trust benefits |

---

## Smart contracts

Self-executing code stored on the blockchain. Conditions are written directly into code — when preconditions are met, the contract executes automatically with no intermediary.

- **Ethereum** is the dominant smart contract platform
- Written in **Solidity** (Ethereum) or **Rust** (Polkadot/Solana)
- Enable: DeFi, NFTs, DAOs, automated escrow, token issuance

---

## Use cases

| Domain | Application |
|---|---|
| **Digital currency** | Bitcoin, stablecoins, CBDC |
| **DeFi** | Decentralized exchanges, lending, yield farming |
| **Smart contracts** | Automated agreements without intermediaries |
| **Supply chain** | Track origin and movement of goods (provenance) |
| **Identity** | Self-sovereign identity — user controls their own credentials |
| **AML / KYC** | Immutable audit trail for financial compliance |
| **Voting** | Tamper-resistant transparent election records |
| **Healthcare** | Shared patient records across providers with consent control |
| **Intellectual property** | Provable timestamped ownership records |
| **Public records** | Land registries, certificates, licenses |

---

## See also
- [[Authentication-Security]] — cryptography fundamentals (OAuth, JWT, asymmetric keys)
- [[Databases-NoSQL]] — distributed data storage concepts
