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
backend/src/services/
├── tokenTerminal.ts          # New - Token Terminal API client
├── multiChainWallet.ts       # New - Multi-chain signature verification
└── helius.ts                 # Already exists (calls Rust service)
import axios from 'axios';

const TOKEN_TERMINAL_API_KEY = process.env.TOKEN_TERMINAL_API_KEY;
const BASE_URL = 'https://api.tokenterminal.com/v2';

const tokenTerminalClient = axios.create({
  baseURL: BASE_URL,
  headers: {
    Authorization: `Bearer ${TOKEN_TERMINAL_API_KEY}`,
  },
});

export async function getProtocolMetrics(slug: string) {
  try {
    const { data } = await tokenTerminalClient.get(`/protocols/${slug}/metrics`);
    return data;
  } catch (error) {
    console.error(`[TokenTerminal] Failed to fetch metrics for ${slug}`, error);
    return null;
  }
}

export async function getTopProtocolsByRevenue(limit = 10) {
  try {
    const { data } = await tokenTerminalClient.get('/protocols', {
      params: { sort_by: 'revenue', limit },
    });
    return data;
  } catch (error) {
    console.error('[TokenTerminal] Failed to fetch top protocols', error);
    return [];
  }
}
import express from 'express';
import { getTopProtocolsByRevenue, getProtocolMetrics } from '../services/tokenTerminal';

const router = express.Router();

router.get('/protocols/top', async (req, res) => {
  const data = await getTopProtocolsByRevenue(15);
  res.json({ success: true, data });
});

router.get('/protocols/:slug', async (req, res) => {
  const { slug } = req.params;
  const data = await getProtocolMetrics(slug);
  res.json({ success: !!data, data });
});

export default router;
import metricsRouter from './routes/metrics';
app.use('/api/metrics', metricsRouter);
import { verifyMessage as verifyEvmMessage } from 'viem';
import * as ed from '@noble/ed25519';

// Verify EVM signature (Ethereum, Base, etc.)
export async function verifyEvmSignature(
  message: string,
  signature: string,
  address: string
): Promise<boolean> {
  try {
    return await verifyEvmMessage({ address: address as `0x${string}`, message, signature: signature as `0x${string}` });
  } catch {
    return false;
  }
}

// Verify Solana signature
export async function verifySolanaSignature(
  message: string,
  signature: string,
  publicKey: string
): Promise<boolean> {
  try {
    const messageBytes = new TextEncoder().encode(message);
    const signatureBytes = Buffer.from(signature, 'base64');
    const publicKeyBytes = Buffer.from(publicKey, 'base64');

    return await ed.verify(signatureBytes, messageBytes, publicKeyBytes);
  } catch {
    return false;
  }
}
import axios from 'axios';
import { getCache, setCache } from '../utils/redis';

const TOKEN_TERMINAL_API_KEY = process.env.TOKEN_TERMINAL_API_KEY;
const BASE_URL = 'https://api.tokenterminal.com/v2';

const client = axios.create({
  baseURL: BASE_URL,
  headers: {
    Authorization: `Bearer ${TOKEN_TERMINAL_API_KEY}`,
  },
  timeout: 10000,
});

interface ProtocolMetrics {
  revenue: number;
  fees: number;
  tvl: number;
  active_users: number;
  [key: string]: any;
}

/**
 * Get protocol metrics with Redis caching (5 min TTL)
 */
export async function getProtocolMetrics(slug: string): Promise<ProtocolMetrics | null> {
  const cacheKey = `tokenterminal:protocol:${slug}`;

  const cached = await getCache<ProtocolMetrics>(cacheKey);
  if (cached) return cached;

  try {
    const { data } = await client.get(`/protocols/${slug}/metrics`);
    await setCache(cacheKey, data, 300); // 5 minutes
    return data;
  } catch (error) {
    console.error(`[TokenTerminal] Failed to fetch ${slug}:`, error);
    return null;
  }
}

/**
 * Get top protocols by revenue (cached 10 min)
 */
export async function getTopProtocolsByRevenue(limit = 20) {
  const cacheKey = `tokenterminal:top:revenue:${limit}`;

  const cached = await getCache(cacheKey);
  if (cached) return cached;

  try {
    const { data } = await client.get('/protocols', {
      params: { sort_by: 'revenue', limit },
    });
    await setCache(cacheKey, data, 600); // 10 minutes
    return data;
  } catch (error) {
    console.error('[TokenTerminal] Failed to fetch top protocols:', error);
    return [];
  }
}
import { verifyMessage } from 'viem';
import * as ed from '@noble/ed25519';

export interface MultiChainSignature {
  chain: 'solana' | 'ethereum' | 'base' | 'bsc';
  message: string;
  signature: string;
  address: string;
}

/**
 * Verify EVM signature (Ethereum, Base, BSC, etc.)
 */
export async function verifyEvmSignature(
  message: string,
  signature: string,
  address: string
): Promise<boolean> {
  try {
    return await verifyMessage({
      address: address as `0x${string}`,
      message,
      signature: signature as `0x${string}`,
    });
  } catch (error) {
    console.error('[MultiChain] EVM verification failed:', error);
    return false;
  }
}

/**
 * Verify Solana signature
 */
export async function verifySolanaSignature(
  message: string,
  signature: string,
  publicKey: string
): Promise<boolean> {
  try {
    const messageBytes = new TextEncoder().encode(message);
    const signatureBytes = Buffer.from(signature, 'base64');
    const publicKeyBytes = Buffer.from(publicKey, 'base64');

    return await ed.verify(signatureBytes, messageBytes, publicKeyBytes);
  } catch (error) {
    console.error('[MultiChain] Solana verification failed:', error);
    return false;
  }
}

/**
 * Unified multi-chain signature verification
 */
export async function verifyMultiChainSignature(
  data: MultiChainSignature
): Promise<boolean> {
  const { chain, message, signature, address } = data;

  if (chain === 'solana') {
    return verifySolanaSignature(message, signature, address);
  }

  // EVM chains
  if (['ethereum', 'base', 'bsc'].includes(chain)) {
    return verifyEvmSignature(message, signature, address);
  }

  return false;
}
import express from 'express';
import { verifyMultiChainSignature } from '../services/multiChainWallet';
import { createLuminexWithdrawalProposal } from '../utils/create-luminex-withdrawal';

const router = express.Router();

router.post('/luminex', async (req, res, next) => {
  try {
    const { destination, amount, creator, chain, signature, message } = req.body;

    // Multi-chain signature verification for high-value actions
    if (signature && message) {
      const isValid = await verifyMultiChainSignature({
        chain: chain || 'solana',
        message,
        signature,
        address: creator,
      });

      if (!isValid) {
        return res.status(401).json({
          success: false,
          error: 'Invalid multi-chain signature',
        });
      }
    }

    // Call Rust microservice
    const rustRes = await fetch('http://helius-service:8080/withdraw/luminex', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ destination, amount }),
    });

    const instructionData = await rustRes.json();

    if (!instructionData.success) {
      return res.status(400).json(instructionData);
    }

    // Create Squads proposal
    const proposal = await createLuminexWithdrawalProposal(instructionData, creator);

    res.json({
      success: true,
      message: 'Multi-chain withdrawal proposal created',
      proposal,
    });
  } catch (error) {
    next(error);
  }
});

export default router;
import express from 'express';
import { getTopProtocolsByRevenue, getProtocolMetrics } from '../services/tokenTerminal';
import { getCache, setCache } from '../utils/redis';

const router = express.Router();

router.get('/protocols/top', async (req, res) => {
  const cacheKey = 'metrics:protocols:top';
  const cached = await getCache(cacheKey);

  if (cached) return res.json({ success: true, data: cached, cached: true });

  const data = await getTopProtocolsByRevenue(20);
  await setCache(cacheKey, data, 600); // 10 min cache

  res.json({ success: true, data });
});

router.get('/protocols/:slug', async (req, res) => {
  const { slug } = req.params;
  const cacheKey = `metrics:protocol:${slug}`;

  const cached = await getCache(cacheKey);
  if (cached) return res.json({ success: true, data: cached, cached: true });

  const data = await getProtocolMetrics(slug);
  if (!data) {
    return res.status(404).json({ success: false, error: 'Protocol not found' });
  }

  await setCache(cacheKey, data, 300);
  res.json({ success: true, data });
});

export default router;
// ... existing code ...

/**
 * Invalidate cache by exact key
 */
export async function invalidateCache(key: string): Promise<void> {
  try {
    await redis.del(key);
    console.log(`[Redis] Invalidated key: ${key}`);
  } catch (error) {
    console.error(`[Redis] Failed to invalidate key: ${key}`, error);
  }
}

/**
 * Invalidate multiple keys at once
 */
export async function invalidateMultipleCache(keys: string[]): Promise<void> {
  if (keys.length === 0) return;
  try {
    await redis.del(...keys);
    console.log(`[Redis] Invalidated ${keys.length} keys`);
  } catch (error) {
    console.error('[Redis] Failed to invalidate multiple keys', error);
  }
}

/**
 * Write-through cache update (update cache + invalidate related patterns)
 */
export async function writeThroughCache(
  key: string,
  value: any,
  ttlSeconds = 300,
  relatedPatterns: string[] = []
): Promise<void> {
  try {
    await redis.setex(key, ttlSeconds, JSON.stringify(value));
    
    // Invalidate related patterns
    for (const pattern of relatedPatterns) {
      await invalidateCacheByPattern(pattern);
    }
  } catch (error) {
    console.error(`[Redis] Write-through cache failed for key: ${key}`, error);
  }
}

/**
 * Event-based invalidation (call after important state changes)
 */
export async function invalidateOnEvent(event: string, relatedKeys: string[] = []): Promise<void> {
  console.log(`[Redis] Event-based invalidation triggered: ${event}`);
  
  // Invalidate all related cache keys
  for (const key of relatedKeys) {
    await invalidateCache(key);
  }
  
  // You can also publish to Redis Pub/Sub for distributed invalidation
  await pub.publish('cache:invalidation', JSON.stringify({ event, keys: relatedKeys }));
}
// After writing new data (example)
export async function refreshProtocolMetrics(slug: string) {
  const cacheKey = `tokenterminal:protocol:${slug}`;
  
  // Invalidate old cache
  await invalidateCache(cacheKey);
  
  // Fetch fresh data and write-through
  const freshData = await getProtocolMetricsFromAPI(slug);
  await writeThroughCache(cacheKey, freshData, 300, [`tokenterminal:protocol:*`]);
}
import * as ed from '@noble/ed25519';
import { PublicKey } from '@solana/web3.js';

export interface SolanaSignaturePayload {
  message: string;
  signature: string;
  publicKey: string;
}

/**
 * Verify a Solana message signature
 */
export async function verifySolanaSignature({
  message,
  signature,
  publicKey,
}: SolanaSignaturePayload): Promise<boolean> {
  try {
    const messageBytes = new TextEncoder().encode(message);
    const signatureBytes = Buffer.from(signature, 'base64');
    const publicKeyBytes = new PublicKey(publicKey).toBytes();

    return await ed.verify(signatureBytes, messageBytes, publicKeyBytes);
  } catch (error) {
    console.error('[Solana Verification] Signature verification failed:', error);
    return false;
  }
}

/**
 * Verify + extract signer (useful for high-value actions)
 */
export async function verifyAndGetSigner(payload: SolanaSignaturePayload): Promise<string | null> {
  const isValid = await verifySolanaSignature(payload);
  return isValid ? payload.publicKey : null;
}

/**
 * Verify Solana signature with additional context (recommended for production)
 */
export async function verifySolanaAction(
  payload: SolanaSignaturePayload,
  expectedAction?: string
): Promise<{ valid: boolean; signer?: string }> {
  const isValid = await verifySolanaSignature(payload);
  
  if (!isValid) {
    return { valid: false };
  }

  // Optional: Add action-specific checks (e.g., timestamp, nonce)
  if (expectedAction && !payload.message.includes(expectedAction)) {
    return { valid: false };
  }

  return {
    valid: true,
    signer: payload.publicKey,
  };
}
import * as ed from '@noble/ed25519';
import { PublicKey } from '@solana/web3.js';
import { getCache, setCache } from '../utils/redis';

export interface SolanaSignaturePayload {
  message: string;
  signature: string;
  publicKey: string;
}

const NONCE_TTL_SECONDS = 300; // 5 minutes

/**
 * Verify Solana signature with Timestamp + Nonce validation
 */
export async function verifySolanaActionWithNonce(
  payload: SolanaSignaturePayload,
  expectedAction?: string
): Promise<{ valid: boolean; signer?: string; error?: string }> {
  try {
    const { message, signature, publicKey } = payload;

    // 1. Basic signature verification
    const messageBytes = new TextEncoder().encode(message);
    const signatureBytes = Buffer.from(signature, 'base64');
    const publicKeyBytes = new PublicKey(publicKey).toBytes();

    const isSignatureValid = await ed.verify(signatureBytes, messageBytes, publicKeyBytes);
    if (!isSignatureValid) {
      return { valid: false, error: 'Invalid signature' };
    }

    // 2. Parse message for timestamp and nonce
    // Expected message format: "ACTION:timestamp:nonce:extra_data"
    const parts = message.split(':');
    if (parts.length < 3) {
      return { valid: false, error: 'Invalid message format' };
    }

    const [action, timestampStr, nonce] = parts;
    const timestamp = parseInt(timestampStr, 10);

    // 3. Timestamp validation (must be within last 5 minutes)
    const now = Math.floor(Date.now() / 1000);
    const fiveMinutesAgo = now - 300;

    if (timestamp < fiveMinutesAgo || timestamp > now + 60) {
      return { valid: false, error: 'Timestamp out of acceptable window' };
    }

    // 4. Nonce validation (prevent replay attacks)
    const nonceKey = `nonce:${publicKey}:${nonce}`;
    const usedNonce = await getCache(nonceKey);

    if (usedNonce) {
      return { valid: false, error: 'Nonce already used (replay attack detected)' };
    }

    // Store nonce temporarily
    await setCache(nonceKey, 'used', NONCE_TTL_SECONDS);

    // 5. Optional action validation
    if (expectedAction && action !== expectedAction) {
      return { valid: false, error: `Expected action: ${expectedAction}` };
    }

    return {
      valid: true,
      signer: publicKey,
    };
  } catch (error) {
    console.error('[Solana Verification] Error:', error);
    return { valid: false, error: 'Verification failed' };
  }
}

/**
 * Simple version without nonce (for lower security actions)
 */
export async function verifySolanaSignature(payload: SolanaSignaturePayload): Promise<boolean> {
  try {
    const messageBytes = new TextEncoder().encode(payload.message);
    const signatureBytes = Buffer.from(payload.signature, 'base64');
    const publicKeyBytes = new PublicKey(payload.publicKey).toBytes();

    return await ed.verify(signatureBytes, messageBytes, publicKeyBytes);
  } catch (error) {
    return false;
  }
}
const result = await verifySolanaActionWithNonce({
  message: `WITHDRAW:${Date.now()}:${crypto.randomUUID()}:100000000`,
  signature: "...",
  publicKey: "..."
}, "WITHDRAW");

if (!result.valid) {
  return res.status(401).json({ error: result.error });
}
use axum::{extract::State, Json};
use serde::Deserialize;
use helius::types::{GetAssetRequest, GetAssetProofRequest};

#[derive(Deserialize)]
pub struct ClaimRequest {
    pub user: String,        // User's public key
    pub reward_id: String,   // Identifier for the reward (e.g. game round + rank)
}

pub async fn claim_compressed_nft(
    State(state): State<crate::AppState>,
    Json(payload): Json<ClaimRequest>,
) -> Json<serde_json::Value> {
    // In production, you would verify eligibility here (e.g. from Redis/DB)
    // For now we assume the caller is eligible

    // Example: Use reward_id to determine which compressed NFT to claim
    // This would normally come from your game/reward system
    let asset_id = format!("compressed-reward-{}", payload.reward_id); // placeholder

    // Get asset + proof from Helius
    let asset_request = GetAssetRequest {
        id: asset_id.clone(),
        display_options: None,
    };

    let proof_request = GetAssetProofRequest {
        id: asset_id,
    };

    let asset = match state.helius.rpc().get_asset(asset_request).await {
        Ok(a) => a,
        Err(e) => return Json(serde_json::json!({
            "success": false,
            "error": format!("Failed to fetch asset: {}", e)
        })),
    };

    let proof = match state.helius.rpc().get_asset_proof(proof_request).await {
        Ok(p) => p,
        Err(e) => return Json(serde_json::json!({
            "success": false,
            "error": format!("Failed to fetch proof: {}", e)
        })),
    };

    Json(serde_json::json!({
        "success": true,
        "asset": asset,
        "proof": proof,
        "message": "Use this data to construct Bubblegum claim transaction on frontend"
    }))
}
.route("/claim/compressed-nft", post(handlers::claim::claim_compressed_nft))
import { createTransferInstruction } from '@metaplex-foundation/mpl-bubblegum';

// Use the proof and asset data returned from the endpoint
const ix = createTransferInstruction({
  // ... construct using proof.root, proof.proof, asset, etc.
});
import express from 'express';

const router = express.Router();

router.post('/compressed-nft', async (req, res, next) => {
  try {
    const { user, reward_id } = req.body;

    // Proxy to Rust microservice
    const rustResponse = await fetch('http://helius-service:8080/claim/compressed-nft', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ user, reward_id }),
    });

    const data = await rustResponse.json();

    if (!data.success) {
      return res.status(400).json(data);
    }

    res.json(data);
  } catch (error) {
    next(error);
  }
});

export default router;
'use client';

import { useState } from 'react';
import { useWallet } from '@solana/wallet-adapter-react';
import { Connection, Transaction } from '@solana/web3.js';
import { 
  createTransferInstruction, 
  getAssetWithProof 
} from '@metaplex-foundation/mpl-bubblegum';
import { publicKey } from '@metaplex-foundation/umi';
import { createUmi } from '@metaplex-foundation/umi-bundle-defaults';

export function useClaimCompressedNFT() {
  const { publicKey, sendTransaction } = useWallet();
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const claimCompressedNFT = async (rewardId: string) => {
    if (!publicKey) {
      setError('Wallet not connected');
      return;
    }

    setIsLoading(true);
    setError(null);

    try {
      // 1. Call backend (which proxies to Rust)
      const res = await fetch('/api/claim/compressed-nft', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          user: publicKey.toBase58(),
          reward_id: rewardId,
        }),
      });

      const { success, asset, proof } = await res.json();

      if (!success) throw new Error('Failed to get claim data');

      // 2. Setup Umi + Bubblegum
      const umi = createUmi(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const assetWithProof = await getAssetWithProof(umi, {
        asset: publicKey(asset.id),
        proof: proof.proof.map((p: string) => publicKey(p)),
      });

      // 3. Create transfer/claim instruction (example using transfer)
      const instruction = createTransferInstruction(umi, {
        ...assetWithProof,
        leafOwner: publicKey(publicKey.toBase58()),
        newLeafOwner: publicKey(publicKey.toBase58()), // or another recipient
      });

      // 4. Build and send transaction
      const connection = new Connection(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const transaction = new Transaction().add(instruction);

      const signature = await sendTransaction(transaction, connection);
      await connection.confirmTransaction(signature, 'confirmed');

      return signature;
    } catch (err: any) {
      setError(err.message);
      throw err;
    } finally {
      setIsLoading(false);
    }
  };

  return { claimCompressedNFT, isLoading, error };
}
import claimRouter from './routes/claim';
app.use('/api/claim', claimRouter);
'use client';

import { useState } from 'react';
import { useWallet } from '@solana/wallet-adapter-react';
import { Connection, Transaction } from '@solana/web3.js';
import { 
  createTransferInstruction, 
  getAssetWithProof 
} from '@metaplex-foundation/mpl-bubblegum';
import { publicKey } from '@metaplex-foundation/umi';
import { createUmi } from '@metaplex-foundation/umi-bundle-defaults';

export function useClaimCompressedNFT() {
  const { publicKey, sendTransaction } = useWallet();
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const claimCompressedNFT = async (rewardId: string) => {
    if (!publicKey) {
      setError('Wallet not connected');
      return;
    }

    setIsLoading(true);
    setError(null);

    try {
      // 1. Call backend (which proxies to Rust)
      const res = await fetch('/api/claim/compressed-nft', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          user: publicKey.toBase58(),
          reward_id: rewardId,
        }),
      });

      const { success, asset, proof } = await res.json();

      if (!success) throw new Error('Failed to get claim data');

      // 2. Setup Umi + Bubblegum
      const umi = createUmi(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const assetWithProof = await getAssetWithProof(umi, {
        asset: publicKey(asset.id),
        proof: proof.proof.map((p: string) => publicKey(p)),
      });

      // 3. Create transfer/claim instruction (example using transfer)
      const instruction = createTransferInstruction(umi, {
        ...assetWithProof,
        leafOwner: publicKey(publicKey.toBase58()),
        newLeafOwner: publicKey(publicKey.toBase58()), // or another recipient
      });

      // 4. Build and send transaction
      const connection = new Connection(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const transaction = new Transaction().add(instruction);

      const signature = await sendTransaction(transaction, connection);
      await connection.confirmTransaction(signature, 'confirmed');

      return signature;
    } catch (err: any) {
      setError(err.message);
      throw err;
    } finally {
      setIsLoading(false);
    }
  };

  return { claimCompressedNFT, isLoading, error };
}
const { claimCompressedNFT, isLoading } = useClaimCompressedNFT();

const handleClaim = async () => {
  try {
    const sig = await claimCompressedNFT('round-42-rank-1');
    console.log('Claimed! Signature:', sig);
  } catch (e) {
    console.error(e);
  }
};
'use client';

import { useState } from 'react';
import { useWallet } from '@solana/wallet-adapter-react';
import { 
  createUmi 
} from '@metaplex-foundation/umi-bundle-defaults';
import { 
  publicKey, 
  createSignerFromWalletAdapter 
} from '@metaplex-foundation/umi';
import { 
  createTransferInstruction 
} from '@metaplex-foundation/mpl-bubblegum';
import { Connection, Transaction } from '@solana/web3.js';

export function useClaimCompressedNFT() {
  const { publicKey: walletPublicKey, sendTransaction } = useWallet();
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const claimCompressedNFT = async (rewardId: string) => {
    if (!walletPublicKey) {
      setError('Wallet not connected');
      return;
    }

    setIsLoading(true);
    setError(null);

    try {
      // 1. Get asset + proof from backend (proxies to Rust)
      const res = await fetch('/api/claim/compressed-nft', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          user: walletPublicKey.toBase58(),
          reward_id: rewardId,
        }),
      });

      const { success, asset, proof } = await res.json();
      if (!success) throw new Error('Failed to fetch claim data');

      // 2. Setup Umi
      const umi = createUmi(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const umiPublicKey = publicKey(walletPublicKey.toBase58());

      // 3. Prepare asset with proof (correct format)
      const assetWithProof = {
        leafOwner: umiPublicKey,
        leafDelegate: umiPublicKey,
        merkleTree: publicKey(asset.compression.tree),
        root: publicKey(proof.root),
        dataHash: publicKey(asset.compression.data_hash),
        creatorHash: publicKey(asset.compression.creator_hash),
        nonce: asset.compression.leaf_id,
        index: asset.compression.leaf_id,
        proof: proof.proof.map((p: string) => publicKey(p)),
      };

      // 4. Create transfer instruction (claim = transfer to self or recipient)
      const instruction = createTransferInstruction(umi, {
        ...assetWithProof,
        newLeafOwner: umiPublicKey, // Change this if claiming to another wallet
      });

      // 5. Build and send transaction
      const connection = new Connection(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const transaction = new Transaction().add(instruction);

      const signature = await sendTransaction(transaction, connection);
      await connection.confirmTransaction(signature, 'confirmed');

      return signature;
    } catch (err: any) {
      console.error(err);
      setError(err.message || 'Claim failed');
      throw err;
    } finally {
      setIsLoading(false);
    }
  };

  return { claimCompressedNFT, isLoading, error };
}
'use client';

import { useState } from 'react';
import { useWallet } from '@solana/wallet-adapter-react';
import { 
  createUmi 
} from '@metaplex-foundation/umi-bundle-defaults';
import { 
  publicKey, 
  createNoopSigner,
  transactionBuilder 
} from '@metaplex-foundation/umi';
import { 
  createTransferInstruction 
} from '@metaplex-foundation/mpl-bubblegum';
import { Connection } from '@solana/web3.js';

export function useClaimCompressedNFT() {
  const { publicKey: walletPublicKey, signTransaction } = useWallet();
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const claimCompressedNFT = async (rewardId: string) => {
    if (!walletPublicKey) {
      setError('Wallet not connected');
      return;
    }

    setIsLoading(true);
    setError(null);

    try {
      // 1. Get asset + proof from backend
      const res = await fetch('/api/claim/compressed-nft', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          user: walletPublicKey.toBase58(),
          reward_id: rewardId,
        }),
      });

      const { success, asset, proof } = await res.json();
      if (!success) throw new Error('Failed to get claim data');

      // 2. Setup Umi
      const umi = createUmi(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const signer = createNoopSigner(publicKey(walletPublicKey.toBase58()));

      // 3. Prepare asset with proof
      const assetWithProof = {
        leafOwner: publicKey(walletPublicKey.toBase58()),
        leafDelegate: publicKey(walletPublicKey.toBase58()),
        merkleTree: publicKey(asset.compression.tree),
        root: publicKey(proof.root),
        dataHash: publicKey(asset.compression.data_hash),
        creatorHash: publicKey(asset.compression.creator_hash),
        nonce: asset.compression.leaf_id,
        index: asset.compression.leaf_id,
        proof: proof.proof.map((p: string) => publicKey(p)),
      };

      // 4. Build instruction using Umi
      const instruction = createTransferInstruction(umi, {
        ...assetWithProof,
        newLeafOwner: publicKey(walletPublicKey.toBase58()),
      });

      // 5. Use Umi Transaction Builder (recommended)
      const txBuilder = transactionBuilder()
        .add(instruction);

      // 6. Build, sign and send
      const connection = new Connection(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const tx = await txBuilder.buildAndSign(umi);

      // Convert Umi transaction to web3.js Transaction for wallet adapter
      const web3Tx = Transaction.from(tx.serialize());

      const signature = await signTransaction(web3Tx);
      const txid = await connection.sendRawTransaction(signature.serialize());
      await connection.confirmTransaction(txid, 'confirmed');

      return txid;
    } catch (err: any) {
      console.error(err);
      setError(err.message);
      throw err;
    } finally {
      setIsLoading(false);
    }
  };

  return { claimCompressedNFT, isLoading, error };
}
{
  "name": "LUMINEX Reward #42",
  "symbol": "LUM",
  "description": "Reward for winning Pumpcade Round 42",
  "image": "https://arweave.net/...",
  "animation_url": "...",
  "external_url": "...",
  "attributes": [
    { "trait_type": "Rank", "value": "1" },
    { "trait_type": "Round", "value": "42" }
  ],
  "properties": {
    "files": [...],
    "category": "image"
  },
  "collection": {
    "name": "LUMINEX Rewards",
    "family": "NexusCore"
  },
  "creators": [
    {
      "address": "YourCreatorWallet...",
      "verified": true,
      "share": 100
    }
  ]
}
'use client';

import { useState } from 'react';
import { useWallet } from '@solana/wallet-adapter-react';
import { 
  createUmi 
} from '@metaplex-foundation/umi-bundle-defaults';
import { 
  publicKey, 
  createNoopSigner,
  transactionBuilder 
} from '@metaplex-foundation/umi';
import { 
  createTransferInstruction 
} from '@metaplex-foundation/mpl-bubblegum';
import { Connection } from '@solana/web3.js';

export function useClaimCompressedNFT() {
  const { publicKey: walletPublicKey, signTransaction } = useWallet();
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const claimCompressedNFT = async (rewardId: string) => {
    if (!walletPublicKey) {
      setError('Wallet not connected');
      return;
    }

    setIsLoading(true);
    setError(null);

    try {
      // 1. Fetch asset + proof from backend
      const res = await fetch('/api/claim/compressed-nft', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          user: walletPublicKey.toBase58(),
          reward_id: rewardId,
        }),
      });

      const { success, asset, proof } = await res.json();
      if (!success) throw new Error('Failed to fetch claim data');

      // 2. Initialize Umi
      const umi = createUmi(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const signer = createNoopSigner(publicKey(walletPublicKey.toBase58()));

      // 3. Build optimized asset with proof (Canopy already handled by Helius)
      const assetWithProof = {
        leafOwner: publicKey(walletPublicKey.toBase58()),
        leafDelegate: publicKey(walletPublicKey.toBase58()),
        merkleTree: publicKey(asset.compression.tree),
        root: publicKey(proof.root),
        dataHash: publicKey(asset.compression.data_hash),
        creatorHash: publicKey(asset.compression.creator_hash),
        nonce: asset.compression.leaf_id,
        index: asset.compression.leaf_id,
        proof: proof.proof.map((p: string) => publicKey(p)),
      };

      // 4. Create transfer instruction using Umi
      const transferInstruction = createTransferInstruction(umi, {
        ...assetWithProof,
        newLeafOwner: publicKey(walletPublicKey.toBase58()),
      });

      // 5. Use Umi Transaction Builder (Best Practice)
      const txBuilder = transactionBuilder()
        .add(transferInstruction);

      // 6. Build and sign using Umi
      const builtTx = await txBuilder.buildAndSign(umi);

      // Convert to web3.js Transaction for wallet adapter compatibility
      const web3Transaction = Transaction.from(builtTx.serialize());

      // 7. Send transaction
      const connection = new Connection(process.env.NEXT_PUBLIC_SOLANA_RPC!);
      const signature = await signTransaction(web3Transaction);
      const txid = await connection.sendRawTransaction(signature.serialize());
      await connection.confirmTransaction(txid, 'confirmed');

      return txid;
    } catch (err: any) {
      console.error(err);
      setError(err.message || 'Claim failed');
      throw err;
    } finally {
      setIsLoading(false);
    }
  };

  return { claimCompressedNFT, isLoading, error };
}





    
    
 


