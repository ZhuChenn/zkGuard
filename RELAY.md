# Program Deployment

In order to deploy and test the zkGuard program you need to do some steps before:

- Compile the program
- Deploy to localnet or devnet
- Run tests
- Create the docker images for testing
- Start compose file for local development

## Compile the Program

```sh
cargo build-sbf --release
```

This will compile the program to BPF bytecode, which is the format Solana uses for on-chain programs.

## Deploy to Localnet

First, start a local Solana test validator:

```sh
solana-test-validator
```

In another terminal, deploy the program:

```sh
solana program deploy target/deploy/zkguard.so
```

## Deploy to Devnet

Set your cluster to devnet and deploy:

```sh
solana config set --url devnet
solana airdrop 2
solana program deploy target/deploy/zkguard.so
```

## Run Tests

Run the test suite:

```sh
cargo test
cargo test-sbf
```

## Create Docker Images for Testing

```sh
docker/scripts/build-zkguard-image-injected.sh
```

## Start Docker Compose for Local Development

```sh
docker-compose -f docker/compose/zkguard-docker-compose.yaml up
```

This will start a local Solana validator and the necessary services for testing.

## Program Instructions

The zkGuard program supports the following main instructions:

- `VerifyGroth16`: Verify a Groth16 proof
- `VerifyPlonk`: Verify a PLONK/UltraPLONK proof  
- `VerifyFFlonk`: Verify an FFLONK proof
- `VerifyRisc0`: Verify a RISC Zero proof
- `VerifySP1`: Verify an SP1 proof
- `AggregateProofs`: Aggregate multiple proofs for batch verification
- `UpdateVerificationKey`: Update a verification key (requires authority)

## Account Structure

The program uses the following account structure:

- **Verification Key Accounts**: Store verification keys for different proof systems
- **Proof Accounts**: Store submitted proofs and their verification status
- **Aggregation Accounts**: Store aggregated proofs for batch verification
- **Program Derived Addresses (PDAs)**: Used for secure account management

## Fee Model

Verification fees are paid in SOL (Solana's native token). Fees are calculated based on computational complexity and are collected by the program.
