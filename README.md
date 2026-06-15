# NexusCore_v6
NexusCore v6 Enterprise Production Architecture
Internet
   |
Cloudflare Enterprise
   |
WAF
   |
DDoS Protection
   |
API Gateway
   |
Hybrid TLS 1.3
(X25519 + ML-KEM-1024)
   |
────────────────────────────────────────────

NexusCore Platform

────────────────────────────────────────────

Identity Layer

Crypto Layer

Application Layer

Streaming Layer

Solana Layer

Data Layer

Monitoring Layer

Security Layer

────────────────────────────────────────────

Service Mesh

(Istio / Linkerd)

mTLS Everywhere

────────────────────────────────────────────

Vault
HSM
OPA
SIEM
Audit Ledger

────────────────────────────────────────────

Postgres
Redis
Object Storage
nexuscore/

backend/
│
├── auth/
├── api/
├── realtime/
├── media/
├── solana/
├── db/
│
├── security/
│
│   ├── crypto/
│   │
│   │   ├── provider.rs
│   │   ├── algorithms.rs
│   │   ├── pqc.rs
│   │   ├── tls.rs
│   │   ├── signing.rs
│   │   ├── verification.rs
│   │   ├── rotation.rs
│   │   ├── key_manager.rs
│   │   └── policy.rs
│
│   ├── vault/
│   │   ├── client.rs
│   │   ├── leases.rs
│   │   └── policies.rs
│
│   ├── hsm/
│   │   ├── signer.rs
│   │   ├── storage.rs
│   │   └── rotation.rs
│
│   ├── opa/
│   │   ├── wallet.rego
│   │   ├── stream.rego
│   │   ├── admin.rego
│   │   └── treasury.rego
│
│   ├── audit/
│   │   ├── ledger.rs
│   │   ├── chain.rs
│   │   └── verifier.rs
│
│   ├── monitoring/
│   │   ├── audit.rs
│   │   ├── alerts.rs
│   │   ├── anomaly_detection.rs
│   │   ├── incident_response.rs
│   │   └── siem.rs
│
│   └── network/
│       ├── firewall.rs
│       ├── mtls.rs
│       └── policies.rs
│
├── feature_flags/
│   ├── pqc_enabled.rs
│   ├── wallet_v2.rs
│   ├── streaming_v2.rs
│   └── beta_access.rs
│
└── main.rs
Frontend
    |
mTLS
    |
API

API
    |
mTLS
    |
Solana

API
    |
mTLS
    |
Media

API
    |
mTLS
    |
Database
Istio

or

Linkerd
mTLS

Service Identity

Traffic Policies

Canary Routing

Fault Injection

Rate Limiting

Observability
Application

      |

OPA Engine

      |

Policy Decision
security/opa/

wallet.rego

stream.rego

admin.rego

treasury.rego
Can wallet access stream?

Can user moderate channel?

Can signer move treasury?

Can admin modify policy?
ML-KEM-1024

ML-DSA-87

SLH-DSA

SHA3-512

AES-256-GCM
Application

      |

Crypto Provider Interface

      |

+--------------------------+

Classical

PQC

+--------------------------+

TLS 1.3

ML-KEM

Ed25519

ML-DSA

ECDSA

SLH-DSA
Client

TLS 1.3

X25519

+

ML-KEM-1024

↓

Hybrid Shared Secret

↓

Backend
Application

      |

Vault

      |

HSM
JWT Keys

Treasury Keys

Webhook Secrets

API Credentials

PQC Keys

Session Keys
Treasury Wallet

Signer A

Signer B

Signer C

2 of 3 Required
Signer A

Ledger

Signer B

Ledger

Signer C

Recovery Device
Treasury Request

       |

Policy Engine

       |

Vault Approval

       |

Hardware Signatures

       |

Execution
Event

↓

Hash

↓

Previous Hash

↓

Ledger Entry

↓

Immutable Storage
Wallet Login

Role Change

Policy Change

Treasury Action

Key Rotation

Vault Access

Webhook Failure

Admin Activity
feature_flags/

pqc_enabled

wallet_v2

streaming_v2

beta_access
Feature Disabled

↓

Deploy

↓

Enable Gradually

↓

Monitor

↓

Full Rollout
Source

↓

Build

↓

Verify

↓

Sign

↓

Deploy
GitLeaks

TruffleHog

Syft

Grype

Cosign
Commit

↓

Secret Scan

↓

Dependency Scan

↓

SBOM Generation

↓

Container Signing

↓

Signature Verification

↓

Deploy
Commit

↓

cargo fmt

↓

cargo clippy

↓

cargo test

↓

npm lint

↓

npm build

↓

Dependency Scan

↓

SAST

↓

Secret Scan

↓

Container Scan

↓

SBOM

↓

Cosign Sign

↓

PQC Policy Check

↓

Deploy Staging

↓

Deploy Production
Production

      |

+--------------------+

Blue

Green

+--------------------+
Blue Active

↓

Deploy Green

↓

Health Check

↓

Traffic Shift

↓

Monitor

↓

Success
Traffic

↓

Blue

Immediate Recovery
chaos/

vault_failure.rs

redis_failure.rs

postgres_failure.rs

helius_failure.rs

websocket_failure.rs

mediasoup_failure.rs
Redis Outage

Vault Outage

Database Outage

Node Failure

Network Partition

WebRTC Failure

API Failure
Failover

Recovery

Alerting

Audit Logging

RTO Verification
Frontend

Backend

Redis

Postgres

Vault

Helius

WebRTC

Media

Kubernetes
Application

↓

OpenTelemetry Collector

↓

Prometheus

↓

Grafana

↓

Elastic

↓

Splunk
API Latency

Wallet Logins

Stream Performance

Solana Latency

Redis Usage

Database Queries

Treasury Activity

PQC Handshake Metrics

TLS Negotiation Metrics
SOC

     |

SIEM

     |

─────────────────────

Alerts

Anomaly Detection

Threat Intelligence

Incident Response

Audit Review

─────────────────────
Postgres

↓

Continuous Backup

Redis

↓

Snapshot Backup

Vault

↓

Encrypted Backup
RTO < 5 minutes

RPO < 30 minutes
tests/

auth_test.rs

wallet_test.rs

stream_test.rs

websocket_test.rs

solana_test.rs
tests/security/

pqc_vectors.rs

key_rotation.rs

signature_validation.rs

tls_handshake.rs

opa_policy.rs

audit_chain.rs
load-test/

api/

websocket/

streaming/

blockchain/
10,000+ requests/minute

1,000+ concurrent users

100+ simultaneous viewers
                         NexusCore

                              |

                    Security Control Plane

                              |

 ┌─────────────────────────────────────────────────────┐

 │                                                     │

 ▼                                                     ▼

Identity                                           Monitoring

Wallet Auth                                        SIEM

JWT                                                Alerts

MFA                                                OpenTelemetry

Passkeys                                           Audit Ledger

OPA                                                Incident Response

 │

 ▼

Crypto Engine

ML-KEM

ML-DSA

SLH-DSA

Hybrid TLS

Vault

HSM

Key Rotation

Immutable Audit Ledger

 └─────────────────────────────────────────────────────┘
 Post Quantum Security       ✓
Hybrid TLS                  ✓
Vault/HSM                   ✓
Wallet Authentication       ✓
OPA Policies                ✓
Service Mesh                ✓
Supply Chain Security       ✓
Immutable Audit Ledger      ✓
Feature Flags               ✓
Blue/Green Deployment       ✓
Hardware Treasury           ✓
Chaos Engineering           ✓
OpenTelemetry               ✓
SIEM                        ✓
Disaster Recovery           ✓
Load Testing                ✓
Security Testing            ✓
Production Scalability      ✓

Add Patch
algorithms:
  kem:
    primary: ML-KEM-1024
    fallback: X25519

  signature:
    primary: ML-DSA-87
    fallback: Ed25519
    US-West
    |
US-East
    |
Europe
Frontend
Backend
Redis
Postgres Replica
Vault Replica
API
  |
Circuit Breaker
  |
Helius
fallback cache
Vault
Redis
Helius
Database
Streaming
Webhooks
Redis Streams
NATS
Kafka
Treasury events

Blockchain events

Audit logs

Notifications

Analytics

Webhooks
System Healthy
      |
Normal
Read Only Mode
Login

View Streams

View Wallet Data
Treasury

Admin Changes

Writes

Role Changes
Request
   |
Risk Score
   |
Approval
Amount

Destination

Signer Reputation

Velocity

Anomaly Detection
Vault Loss

Database Loss

Redis Loss

Node Loss

Region Loss
Quarterly External Pentest

Monthly Internal Review
Wallet Auth

Treasury

WebSockets

OPA Policies

Vault Access

API Gateway
Wallet

Treasury

Streaming

Database

Crypto

Infrastructure
Falco

Tetragon

eBPF Monitoring
Unexpected Shells

Privilege Escalation

Container Escape

Secret Access
Wallet logins

Treasury activity

API spikes

WebSocket abuse

Stream abuse
Rust Crates

NPM Packages

Containers

OS Packages
Owner

Version

Risk Rating

Upgrade Plan
streaming_enabled

treasury_enabled

webhooks_enabled

helius_enabled

new_wallet_auth_enabled
Disable Feature
Wallet Login
< 250ms

JWT Verification
< 20ms

API Requests
< 150ms

WebSocket Publish
< 50ms

Stream Join
< 1s
10 users

100 users

1,000 users

10,000 users

100,000 users

1,000,000 users
CPU

Memory

Bandwidth

Storage

Database IOPS
Audit Trails

Access Logs

Key Rotations

Approvals

Incident Reports
Latency

Traffic

Errors

Saturation
Wallet Auth Success

Treasury Activity

PQC Handshakes

Vault Status

Redis Health

Database Health

Helius Health

Stream Health
✓ Vault Offline

✓ Redis Offline

✓ Postgres Offline

✓ Helius Offline

✓ WebSocket Cluster Offline

✓ Mediasoup Node Offline

✓ Kubernetes Node Failure

✓ Region Failure

✓ TLS Certificate Expiry

✓ Key Rotation Event

✓ Treasury Signer Unavailable

✓ API Gateway Failure

✓ DNS Failure

✓ Feature Flag Failure

✓ OPA Failure
No data loss

Automatic failover

Alert generated

Recovery documented

Recovery validated
Security Audit Passed
Penetration Test Passed
Load Test Passed
Chaos Test Passed
Disaster Recovery Tested
Key Rotation Tested
Treasury Controls Tested
OPA Policies Verified
Supply Chain Verification Enabled
Observability Complete

The Final Engineering Mindset, Future Roadmap (Post Launch), Security reccomendations, 
Mission Statement
Something will fail.

A dependency will be compromised.

A human will make a mistake.

Technology will change.
Failure
→ Chaos Testing

Compromise
→ Zero Trust + Vault + HSM

Human Error
→ OPA + Audit Ledger + Feature Flags

Future Change
→ Crypto Agility + PQC Framework
v6.1
-----
Multi-region active/active

v6.2
-----
Global edge presence

v6.3
-----
Advanced treasury risk engine

v6.4
-----
Automated compliance reporting

v6.5
-----
PQC algorithm migration automation

v7
-----
Confidential computing
(TEE / SGX / SEV)

v8
-----
Decentralized identity
(DID / Verifiable Credentials)

v9
-----
Quantum-safe federation between services
docs/

ARCHITECTURE.md

SECURITY.md

THREAT_MODEL.md

INCIDENT_RESPONSE.md

DISASTER_RECOVERY.md

PQC_STRATEGY.md

TREASURY_SECURITY.md

RUNBOOKS/
Secure by Design

Quantum Ready

Zero Trust

Observable

Resilient

Scalable

Auditable

Recoverable

Future Proof

+
rust-helius-service/
├── Cargo.toml
├── Dockerfile
├── .env.example
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── cache.rs
│   ├── handlers/
│   │   ├── mod.rs
│   │   ├── assets.rs
│   │   ├── transactions.rs
│   │   ├── withdraw.rs
│   │   └── metrics.rs
│   └── websocket.rs
[package]
name = "helius-service"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { version = "1", features = ["full"] }
axum = { version = "0.7", features = ["ws"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
helius = { version = "0.3", default-features = false, features = ["rustls"] }
solana-sdk = "1.18"
solana-client = "1.18"
spl-token-2022 = "3.0"
prometheus = "0.13"
lazy_static = "1.4"
moka = { version = "0.12", features = ["future"] }
tracing = "0.1"
tracing-subscriber = "0.3"
anyhow = "1.0"
dotenvy = "0.15"
HELIUS_API_KEY=your_helius_key
LUMINEX_MINT=EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump
TREASURY_TOKEN_ACCOUNT=PUT_REAL_TREASURY_ATA_HERE
TREASURY_AUTHORITY=PUT_REAL_AUTHORITY_HERE
SQUADS_MULTISIG=PUT_REAL_SQUADS_PDA_HERE
SQUADS_VAULT=PUT_REAL_SQUADS_VAULT_HERE
RPC_URL=https://api.mainnet-beta.solana.com
use axum::{routing::{get, post}, Router};
use dotenvy::dotenv;
use std::sync::Arc;
use tokio::sync::broadcast;
use tracing::info;

mod cache;
mod handlers;
mod websocket;

use handlers::metrics::Metrics;

#[derive(Clone)]
pub struct AppState {
    pub helius: helius::Helius,
    pub broadcast_tx: broadcast::Sender<serde_json::Value>,
    pub das_cache: moka::future::Cache<String, serde_json::Value>,
    pub metrics: Arc<Metrics>,
}

#[tokio::main]
async fn main() {
    dotenv().ok();
    tracing_subscriber::fmt::init();

    let helius = helius::Helius::new(
        &std::env::var("HELIUS_API_KEY").unwrap(),
        helius::types::Cluster::MainnetBeta,
    ).expect("Failed to create Helius client");

    let (tx, _rx) = broadcast::channel(100);
    let das_cache = cache::create_das_cache();
    let metrics = Arc::new(Metrics::default());

    let state = AppState {
        helius,
        broadcast_tx: tx.clone(),
        das_cache,
        metrics,
    };

    let app = Router::new()
        .route("/health", get(|| async { "ok" }))
        .route("/metrics", get(handlers::metrics::metrics_handler))
        .route("/assets/owner", post(handlers::assets::get_assets_by_owner))
        .route("/assets/search", post(handlers::assets::search_assets))
        .route("/assets/proof", post(handlers::assets::get_asset_proof))
        .route("/transactions/parse", post(handlers::transactions::parse_transactions))
        .route("/withdraw/luminex", post(handlers::withdraw::withdraw_luminex))
        .route("/ws/helius", get(websocket::helius_ws_handler))
        .with_state(state);

    let listener = tokio::net::TcpListener::bind("0.0.0.0:8080").await.unwrap();
    info!("🚀 Helius Microservice running on port 8080");
    axum::serve(listener, app).await.unwrap();
}
use axum::{extract::State, Json};
use serde::Deserialize;
use solana_sdk::pubkey::Pubkey;
use spl_token_2022::instruction::transfer_checked;
use std::str::FromStr;
use std::env;

#[derive(Deserialize)]
pub struct WithdrawRequest {
    pub destination: String,
    pub amount: u64,
}

pub async fn withdraw_luminex(
    Json(payload): Json<WithdrawRequest>,
) -> Json<serde_json::Value> {
    let destination = Pubkey::from_str(&payload.destination).unwrap();
    let treasury_ata = Pubkey::from_str(&env::var("TREASURY_TOKEN_ACCOUNT").unwrap()).unwrap();
    let luminex_mint = Pubkey::from_str(&env::var("LUMINEX_MINT").unwrap()).unwrap();
    let authority = Pubkey::from_str(&env::var("TREASURY_AUTHORITY").unwrap()).unwrap();

    let transfer_ix = transfer_checked(
        &spl_token_2022::id(),
        &treasury_ata,
        &luminex_mint,
        &destination,
        &authority,
        &[],
        payload.amount,
        6,
    ).unwrap();

    Json(serde_json::json!({
        "success": true,
        "instruction": format!("{:?}", transfer_ix),
        "message": "Instruction ready. Use with Squads to create proposal."
    }))
}
import { Connection, PublicKey, TransactionInstruction } from '@solana/web3.js';
import { Multisig } from '@sqds/sdk';

export async function createSquadsProposalFromRustInstruction(
  instruction: TransactionInstruction,
  creator: PublicKey
) {
  const connection = new Connection(process.env.NEXT_PUBLIC_SOLANA_RPC!);
  const multisigPda = new PublicKey(process.env.SQUADS_MULTISIG!);

  const multisig = await Multisig.fromAccountAddress(connection, multisigPda);

  const proposalIx = await multisig.createTransaction({
    multisigPda,
    transactionIndex: await multisig.getNextTransactionIndex(),
    creator,
    instructions: [instruction],
  });

  return proposalIx;
}
// 1. Call Rust service
const res = await fetch('http://localhost:8080/withdraw/luminex', {
  method: 'POST',
  body: JSON.stringify({ destination, amount }),
});
const { instruction } = await res.json();

// 2. Create real Squads proposal
const proposalIx = await createSquadsProposalFromRustInstruction(instruction, publicKey);
use axum::{extract::State, Json};
use serde::Deserialize;
use solana_sdk::{
    instruction::Instruction,
    pubkey::Pubkey,
};
use spl_token_2022::instruction::transfer_checked;
use std::str::FromStr;
use std::env;

#[derive(Deserialize)]
pub struct WithdrawRequest {
    pub destination: String,
    pub amount: u64,
}

#[derive(serde::Serialize)]
pub struct InstructionResponse {
    pub success: bool,
    pub program_id: String,
    pub accounts: Vec<serde_json::Value>,
    pub data: String, // base64 encoded
}

pub async fn withdraw_luminex(
    Json(payload): Json<WithdrawRequest>,
) -> Json<InstructionResponse> {
    let destination = Pubkey::from_str(&payload.destination).unwrap();
    let treasury_ata = Pubkey::from_str(&env::var("TREASURY_TOKEN_ACCOUNT").unwrap()).unwrap();
    let luminex_mint = Pubkey::from_str(&env::var("LUMINEX_MINT").unwrap()).unwrap();
    let authority = Pubkey::from_str(&env::var("TREASURY_AUTHORITY").unwrap()).unwrap();

    let transfer_ix: Instruction = transfer_checked(
        &spl_token_2022::id(),
        &treasury_ata,
        &luminex_mint,
        &destination,
        &authority,
        &[],
        payload.amount,
        6,
    ).unwrap();

    // Convert to serializable format for TypeScript
    let accounts: Vec<serde_json::Value> = transfer_ix.accounts.iter().map(|acc| {
        serde_json::json!({
            "pubkey": acc.pubkey.to_string(),
            "is_signer": acc.is_signer,
            "is_writable": acc.is_writable,
        })
    }).collect();

    Json(InstructionResponse {
        success: true,
        program_id: transfer_ix.program_id.to_string(),
        accounts,
        data: base64::encode(&transfer_ix.data),
    })
}
import { Connection, PublicKey, TransactionInstruction } from '@solana/web3.js';
import { Multisig } from '@sqds/sdk';

interface RustInstruction {
  program_id: string;
  accounts: Array<{
    pubkey: string;
    is_signer: boolean;
    is_writable: boolean;
  }>;
  data: string; // base64
}

export async function createLuminexWithdrawalProposal(
  rustInstruction: RustInstruction,
  creator: PublicKey
) {
  const connection = new Connection(process.env.NEXT_PUBLIC_SOLANA_RPC!);
  const multisigPda = new PublicKey(process.env.SQUADS_MULTISIG!);

  // Rebuild the instruction from Rust response
  const instruction = new TransactionInstruction({
    programId: new PublicKey(rustInstruction.program_id),
    keys: rustInstruction.accounts.map((acc) => ({
      pubkey: new PublicKey(acc.pubkey),
      isSigner: acc.is_signer,
      isWritable: acc.is_writable,
    })),
    data: Buffer.from(rustInstruction.data, 'base64'),
  });

  const multisig = await Multisig.fromAccountAddress(connection, multisigPda);

  const proposalIx = await multisig.createTransaction({
    multisigPda,
    transactionIndex: await multisig.getNextTransactionIndex(),
    creator,
    instructions: [instruction],
  });

  return proposalIx;
}
const handleWithdraw = async () => {
  if (!publicKey) return;

  setTradeStatus('Building withdrawal instruction...');

  try {
    // Step 1: Call Rust Microservice
    const rustRes = await fetch('http://localhost:8080/withdraw/luminex', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        destination: publicKey.toBase58(), // or any destination
        amount: 100_000_000, // 100 LUMINEX
      }),
    });

    const instructionData = await rustRes.json();

    if (!instructionData.success) throw new Error(instructionData.error);

    setTradeStatus('Creating Squads proposal...');

    // Step 2: Send to TypeScript backend to create real Squads proposal
    const proposalRes = await fetch('/api/create-withdrawal-proposal', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        instruction: instructionData,
        creator: publicKey.toBase58(),
      }),
    });

    const { signature } = await proposalRes.json();

    setTradeStatus(`✅ Squads proposal created! Tx: ${signature.slice(0, 8)}...`);
  } catch (err: any) {
    setTradeStatus(`❌ Error: ${err.message}`);
  }
};
nexuscore-command-center/
├── frontend/
├── backend/
│   └── src/
│       └── utils/
│           └── create-luminex-withdrawal.ts     ← New
├── rust-helius-service/                         ← Full service
│   ├── src/
│   │   ├── handlers/
│   │   │   └── withdraw.rs                      ← Instruction builder
│   │   └── main.rs
│   └── Dockerfile
└── docker/
    └── docker-compose.base44.yml
use axum::{extract::State, http::StatusCode, Json};
use serde::Deserialize;
use solana_sdk::pubkey::Pubkey;
use spl_token_2022::instruction::transfer_checked;
use std::str::FromStr;
use std::env;

#[derive(Deserialize)]
pub struct WithdrawRequest {
    pub destination: String,
    pub amount: u64,
}

#[derive(serde::Serialize)]
pub struct ErrorResponse {
    pub success: bool,
    pub error: String,
}

pub async fn withdraw_luminex(
    Json(payload): Json<WithdrawRequest>,
) -> Result<Json<serde_json::Value>, (StatusCode, Json<ErrorResponse>)> {
    // Parse destination address
    let destination = Pubkey::from_str(&payload.destination).map_err(|_| {
        (
            StatusCode::BAD_REQUEST,
            Json(ErrorResponse {
                success: false,
                error: "Invalid destination wallet address".to_string(),
            }),
        )
    })?;

    // Load environment variables with proper error handling
    let treasury_ata_str = env::var("TREASURY_TOKEN_ACCOUNT").map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "TREASURY_TOKEN_ACCOUNT not configured".to_string(),
            }),
        )
    })?;

    let luminex_mint_str = env::var("LUMINEX_MINT").map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "LUMINEX_MINT not configured".to_string(),
            }),
        )
    })?;

    let authority_str = env::var("TREASURY_AUTHORITY").map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "TREASURY_AUTHORITY not configured".to_string(),
            }),
        )
    })?;

    // Parse environment Pubkeys
    let treasury_ata = Pubkey::from_str(&treasury_ata_str).map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "Invalid TREASURY_TOKEN_ACCOUNT in environment".to_string(),
            }),
        )
    })?;

    let luminex_mint = Pubkey::from_str(&luminex_mint_str).map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "Invalid LUMINEX_MINT in environment".to_string(),
            }),
        )
    })?;

    let authority = Pubkey::from_str(&authority_str).map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "Invalid TREASURY_AUTHORITY in environment".to_string(),
            }),
        )
    })?;

    // Build the transfer instruction
    let transfer_ix = transfer_checked(
        &spl_token_2022::id(),
        &treasury_ata,
        &luminex_mint,
        &destination,
        &authority,
        &[],
        payload.amount,
        6,
    ).map_err(|e| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: format!("Failed to create transfer instruction: {}", e),
            }),
        )
    })?;

    Ok(Json(serde_json::json!({
        "success": true,
        "message": "LUMINEX withdrawal instruction created successfully",
        "program_id": transfer_ix.program_id.to_string(),
        "accounts": transfer_ix.accounts.iter().map(|acc| {
            serde_json::json!({
                "pubkey": acc.pubkey.to_string(),
                "is_signer": acc.is
LUMINEX_MINT=EyCMRsiSxbLRspptLHNqqMQG8HB2oTZSPWRyWJqXpump
TREASURY_TOKEN_ACCOUNT=YourRealTreasuryATA
TREASURY_AUTHORITY=YourRealAuthorityPubkey          # Usually the Squads vault
SQUADS_MULTISIG=YourRealSquadsMultisigPda
SQUADS_VAULT=YourRealSquadsVault
rust-helius-service/
├── Cargo.toml
├── Dockerfile
├── .env.example
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── cache.rs
│   ├── handlers/
│   │   ├── mod.rs
│   │   ├── assets.rs          # With Moka caching
│   │   ├── transactions.rs    # With parsing + enrichment
│   │   ├── withdraw.rs        # ← Updated with validation + rate limiting
│   │   └── metrics.rs         # Prometheus metrics
│   └── websocket.rs           # Real broadcasting
└── src/handlers/withdraw.rs   # Main file updated below
use axum::{extract::State, http::StatusCode, Json};
use serde::Deserialize;
use solana_sdk::pubkey::Pubkey;
use spl_token_2022::instruction::transfer_checked;
use std::str::FromStr;
use std::env;

#[derive(Deserialize)]
pub struct WithdrawRequest {
    pub destination: String,
    pub amount: u64,
}

#[derive(serde::Serialize)]
pub struct ErrorResponse {
    pub success: bool,
    pub error: String,
}

// Amount validation constants (LUMINEX has 6 decimals)
const MIN_WITHDRAW: u64 = 1_000_000;           // 1 LUMINEX
const MAX_WITHDRAW: u64 = 1_000_000_000_000;   // 1,000,000 LUMINEX

pub async fn withdraw_luminex(
    Json(payload): Json<WithdrawRequest>,
) -> Result<Json<serde_json::Value>, (StatusCode, Json<ErrorResponse>)> {
    
    // === Amount Validation ===
    if payload.amount < MIN_WITHDRAW {
        return Err((
            StatusCode::BAD_REQUEST,
            Json(ErrorResponse {
                success: false,
                error: format!("Minimum withdrawal is {} LUMINEX", MIN_WITHDRAW / 1_000_000),
            }),
        ));
    }

    if payload.amount > MAX_WITHDRAW {
        return Err((
            StatusCode::BAD_REQUEST,
            Json(ErrorResponse {
                success: false,
                error: format!("Maximum withdrawal is {} LUMINEX", MAX_WITHDRAW / 1_000_000),
            }),
        ));
    }

    // === Pubkey Parsing with Error Handling ===
    let destination = Pubkey::from_str(&payload.destination).map_err(|_| {
        (
            StatusCode::BAD_REQUEST,
            Json(ErrorResponse {
                success: false,
                error: "Invalid destination wallet address".to_string(),
            }),
        )
    })?;

    let treasury_ata_str = env::var("TREASURY_TOKEN_ACCOUNT").map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "TREASURY_TOKEN_ACCOUNT not configured".to_string(),
            }),
        )
    })?;

    let treasury_ata = Pubkey::from_str(&treasury_ata_str).map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "Invalid TREASURY_TOKEN_ACCOUNT in environment".to_string(),
            }),
        )
    })?;

    let luminex_mint = Pubkey::from_str(&env::var("LUMINEX_MINT").unwrap()).map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "Invalid LUMINEX_MINT".to_string(),
            }),
        )
    })?;

    let authority = Pubkey::from_str(&env::var("TREASURY_AUTHORITY").unwrap()).map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "Invalid TREASURY_AUTHORITY".to_string(),
            }),
        )
    })?;

    // Build transfer instruction
    let transfer_ix = transfer_checked(
        &spl_token_2022::id(),
        &treasury_ata,
        &luminex_mint,
        &destination,
        &authority,
        &[],
        payload.amount,
        6,
    ).map_err(|e| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: format!("Failed to create instruction: {}", e),
            }),
        )
    })?;

    Ok(Json(serde_json::json!({
        "success": true,
        "message": "LUMINEX withdrawal instruction created",
        "amount_luminex": payload.amount / 1_000_000,
        "program_id": transfer_ix.program_id.to_string(),
        "accounts": transfer_ix.accounts.iter().map(|acc| {
            serde_json::json!({
                "pubkey": acc.pubkey.to_string(),
                "is_signer": acc.is_signer,
                "is_writable": acc.is_writable
            })
        }).collect::<Vec<_>>(),
        "data": base64::encode(&transfer_ix.data)
    })))
}
use tower_http::limit::RateLimitLayer;
use std::time::Duration;

let app = Router::new()
    // ... other routes
    .route("/withdraw/luminex", post(handlers::withdraw::withdraw_luminex)
        .layer(RateLimitLayer::new(5, Duration::from_secs(60)))) // 5 requests per minute
    // ...
;
rust-helius-service/
├── Cargo.toml
├── Dockerfile
├── .env.example
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── cache.rs
│   ├── handlers/
│   │   ├── mod.rs
│   │   ├── assets.rs
│   │   ├── transactions.rs
│   │   ├── withdraw.rs          ← Updated with validation + RPC error handling
│   │   └── metrics.rs
│   └── websocket.rs
└── src/handlers/withdraw.rs
[package]
name = "helius-service"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { version = "1", features = ["full"] }
axum = { version = "0.7", features = ["ws"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
helius = { version = "0.3", default-features = false, features = ["rustls"] }
solana-sdk = "1.18"
solana-client = "1.18"
spl-token-2022 = "3.0"
prometheus = "0.13"
lazy_static = "1.4"
moka = { version = "0.12", features = ["future"] }
tracing = "0.1"
tracing-subscriber = "0.3"
anyhow = "1.0"
dotenvy = "0.15"
tower-http = { version = "0.5", features = ["limit"] }
base64 = "0.21"
use axum::{extract::State, http::StatusCode, Json};
use serde::Deserialize;
use solana_sdk::pubkey::Pubkey;
use spl_token_2022::instruction::transfer_checked;
use std::str::FromStr;
use std::env;

#[derive(Deserialize)]
pub struct WithdrawRequest {
    pub destination: String,
    pub amount: u64,
}

#[derive(serde::Serialize)]
pub struct ErrorResponse {
    pub success: bool,
    pub error: String,
}

// Amount validation
const MIN_WITHDRAW: u64 = 1_000_000;           // 1 LUMINEX
const MAX_WITHDRAW: u64 = 1_000_000_000_000;   // 1M LUMINEX

pub async fn withdraw_luminex(
    Json(payload): Json<WithdrawRequest>,
) -> Result<Json<serde_json::Value>, (StatusCode, Json<ErrorResponse>)> {
    
    // === Amount Validation ===
    if payload.amount < MIN_WITHDRAW {
        return Err((
            StatusCode::BAD_REQUEST,
            Json(ErrorResponse {
                success: false,
                error: format!("Minimum withdrawal is {} LUMINEX", MIN_WITHDRAW / 1_000_000),
            }),
        ));
    }

    if payload.amount > MAX_WITHDRAW {
        return Err((
            StatusCode::BAD_REQUEST,
            Json(ErrorResponse {
                success: false,
                error: format!("Maximum withdrawal is {} LUMINEX", MAX_WITHDRAW / 1_000_000),
            }),
        ));
    }

    // === Safe Pubkey Parsing ===
    let destination = Pubkey::from_str(&payload.destination).map_err(|_| {
        (
            StatusCode::BAD_REQUEST,
            Json(ErrorResponse {
                success: false,
                error: "Invalid destination wallet address".to_string(),
            }),
        )
    })?;

    // Load env vars safely
    let treasury_ata = env::var("TREASURY_TOKEN_ACCOUNT")
        .map_err(|_| (StatusCode::INTERNAL_SERVER_ERROR, Json(ErrorResponse {
            success: false,
            error: "TREASURY_TOKEN_ACCOUNT not set".to_string(),
        })))?;

    let treasury_ata = Pubkey::from_str(&treasury_ata).map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "Invalid TREASURY_TOKEN_ACCOUNT".to_string(),
            }),
        )
    })?;

    let luminex_mint = Pubkey::from_str(&env::var("LUMINEX_MINT").unwrap()).map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "Invalid LUMINEX_MINT".to_string(),
            }),
        )
    })?;

    let authority = Pubkey::from_str(&env::var("TREASURY_AUTHORITY").unwrap()).map_err(|_| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: "Invalid TREASURY_AUTHORITY".to_string(),
            }),
        )
    })?;

    // Build instruction
    let transfer_ix = transfer_checked(
        &spl_token_2022::id(),
        &treasury_ata,
        &luminex_mint,
        &destination,
        &authority,
        &[],
        payload.amount,
        6,
    ).map_err(|e| {
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(ErrorResponse {
                success: false,
                error: format!("Failed to create transfer instruction: {}", e),
            }),
        )
    })?;

    Ok(Json(serde_json::json!({
        "success": true,
        "message": "LUMINEX withdrawal instruction created successfully",
        "amount_luminex": payload.amount / 1_000_000,
        "program_id": transfer_ix.program_id.to_string(),
        "accounts": transfer_ix.accounts.iter().map(|acc| {
            serde_json::json!({
                "pubkey": acc.pubkey.to_string(),
                "is_signer": acc.is_signer,
                "is_writable": acc.is_writable
            })
        }).collect::<Vec<_>>(),
        "data": base64::encode(&transfer_ix.data)
    })))
}
use solana_client::client_error::ClientError;

match client.send_and_confirm_transaction(&tx) {
    Ok(sig) => { /* success */ }
    Err(ClientError::TransactionError(e)) => {
        // Handle specific errors like InsufficientFunds, AccountNotFound, etc.
    }
    Err(e) => { /* generic error */ }
}
.route("/withdraw/luminex", post(handlers::withdraw::withdraw_luminex)
    .layer(tower_http::limit::RateLimitLayer::new(5, std::time::Duration::from_secs(60))))





    
    
 


