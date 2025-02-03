---
layout: single
title: PBA-X
---

# 1. Cryptography
## 1.1 Kerckhoff's Principle:
A cryptographic system should be secure even if everything about the system, except the key, is public knowledge.

## 1.2 Hashing attacks
* **Preimage Attack**: Given a hash value h, find any message m such that `hash(m) = h`. Trying to find an input that produces a specific target hash
* **Weak Collision Attack (Second Preimage Attack)**: Given a message `m1`, find another message `m2` such that `hash(m1) = hash(m2)`.
* **Strong Collision Attack**: Find any two different messages `m1` and `m2` such that hash(m1) = hash(m2). Can use any two inputs, not tied to a specific input or hash.

## 1.3 Encryption
* Symmetric key encryption is less computationally expensive, so faster, than asymmetric key encryption. 
* So we try to share the symmetric keys securely and then use them for communication. 2 ways to share keys:
    * **Diffie Hellman key exchange**: Both parties involved create unique secret keys. Then they share a common `base` and `modulus` and use modular arithmetic to calculate a shared secret key.
    * **Asymmetric Key Encryption (RSA)**: Here both parties create separate public and private keys. The public key is shared with the other party and the private key is kept secret. Both parties use this secure channel to share a symmetric key.

## 1.4 Wallet and Accounts
* A crypto wallet doesn't store any crypto assets. It stores the private keys which prove ownership of the crypto assets.
* The public key is the wallet address.
* Pneumonic phrases are a way to generate a private key from a random string of words.
* We can't remember a `2048` bit private key, so we use a pass phrase to generate it. The magic here is that we change the base of the private key from `2` to `58` (in case of polkadot) so that the private keys becomes shorter and is easy to remember.
* The specific address format used in polkadot is `SS58` format. `SS58` is a base58 encoding of the public key and it adds `2` bytes at the beginning to "indicate the network". It also adds `2` bytes at the end for `Checksum`.
* **Hard Derived Account**: Has it's own private key and public key. Created with `\\` (2 slashes).
* **Soft Derived Account**: Has it's own public key and shares the private key with the parent account. Created with `/` (1 slash).

## 1.5 Digital Signatures
* A digital signature is a mathematical scheme for verifying the authenticity of a digital message or document.
* Guaranteed properties of a Digital Signature scheme in the context of transactions:
    * **Authenticity**: a valid signature implies that the signer deliberately signed the transaction.
    * **Unforgeability**: it is computationally infeasible to forge a signature without knowledge of the private key.
    * **Non-repudiation**: the signer cannot later deny having signed the message
    * **Integrity**: ensure the transaction data has not been modified
* It consists of 3 algorithms:
    * **Key Generation**: Generate a public and private key pair.
    * **Signing Algorithm**: Uses the private key and the message to create a signature.
    * **Verification Algorithm**: Uses the public key and the original message and the signature to verify if the signature is valid. The signature will only verify correctly if the message hasn't been tampered with and the public key corresponds to the private key that was used to create the signature.
* **In Practice** digital signatures don't sign the entire message. Instead a hash function is used to hash the message and create a `Digest` or `Fingerprint` of fixed length. The reason for this is that Cryptographic hashing functions usually work on fixed size inputs.
* One challenge that Digital signatures address is `Replay Attacks`. A replay attack is when an attacker captures a message and then later re-sends it to the recipient. The recipient can't tell if the message is new or old. To prevent this, Digital signatures often use additional information like `nonces` and `timestamps`/`lifetimes`.
* **Multisig accounts** are accounts that require multiple signatures to authorize a transaction. This is useful for things like other decentralized organizations.

## 1.6 Hash Based Data Structures

### 1.6.1 Hash Chains
* A hash chain is a fundamental data structure used in blockchains to ensure the integrity and immutability of data. It is a sequence of blocks (objects), each containing data and a cryptographic hash of the previous block, forming a chain (could be anything like vector or list). This structure ensures that any alteration in a block would require changes to all subsequent blocks, making it tamper-evident.
* Most blockchain implementations (including major ones like Bitcoin) store blocks in a database or vector and use the hash values for validation, not traversal.

### 1.6.2 Merkle Tries
* Merkle tree also known as hash tree is a data structure used for data verification and synchronization. 
* It is a tree data structure where each non-leaf node is a hash of it’s child nodes. All the leaf nodes are at the same depth and are as far left as possible. 
* Each transaction gets hashed, Hashes are paired and hashed again, Process repeats until single root hash, Root hash goes in block header of the blockchain block.
* It is called a `merkle "trie"` because the trie data structure is used to reduce the amount of redundant data stored in the tree.
* Whereas reading and writing to a database could be considered `O(1)`, a merklized database has read and write complexity of `O(log N)`, where `N` is the total number of items stored in the database.
* The primary advantage of using a merkle trie is that proving specific data exists inside the database is much more efficient! Whereas you would normally need to share the `whole database` to prove that some data exists, with a merklized database, you only need to share `O(log N)` amount of data. This is very important to support `light clients`.

![Diagram Description](images/merkle-tree.png)

### 1.6.3 Merkle Mountain Range
* A Merkle Mountain Range (MMR) is a variation of Merkle Trees that allows efficient appending of new elements. Think of it as a collection of perfect binary trees of different heights, forming a "mountain range" profile.
* Efficient appending O(log n), Easy to prove membership, Good for growing datasets, Used in blockchain UTXO sets.

![Diagram Description](images/UTXO-mmr-mgmt.png)

* MMR provides a way for blockchain UTXO management. When new transaction creates UTXOs:
    * Hash the new UTXOs
    * Add to MMR structure
    * Update peaks if needed
* When spending UTXOs:
    * Prove UTXO exists using MMR path
    * Remove spent UTXO
    * Add new UTXOs from transaction

### 1.6.4 Radix/Patricia Tries
* A Patricia Trie (Practical Algorithm To Retrieve Information Coded In Alphanumeric) is a modified trie that compresses nodes with only one child, making it more space-efficient. In blockchains, it's used to store state data efficiently.

![Diagram Description](images/patricia-trie.png)

## 1.7 Advanced Cryptography Topics

### 1.7.1 Shamir Secret Sharing
* Shamir Secret Sharing is a cryptographic method to split a secret into n shares, where any k shares (threshold) can reconstruct the secret, but k-1 or fewer shares reveal nothing about the secret.

### 1.7.2 Verifiable Random Functions (VRFs)
A VRF is a cryptographic function that:
* Takes an input and private key to generate a random number
* Provides a proof that the number was generated correctly
* Anyone can verify the proof using the public key
* Same input + private key always gives same output
* Output appears random to anyone without the private key

### 1.7.3 Zero-Knowledge Proofs (ZKPs)
* A zero knowledge proof is a method where one party (prover) can prove to another party (verifier) that a statement is true without revealing any information beyond the validity of the statement.

# 2. Fundamentals of blockchains

## 2.1 Blockchain Concepts Pre-Quiz:
* Bitcoin uses `UTXO` model instead of the `Accounts` model to process transactions.
* Typically, The hash of the NFT images and videos is stored on the blockchain network. The original content is stored typically on a decentralized storage platform which may or may not use blockchain technology stack. Storing them directly on the blocks or in the blockchain state can be expensive and is an inefficient implementation of the NFT use case.
* Just because the transaction shows up on Bitcoin block explorers does not mean the transaction is final. The finality of the Bitcoin blockchain is probabilistic. That is why central exchanges wait a couple of hours before letting you transact with the Bitcoin you deposited on their platform.
* The security guarantees offered by Ethereum Roll-ups (Layer 2 chains) are **NOT** the same as the security guarantees of the Ethereum network (Layer 1 chain).
* Blockchain network's security and resilience to attacks rely on factors like decentralization, consensus mechanisms, and protocol design.

## 2.2 Blockchain Networks:

### 2.2.1 Types of Decentralization
* **Architectural Decentralization**: To address single point of failures in the network (Distributed systems)
* **Political Decentralization**: Who gets to decide how the blockchain should operate? What is the governance model?
* **Logical Decentralization**: Blockchains may be politically decentralized (no one controls them) and architecturally decentralized (no infrastructural central point of failure) but they are logically centralized. There is one commonly agreed state and the system behaves like a single computer. Most blockchains are logically centralized.

## 2.3 Blockchain Architecture
* Blockchain architecture consists of multiple layers, each with specific functions:
    * **Application Layer**: Top-most layer where users interact with blockchain. Includes dApps, smart contracts, wallets, and user interfaces. Provides services and functionality to end users
    * **Consensus Layer**: Ensures all nodes agree on the state of the blockchain. Implements consensus mechanisms (PoW, PoS, etc.). Validates and orders transactions
    * **Network Layer**: Handles peer-to-peer communication between nodes. Manages transaction and block propagation. Maintains network connectivity and node discovery
    * **Data Layer**: Defines structure of blockchain data (blocks, transactions). Implements cryptographic primitives and data structures. Handles data storage and retrieval
    * **Infrastructure Layer**: Base layer providing hardware and software requirements. Includes nodes, physical networks, and computing resources. Supports basic blockchain operations

![Diagram Description](images/blockchain-architecture-layers.png)

* Different **types of blockchain architectures**:

![Diagram Description](images/kinds-of-blockchain-architectures.png)

* Layers in Blockchain Ecosystem Architecture:
    * **Layer 0**: Network infrastructure protocols that allow blockchains to be built and communicate
        * Not a blockchain itself
        * A foundation layer/protocol
        * Example: Polkadot isn't a blockchain, it's a protocol that allows creation and connection of blockchains (parachains)
    * **Layer 1**: Actual base blockchains
        * These ARE blockchains
        * Independent networks with their own consensus
        * Example: Ethereum is a L1 blockchain
    * **Layer 2**: Scaling solutions built ON TOP of L1 blockchains
        * Not independent blockchains
        * Solutions that extend L1 capabilities
        * Example: Optimism isn't its own blockchain, it's a scaling solution for Ethereum

## 2.4 Blockchain Trilemma
* The Blockchain Trilemma refers to the fundamental challenge in blockchain design where it's seemingly impossible to achieve all three key properties simultaneously:
    * Decentralization
    * Security
    * Scalability

## 2.5 Blockchain Consensus

### 2.5.1 Layers of Consensus
These are used in consensus mechanisms to filter out chains until we find the canonical chain.
* **State Machine Validity**: This checks if the history of transaction (`State Transitions`) are valid.
* **Political Validity**: We might reject blocks that don't meet certain `political` or `arbitrary` rules. For example, in Bitcoin, blocks bigger than 1MB are rejected.
* **Right to Author**: This checks if the block was authored by the right person who was elected to author the block.
* **Fork Choice**: Each node has its own view of the blockchain and decides, based on that, if the current block is valid or not. A block in an invalid fork would be rejected.

### 2.5.2 Consensus Mechanisms
* **Proof of Work (PoW)**: 
    * It is a `permissionless` consensus mechanism that requires miners to solve complex mathematical problems to validate transactions and add new blocks to the blockchain. It ties the blockchain's value to a real world scarce resource (energy).
    * Downsides: It is energy-intensive and the blocks are produced at varying times.
* **Proof of Stake (PoS)**:
    * A consensus mechanism where validators stake (lock up) cryptocurrency to get the right to validate blocks. The more you stake, the higher chance of being selected to validate.
* **NOTE**: `PoW` and `PoS` are mechanisms for `block authoring`. Consensus on `block finality` is achieved through finality gadgets like `GRANDPA` for deterministic block finality.

### 2.5.3 Block Finality Mechanisms

* **Block Finality**: The guarantee that a block cannot be reverted/changed once added to the chain.
* **Finality Gadgets**: These are mechanisms that allow a blockchain to reach `Deterministic Finality`. In Polkadot, the primary finality gadget is `GRANDPA`(GHOST-based Recursive Ancestor Deriving Prefix Agreement).

#### 2.5.3.1 Probabilistic Finality
* Finality becomes stronger over time. More blocks = more certainty. Used in BTC.
* There is always a risk of reverting a block if a longer chain is found.

#### 2.5.3.2 Deterministic Finality
* Finality is guaranteed. Once confirmed, block cannot be reverted. Used in PoS systems like Polkadot. Example: After 2/3 validators sign, block is final.
* There is no risk of reverting a block.

#### 2.5.3.3 GRANDPA
* `GRANDPA` is responsible for finalising blocks in polkadot.
* Polkadot uses a two-phase voting system (`Hybrid Consensus Model`) to reach finality. It separates the block production from block finality.

### 2.5.4 Forks in Blockchain
* **Soft Fork**: A change to the protocol that is backward compatible. Tightens/adds rules. Old nodes can still validate blocks.
* **Hard Fork**: A change to the protocol that is not backward compatible. Changes fundamental rules. Old nodes will reject new blocks.

## 2.6 Blockchain Node v/s Runtime

![Diagram Description](images/Node-vs-runtime.png)

# 3. Polkadot
## 3.1 Blockchain
A blockchain is, in its essence, a distributed and decentralized key-value database. The principle of a blockchain is to make it possible for any participant to perform modifications to this database, and for all participants to eventually agree on the current state of said database.

In Polkadot and Substrate-compatible chains, the state of this database is referred to as "the storage". The storage can be seen more or less as a very large HashMap.

A blockchain therefore consists in three main things:
1. The initial state of the storage at the moment when the blockchain starts
2. A list of blocks, where each block represents a group of modifications performed to the storage
3. A peer-to-peer network of clients connected to each other and exchanging information such as newly-produced blocks

Blocks are built on top of each other, forming a sequential list of modifications to the storage on top of its initial state.

## 3.2 Polkadot Architecture

Polkadot is a heterogeneous multichain with shared security and interoperability.

![Diagram Description](images/polkadot-architecture.png)

### 3.2.1 Relay Chain - A Layer 0 Blockchain
The Relay Chain is the central chain of Polkadot. The Relay Chain has deliberately minimal functionality - for instance, smart contracts are not supported. The main responsibility is to coordinate the system as a whole, including parachains. Other specific work is delegated to the parachains, which have different implementations and features.

### 3.2.2 Parachains and Parathreads
Polkadot can support a number of execution slots. These slots are like cores on a computer's processor (a modern laptop's processor may have eight cores, for example). Each one of these cores can run one process at a time. 

Polkadot allows these slots using two subscription models:
- **Parachains**: Have a dedicated slot (core) for their chain and are like a process that runs constantly
- **Parathreads**: Share slots amongst a group, and are thus more like processes that need to be woken up and run less frequently

### 3.2.3 Validator
Validators are the Relay chain nodes that, if elected to the validator set, produce blocks on the Relay Chain. They are incentivized to act in the best interests of the network through rewards.

### 3.2.4 Collator
Collators are full nodes on both a parachain and the Relay Chain. They:
- Collect parachain transactions
- Produce state transition proofs for the validators on the Relay Chain
- Can send and receive messages from other parachains
- Maintain a full node of the parachain
- Retain all necessary information of the parachain
- Produce new block candidates to pass to the Relay Chain validators for verification and inclusion in the shared state of Polkadot

### 3.2.5 Bridges
A blockchain bridge is a connection that allows for arbitrary data to transfer from one network to another. These chains are interoperable through the bridge but can exist as standalone chains with different protocols, rules, and governance models. 

In Polkadot, bridges:
- Connect to the Relay Chain
- Are secured through the Polkadot consensus mechanism
- Are maintained by collators

# 4. Polkadot SDK
## 4.1 Polkadot SDK Overview
* **Polkadot SDK**: A collection of tools and libraries that help developers build applications on the Polkadot network.
* **Polkadot SDK Components**: The SDK has 3 main components:
  * **`Substrate`**: A framework for building blockchains. This is the foundational framework of Polkadot SDK. It includes core components like networking, transaction pools and APIs.
  * **`FRAME`**: A collection of modules that provide common functionality for building blockchain applications. This is built on top of Substrate and defines the Application logic of the blockchain.
  * **`Cumulus`**: This is a lighter layer in the SDK which enables `substrate` based chains to be compatible with Polkadot.
* **`ZombieNet`**: It helps simulate a relay chain and parachain node setup on a local machine for testing the features and functionalities of a parachain (end-to-end testing for parachains on a local machine).
* **`Chopsticks`**: Chopsticks is a tool that can be used to test blockchain runtime. It simulates just the relay chain and parachain runtimes without running the nodes and can be used for testing runtime features. Chopsticks is a versatile, light weight testing tool for cross-chain functionality in the Polkadot ecosystem. Chopsticks tooling can be used to test cross-chain functionalities between Polkadot and parachains, as well as the functionalities between different parachains.

## 4.2 Substrate (PolkadotSDK's blockchain building framework)
* **Substrate**: A framework for building blockchains. It provides a set of tools and libraries for building blockchains, including networking, transaction pools, APIs, and other core components. It is mostly un-opinionated and allows developers to build their own blockchain applications. It has the following features:
  * **Modularity**: Substrate is modular, allowing developers to choose which components they want to use and build their blockchain on top of it.
  * **Flexibility**/**Extensibility**: Substrate is extensible and customizable, allowing developers to add their own functionality to the blockchain.
  * **Upgradability**: Substrate is designed to be upgradable, allowing developers to upgrade their blockchain without downtime. The forkless and seamless upgrades are a distinctive feature of the Polkadot SDK.

### 4.2.1 Substrate Wasm Meta Protocol

* **Meta Protocol**: A meta protocol is a protocol that defines rules for creating and modifying other protocols. In the context of `Polkadot` and `Substrate`, the `Substrate Wasm Meta Protocol` refers to a mechanism for `defining`, `updating`, and `executing` the logic that governs the blockchain runtime.
* **Wasm**: WebAssembly (Wasm) is a lightweight, platform-agnostic binary instruction format designed for high-performance applications. It allows code written in languages like Rust, C++, or Go to run in web browsers and other environments, including blockchains. It is a portable, efficient runtime execution environment.
* **Substrate Wasm Meta Protocol**: The Substrate Wasm Meta Protocol describes the dynamic, modular architecture in Substrate and Polkadot, where:
  * Blockchain Runtime:
    1. Encapsulated in a Wasm binary.
    2. Contains all the business logic, including consensus mechanisms, transaction validation rules, and governance processes.
  * Runtime Upgrades:
    1. Instead of requiring hard forks, the runtime logic can be upgraded on-chain by submitting a new Wasm binary.
    2. The meta protocol defines the process for proposing, validating, and deploying these upgrades.
  * Chain Flexibility:
    1. Because the runtime is abstracted, a single chain can evolve its behavior over time. This enables rapid development and adaptation to new use cases or requirements.

![Diagram Description](images/substrate-based-node.svg)

![Diagram Description](images/substrate|node|runtime.png)

![Diagram Description](images/comm-path-in-node.png)

## 4.3 Polkadot SDK Storage Overview
* Blocks: Stored sequentially, containing transactions and state root hashes.
* Current State: Stored in a Merkle Trie, with the state root hash providing a compact representation.
* Database: In Polkadot, RocksDB (or optionally ParityDB) is used - these are NoSQL, key-value databases. The same database stores both blocks and state, but in different ways:

  * Block Storage:
  ```
    Key                     Value
    -----------------------------------------
    block:0                 <block_0_data>
    block:1                 <block_1_data>
    block:2                 <block_2_data>
    block_hash:0xabc...     <block_data>      # Hash-based lookup
  ```

  * State Storage:
  ```
    Key                              Value
    -----------------------------------------
    state:account:alice              <account_data>
    state:balance:alice             100
    state:custom_pallet:counter     42
  ```
  * **How it works**:
  The database itself doesn't know about sequences or trees. The node software:
    * Uses prefix-based keys for different data types
    * Implements Merkle trie logic in code and Manages state trie structure
    * Maintains relationships between data
    * Handles sequential block access

## 4.4 Extrinsics in Polkadot SDK
In Polkadot SDK based blockchains, the transactions (extrinsics) can be:
- **Signed**: These are transactions that have been signed by the sender and are ready to be included in a block.
- **Unsigned**: These are transactions that have been submitted without a signature, often requiring custom validation logic.
- **Inherent**: typically inserted directly into blocks (like current timestamp) by block authoring nodes, without gossiping between peers

## 4.5 Off Chain Workers (OWCs) and Oracles in Polkadot SDK
* **Off-chain Workers (OWCs)**: These are background tasks that run outside of the blockchain's consensus process. They can be used for various purposes, such as fetching data from external sources, executing complex computations, or performing other tasks that don't require consensus. OCWs run OUTSIDE the blockchain's deterministic environment.
* **Oracles**: Oracles are EXTERNAL services that submit data TO the blockchain in the form of transactions.

* Practical Application:
  ```
    Use Case    → Implementation Choice

    Low-value data     → OCW direct fetch
    High-value data    → Oracle service
    Complex compute    → OCW processing
    Critical data      → Multiple oracles
  ```

* Integration Paths:
  ```
    Parachain A                    Parachain B
        ├── OCW fetches data          ├── Uses oracle data
        ├── Validates locally         ├── Pays for access
        └── Submits to chain          └── Trusts oracle source
  ```

# 5. FRAME
* **FRAME** is a rust-based framework for building Substrate-based blockchains/substrate runtimes by providing re-usable building blocks.

## 5.1 Pallets
* FRAME takes the opinion that the blockchain runtime should be composed of individual modules called `pallets`.
![Diagram Description](images/frame-overview.png)
* Essential components of a `pallet`:
  * **Calls/Dispatchable Extrinsics**: These are functions that can be called on the pallet. They can be used to interact with the blockchain, such as transferring balance. A transaction will specify the call to dispatch.
  * **Storage Items**: These are the data that the pallet manages. They can be stored in the blockchain's state.
  * **Events**: These are the events that the pallet emits. They can be used to notify users of important events, such as a balance change.
  * **Errors**: These are the errors that the pallet can return. They can be used to indicate that a call failed.
  * **Hooks**: Hooks allow you to define logic that runs at specific points in the lifecycle of a block such as the beginning or end of a block.

# 6. XCM
* **XCM** (Cross-Chain Messaging) is a **language** for communicating **intentions** between **Consensus Systems**. It allows different blockchains to communicate with each other. It is a way for different blockchains to interact with each other, such as transferring assets, executing smart contracts, or performing other tasks that don't require consensus.
* XCM adheres to four guiding principles that ensure robust and reliable communication across consensus systems:
  * **Asynchronous** - XCM messages operate independently of sender acknowledgment, avoiding delays due to blocked processes
  * **Absolute** - XCM messages are guaranteed to be delivered and interpreted accurately, in order, and timely. Once a message is sent, one can be sure it will be processed as intended
  * **Asymmetric** - XCM messages follow the 'fire and forget' paradigm meaning no automatic feedback is provided to the sender. Any results must be communicated separately to the sender with an additional message back to the origin
  * **Agnostic** - XCM operates independently of the specific consensus mechanisms, making it compatible across diverse systems.

# 7. Parachains
* Parachains are Polkadot's unique version of shards or roll-ups functioning as parallel block chains that independently run and process transactions.
* Parachain network maintains its own blockchain and state. Polkadot validators process the parachain blocks and hold them for a short time (24 hours) for data availability for approvals and disputes processing. The Polkadot relay chain only stores a shortened digest of the parachain block. Storing the whole blocks indefinitely would bloat the Polkadot blockchain and beats the purpose of sharding.

## 7.1 Collators
* **Parachain-Specific Nodes**: Collators are nodes specific to a parachain, not randomly selected from the Polkadot Relay Chain. They maintain the full state of their parachain, collect transactions, and produce blocks for validation.
* **Block Authors**: They act as block producers (similar to "miners" or "validators" in other chains) for their parachain, creating candidate blocks to be validated by Polkadot's Relay Chain validators.
* If you create a parachain, you must run your own collator nodes. Polkadot does not provide collators; they are the responsibility of the parachain team. Collators ensure the parachain remains operational by producing blocks and interacting with the Relay Chain.

## 7.2 Backing Group / Polkadot Core / Execution Core
* A **subset** of Polkadot’s Relay Chain validators (randomly assigned to parachains) verifies parachain blocks. They check validity and availability before inclusion in the Relay Chain.
* **Consensus Finalization**: While collators produce blocks, finality is achieved only after Relay Chain validators approve them. This ensures shared security across Polkadot.

![Diagram Description](images/storage-relay-vs-parachain.svg)

## 7.3 Parachain Block Validation and Inclusion

```
                            Polkadot Data Availability Process
                                     +-----------------------+
                                     |     Parachain Block    |
                                     | (Collation by Collator)|
                                     +-----------+-----------+
                                                 |
                                                 v
                                     +-----------------------+
                                     |   Backing Group       |
                                     | (Validate Correctness)|
                                     +-----------+-----------+
                                                 |
                                                 v
                                     +-----------------------+
                                     | Reed-Solomon Erasure  |
                                     | Coding & Distribution |
                                     +-----------+-----------+
                                                 |
                                                 v
               +---------------------+--------------------------+---------------------+
               |                     |                          |                     |
               v                     v                          v                     v
      +----------------+   +----------------+         +----------------+   +----------------+
      | Validator 1    |   | Validator 2    |   ...   | Validator N    |   | Validator N+1  |
      | (Chunk Storage)|   | (Chunk Storage)|         | (Chunk Storage)|   | (Chunk Storage)|
      +----------------+   +----------------+         +----------------+   +----------------+
                                                 |
                                                 v
                                     +-----------------------+
                                     | Relay Chain Block     |
                                     | - Parachain Header    |
                                     | - Erasure-Coded Root  |
                                     | - Validity Proofs     |
                                     +-----------------------+
```

### 7.3.1 Overview
After a collator creates a parachain block (collation), it undergoes two critical stages before being included in the Relay Chain block:
1. **Backing Process**: The collation is validated by a subset of Relay Chain validators (the backing group) to ensure correctness.
2. **Data Availability Stage**: Ensures the collation’s data is stored redundantly across the network so it can be reconstructed later if needed.
3. **Approval Checking and Disputes**: A **random subset of validators** (not in the backing group) is selected to re-check the block. Validators submit approval votes to the Relay Chain. If there is even one validator which rejected the collation, it creates a `Dispute` and all validators in the relay chain then verify the collation and the stake of the malicious actor gets slashed.
4. **Finalization**: In the end, the block is finalized via `GRANDPA` (Polkadot’s finality gadget). `GRANDPA` would never finalize a disputed block.

### 7.3.2 Collation vs PoV (Proof of Validity) Block
Key Differences
Aspect	Collation	Proof of Validity (PoV)
Content	Transactions, state changes, block data.	Cryptographic proof of the collation’s validity.
Created By	Collators (parachain-specific nodes).	Collators (using the parachain’s logic).
Role in Validation	Raw data to be validated.	Enables efficient validation by Relay Chain.
Storage	Stored on parachain nodes.	Submitted to Relay Chain alongside collations.
Dependency	Standalone block data.	Dependent on the collation it proves.

### 7.3.3 Key Steps
* **`Reed-Solomon Erasure Coding`**: The parachain block data is split into N chunks and encoded into 2N chunks using Reed-Solomon codes. Even if up to N chunks are lost, the original data can still be recovered.
  * **Why?**: To prevent data withholding attacks (e.g., a malicious validator hiding data to break consensus).
  * **Distribution**: Chunks are distributed to all Relay Chain validators, not just the backing group.

* **`Data Availability Confirmation`**: Validators attest that they’ve received their assigned chunks. Once a threshold of validators (e.g., 2/3) confirms availability, the collation is marked as "available."

* **Inclusion in the Relay Chain**: The Relay Chain does not store the full parachain block. Instead, it stores:
  * `Parachain Block Header`: A condensed summary of the parachain block.
  * `Erasure-Coded Root`: A cryptographic commitment (e.g., Merkle root) to the erasure-coded chunks. Proof that the parachain block data is available and recoverable.
  * `Validity Attestations`: Signatures from the backing group confirming the block’s validity.

![Diagram Description](images/data-availability-from-parachains.svg)


