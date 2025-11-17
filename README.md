# zkGuard

Implementation of a **Zero-Knowledge Proof Verification Program** for the Solana blockchain.

This program is built using the [Solana Program Framework](https://docs.solana.com/developing/programming-model/overview) and provides high-performance, on-chain verification of zero-knowledge proofs.

> [!IMPORTANT]
> ***zkGuard*** is built for **mainnet** and ready for production use on the Solana blockchain.

## What is zkGuard

zkGuard is a **Solana Program** (smart contract) designed to provide efficient, on-chain verification for zero-knowledge proofs. As a high-performance, public, decentralized, reliable, and secure solution, zkGuard implements dedicated proof verification methods written in Rust, available to be used in a modular and composable way within the Solana ecosystem.

## How It Works on Solana

### Solana Program Architecture

zkGuard operates as a **Solana Program** (also known as a smart contract) that runs on the Solana blockchain. Unlike traditional blockchain architectures, Solana uses a unique execution model:

- **Programs are Stateless**: Programs on Solana are stateless - they don't store data themselves. Instead, data is stored in **Accounts** that are passed to the program during execution.
- **Account-Based Model**: All state is stored in accounts, which can be owned by programs. The verification results, proof data, and verification keys are stored in dedicated accounts.
- **Transaction-Based Execution**: Verification requests are submitted as transactions that invoke the program's verification instructions.
- **Parallel Execution**: Solana's Sealevel runtime allows multiple transactions to execute in parallel, enabling high-throughput proof verification.

### Key Components

1. **Verification Instructions**: The program exposes various instructions (functions) for different proof systems:
   - Groth16 verification
   - PLONK/UltraPLONK verification
   - FFLONK verification
   - RISC Zero verification
   - SP1 verification
   - EZKL verification
   - Plonky2 verification

2. **Account Structure**:
   - **Verification Key Accounts**: Store verification keys for different proof systems
   - **Proof Accounts**: Store submitted proofs and their verification status
   - **Aggregation Accounts**: Store aggregated proofs for batch verification
   - **Program Derived Addresses (PDAs)**: Used for secure account management

3. **Fee Model**: 
   - Verification fees are paid in SOL (Solana's native token)
   - Fees are calculated based on computational complexity
   - Fees are collected by the program and can be distributed to validators

### Solana-Specific Advantages

- **High Throughput**: Solana's 400ms block time and parallel execution enable rapid proof verification
- **Low Costs**: Transaction fees on Solana are extremely low (typically $0.00025 per transaction)
- **Fast Finality**: Transactions are finalized in ~400ms, providing near-instant verification results
- **Composability**: Programs can easily call other programs, enabling complex verification workflows
- **No Gas Limits**: Solana's compute units system allows for complex verification logic without gas limit concerns

## Motivation

As execution and data availability have been offloaded from monolithic blockchain stacks, we believe that proof verification and settlement need specialization. This system provides such, enabling the next wave of innovation for ZK cryptography within Web3 and beyond.

### Prohibitive Costs on Other Chains

The proof verification market is estimated to incur $100+ million in security expenses alone for zkRollups in 2024, extending to $1.5 billion by 2028 when including ZK applications.
The verification of a single ZK proof on Ethereum can consume upwards of 200,000 to 300,000 gas units, depending on the proof type. Beyond nominal fees today, the variability of future fees inhibits product adoption.

### Solana's Solution

Solana's architecture provides a natural fit for ZK proof verification:
- **Deterministic Fees**: Fixed compute unit costs make fee prediction reliable
- **High Performance**: Parallel execution enables batch verification of multiple proofs
- **Program Upgradability**: Solana programs can be upgraded without hard forks, enabling rapid adoption of new proof systems
- **Native Integration**: Direct integration with Solana's DeFi ecosystem and other programs

### Innovation Without Bottlenecks

Unlike Ethereum's EIP process, Solana programs can be deployed and upgraded by program owners, enabling rapid adoption of cutting-edge proving systems. This flexibility allows the ecosystem to quickly integrate new proof systems and cryptographic primitives without waiting for network-wide upgrades.

## Building and Running

### Prerequisites

Install Rust toolchain. Instructions available [here](https://www.rust-lang.org/tools/install)

Install Solana CLI tools:
```bash
sh -c "$(curl -sSfL https://release.solana.com/stable/install)"
```

For Mac users, after installation run:
```bash
brew install protobuf
```

### Build from source

To build the program from source, clone this repository and run the following commands from the root of the project:

```bash
git checkout <latest tagged release>
cargo build-sbf
```

This will compile the Solana program to BPF (Berkeley Packet Filter) bytecode, which is the format Solana uses for on-chain programs.

### Deploy to Localnet

To deploy to a local Solana test validator:

```bash
# Start a local validator
solana-test-validator

# In another terminal, deploy the program
solana program deploy target/deploy/zkguard.so
```

### Deploy to Devnet

To deploy to Solana devnet:

```bash
# Set cluster to devnet
solana config set --url devnet

# Airdrop some SOL for fees
solana airdrop 2

# Deploy the program
solana program deploy target/deploy/zkguard.so
```

### Deploy to Mainnet

To deploy to Solana mainnet:

```bash
# Set cluster to mainnet
solana config set --url mainnet-beta

# Ensure you have sufficient SOL for deployment fees
# Deploy the program
solana program deploy target/deploy/zkguard.so
```

> **Warning**: Mainnet deployments are permanent. Ensure you have thoroughly tested the program on devnet before deploying to mainnet.

### Run Tests

It is possible to run tests with:

```bash
cargo test
```

For Solana-specific program tests:

```bash
cargo test-sbf
```

## Using the Program

### Submitting a Proof for Verification

To verify a proof, you need to:

1. Create or load the verification key account
2. Create a proof account
3. Submit a transaction with the verification instruction

Example using Solana Web3.js:

```javascript
const transaction = new Transaction().add(
  verifyProofInstruction({
    programId: PROGRAM_ID,
    proofAccount: proofAccountPubkey,
    verificationKeyAccount: vkAccountPubkey,
    // ... other accounts
  })
);

await sendAndConfirmTransaction(connection, transaction, [payer]);
```

### Program Instructions

The program supports the following main instructions:

- `VerifyGroth16`: Verify a Groth16 proof
- `VerifyPlonk`: Verify a PLONK/UltraPLONK proof
- `VerifyFFlonk`: Verify an FFLONK proof
- `VerifyRisc0`: Verify a RISC Zero proof
- `VerifySP1`: Verify an SP1 proof
- `AggregateProofs`: Aggregate multiple proofs for batch verification
- `UpdateVerificationKey`: Update a verification key (requires authority)

## Hardware Requirements for Validators

For running a Solana validator that processes these verification transactions, we recommend:
- **CPU**: 12+ cores (16+ recommended)
- **RAM**: 128GB+ (256GB recommended)
- **Storage**: NVMe SSD with 2TB+ capacity
- **Network**: 1 Gbps+ connection

On-chain compute unit costs have been benchmarked on standard Solana validator hardware.

## Docker

zkGuard includes Docker files for building the program and running test environments.
For more information, see [docker/README.md](docker/README.md).

## Documentation

The official documentation is available at [docs.zkguard.io](https://docs.zkguard.io/).

## License

zkGuard is released under the following licenses:
- [GPL-3](LICENSE-GPL3) for the core verification program and certain verifier implementations.
- [Apache 2.0](LICENSE-APACHE2) for all other components.
