# TrustTrade Architecture

## 1. Project Overview

**TrustTrade** is an onchain trust, reputation, and credit layer for autonomous financial AI agents.

The core principle is:

> **AI agents don't get trusted. They earn trust.**

TrustTrade combines:

- AI-powered market analysis and trade proposals
- Verifiable AI-agent identity
- Onchain reputation
- Risk assessment
- Programmable trading policies
- Controlled onchain execution
- Reputation and credit-limit updates after trades

The AI proposes a trade, but smart-contract policy determines whether the trade can execute.

---

## 2. High-Level Architecture

```text
                              USER
                                |
                                v
                    +-----------------------+
                    |   TrustTrade Frontend |
                    | Next.js / React / TS  |
                    +-----------+-----------+
                                |
                                | HTTPS / JSON
                                v
                    +-----------------------+
                    |    FastAPI Backend    |
                    +-----------+-----------+
                                |
                +---------------+----------------+
                |               |                |
                v               v                v
        +---------------+ +------------+ +--------------+
        | Market        | | Trading    | | Risk Agent   |
        | Analyst Agent | | Agent      | | / Risk Engine|
        +-------+-------+ +-----+------+ +------+-------+
                |               |               |
                +---------------+---------------+
                                |
                                v
                    +-----------------------+
                    |     Trust Engine      |
                    +-----------------------+
                    | Identity             |
                    | Reputation           |
                    | Risk Score            |
                    | Credit Limit          |
                    +-----------+-----------+
                                |
                                v
                    +-----------------------+
                    |    Policy Engine      |
                    +-----------------------+
                    | Max trade             |
                    | Daily loss limit      |
                    | Exposure limit        |
                    | Allowed assets        |
                    | Reputation threshold  |
                    +-----------+-----------+
                                |
                                | Approved request
                                v
                    +-----------------------+
                    |  Smart Contracts      |
                    +-----------------------+
                    | AgentRegistry         |
                    | ReputationRegistry    |
                    | PolicyManager         |
                    | TradeExecutor         |
                    +-----------+-----------+
                                |
                                v
                             MONAD
                                |
                 +--------------+--------------+
                 |                             |
                 v                             v
          +-------------+               +-------------+
          | DEX / AMM   |               | Onchain     |
          | Execution   |               | Events      |
          +-------------+               +-------------+
```

---

## 3. Core Design Principle

TrustTrade deliberately separates **AI intelligence** from **financial authority**.

```text
AI
 |
 | proposes
 v
Trade Proposal
 |
 v
Risk Engine
 |
 v
Policy Manager
 |
 +---- REJECT ----> Stop
 |
 +---- APPROVE ---> Trade Executor
                         |
                         v
                       DEX
                         |
                         v
                       Monad
```

The AI never receives unrestricted control of the treasury.

**AI proposes. The protocol decides.**

---

## 4. Main Components

### 4.1 Frontend

Recommended stack:

- Next.js
- React
- TypeScript
- Tailwind CSS
- wagmi
- viem

Main screens:

1. Dashboard
2. Create Agent
3. Agent Profile
4. Trust Score
5. Trade Proposal
6. Risk Assessment
7. Transaction Status
8. Reputation History
9. Agent Timeline
10. Trust Certificate

---

### 4.2 FastAPI Backend

The backend acts as the orchestration layer.

Responsibilities:

- Authentication/session handling
- AI-agent orchestration
- Market-data retrieval
- Trade proposal creation
- Risk evaluation
- Reputation calculation
- Database operations
- Blockchain interaction
- Event indexing
- API endpoints for frontend

Suggested API groups:

```text
/api/agents
/api/reputation
/api/risk
/api/trades
/api/policies
/api/transactions
```

---

## 5. AI Agent Architecture

TrustTrade uses specialized AI components instead of one unrestricted AI.

### Market Analyst Agent

Input:

- Price
- Volume
- Liquidity
- Volatility
- Market/onchain signals

Output:

```json
{
  "signal": "BUY",
  "confidence": 0.86,
  "risk": "MEDIUM",
  "reason": "Positive momentum with acceptable liquidity"
}
```

### Trading Agent

Converts market analysis into a structured proposal.

```json
{
  "asset": "MON",
  "action": "BUY",
  "amount": 850,
  "stopLoss": 0.03,
  "takeProfit": 0.06
}
```

### Risk Agent

Checks:

- Trade size
- Portfolio exposure
- Daily loss
- Drawdown
- Volatility
- Agent reputation
- Credit limit

Output:

```json
{
  "decision": "APPROVE",
  "riskScore": 23,
  "reason": "Trade is within all configured policy limits"
}
```

---

## 6. Trust Engine

The Trust Engine calculates the agent's current trust/reputation.

Suggested score:

```text
Trust Score =
    30% Identity
  + 30% Trading Performance
  + 20% Risk Management
  + 20% Reliability
```

Score range:

```text
0 - 100
```

Example:

```text
Identity            100
Trading Performance  96
Risk Management      91
Reliability          95
--------------------------------
Trust Score           95
```

The score should be explainable and reproducible.

---

## 7. Reputation Lifecycle

```text
Agent Created
     |
     v
Initial Reputation
     |
     v
Trade Executed
     |
     v
Trade Result
     |
     +---- Profit / Success ----> Reputation increases
     |
     +---- Loss / Violation ----> Reputation decreases
     |
     v
Credit Limit Recalculated
     |
     v
Future Financial Permissions
```

Example:

```text
New Agent
Reputation: 50
Credit: $500

        |
        | successful trades
        v

Reputation: 72
Credit: $2,000

        |
        | consistent low-risk performance
        v

Reputation: 91
Credit: $15,000
```

---

## 8. Smart Contract Architecture

TrustTrade uses four core contracts.

### 8.1 AgentRegistry.sol

Purpose:

- Register agents
- Associate agent wallet with owner
- Store metadata hash
- Mark verification status

Conceptual structure:

```solidity
struct Agent {
    address owner;
    address wallet;
    bytes32 metadataHash;
    uint256 createdAt;
    bool verified;
}
```

---

### 8.2 ReputationRegistry.sol

Purpose:

- Store reputation
- Record successful/failed trades
- Emit reputation update events

Conceptual data:

```text
score
successfulTrades
failedTrades
totalTrades
```

Only authorized components should update reputation.

---

### 8.3 PolicyManager.sol

Purpose:

- Enforce financial rules

Example policies:

```text
Maximum trade
Daily loss limit
Maximum portfolio exposure
Allowed assets
Minimum reputation
Credit limit
```

The policy manager returns:

```text
APPROVED
or
REJECTED
```

---

### 8.4 TradeExecutor.sol

Purpose:

- Execute approved trades
- Interact with supported DEX/AMM
- Emit trade events
- Prevent unauthorized execution

Execution flow:

```text
Trade Proposal
      |
      v
PolicyManager
      |
      v
APPROVED
      |
      v
TradeExecutor
      |
      v
DEX
      |
      v
Monad
```

---

## 9. Database Architecture

Recommended database:

**PostgreSQL**

The database stores application and analytics data that does not need to live directly onchain.

### agents

```text
id
name
wallet_address
owner_address
strategy
risk_profile
reputation_score
credit_limit
status
created_at
```

### trades

```text
id
agent_id
asset
side
amount
entry_price
exit_price
profit_loss
risk_score
tx_hash
status
created_at
```

### risk_assessments

```text
id
trade_id
risk_score
decision
reason
portfolio_exposure
daily_loss
created_at
```

### reputation_history

```text
id
agent_id
old_score
new_score
reason
trade_id
timestamp
```

---

## 10. Onchain vs Offchain Data

### Store onchain

- Agent identity registration
- Metadata hash
- Trade execution
- Policy events
- Reputation update events
- Capital movements
- Transaction hashes

### Store offchain

- Detailed AI outputs
- Market-data snapshots
- UI state
- Detailed analytics
- Risk-analysis explanations
- Historical dashboard data
- Non-critical application metadata

This keeps the system efficient while retaining verifiability.

---

## 11. Trade Execution Flow

```text
1. Market Analyst detects opportunity
             |
             v
2. Trading Agent creates proposal
             |
             v
3. Risk Agent evaluates proposal
             |
             v
4. Backend sends proposal to PolicyManager
             |
             v
5. PolicyManager checks:
       - identity
       - reputation
       - credit
       - trade size
       - daily loss
       - exposure
       - allowed asset
             |
       +------ REJECT ------> User receives reason
             |
             v
6. TradeExecutor executes approved trade
             |
             v
7. Monad confirms transaction
             |
             v
8. Backend indexes result
             |
             v
9. Reputation Engine calculates new score
             |
             v
10. ReputationRegistry records update
             |
             v
11. Credit limit is recalculated
```

---

## 12. Security Model

TrustTrade should follow these rules:

### Rule 1

The AI should not hold unrestricted treasury private keys.

### Rule 2

All trade execution must pass through policy validation.

### Rule 3

Reputation cannot be changed by the frontend.

### Rule 4

Only authorized backend/attestor roles can submit reputation updates.

### Rule 5

Every trade should have an onchain transaction reference.

### Rule 6

The system should enforce maximum trade and daily loss limits.

### Rule 7

Agent permissions should be revocable.

---

## 13. Trust Certificate

Each agent can have a public verification profile.

Example:

```text
TRUSTTRADE CERTIFICATE

Agent: AlphaX
Agent ID: 0x72...91A

Identity: VERIFIED
Strategy: Momentum
Risk Profile: Medium

Trust Score: 94 / 100

Successful Trades: 1,284
Win Rate: 81%
Max Drawdown: 4.2%

Credit Limit: $25,000

[VERIFY ONCHAIN]
```

The verification page should link to the relevant Monad transaction/contract.

---

## 14. Dashboard Flow

```text
Dashboard
   |
   +-- Create Agent
   |
   +-- View Agent
   |      |
   |      +-- Identity
   |      +-- Trust Score
   |      +-- Risk
   |      +-- Credit
   |      +-- Trading History
   |      +-- Reputation History
   |
   +-- Trade
   |      |
   |      +-- AI Proposal
   |      +-- Risk Assessment
   |      +-- Policy Check
   |      +-- Execute
   |
   +-- Activity
          |
          +-- AI decisions
          +-- Trades
          +-- Reputation updates
          +-- Transactions
```

---

## 15. Recommended Repository Structure

```text
trusttrade/
│
├── contracts/
│   ├── AgentRegistry.sol
│   ├── ReputationRegistry.sol
│   ├── PolicyManager.sol
│   ├── TradeExecutor.sol
│   └── interfaces/
│
├── backend/
│   ├── main.py
│   ├── agents/
│   │   ├── analyst.py
│   │   ├── trader.py
│   │   ├── risk.py
│   │   └── reputation.py
│   ├── services/
│   │   ├── market_data.py
│   │   ├── blockchain.py
│   │   ├── reputation.py
│   │   └── trading.py
│   ├── models/
│   │   ├── agent.py
│   │   ├── trade.py
│   │   └── reputation.py
│   └── database/
│       ├── db.py
│       └── schemas.py
│
├── frontend/
│   ├── app/
│   ├── components/
│   │   ├── AgentCard.tsx
│   │   ├── TrustScore.tsx
│   │   ├── TradePanel.tsx
│   │   ├── ReputationChart.tsx
│   │   └── AgentTimeline.tsx
│   └── lib/
│
├── scripts/
│   └── deploy.ts
│
├── tests/
│   ├── AgentRegistry.t.sol
│   ├── PolicyManager.t.sol
│   └── TradeExecutor.t.sol
│
├── requirements.txt
├── README.md
└── .env.example
```

---

## 16. Hackathon MVP

### Must Have

- Agent registration
- Agent identity
- Trust score
- Risk score
- Credit limit
- AI trade proposal
- Policy validation
- One onchain trade flow
- Reputation update
- Transaction display
- Dashboard

### Nice to Have

- Multiple trading strategies
- Multiple DEXs
- Advanced charts
- Agent marketplace
- Agent-to-agent transactions
- Automated credit scoring
- Cross-chain identity

---

## 17. Key Demo

The strongest demo should show:

```text
CREATE AGENT
      ↓
IDENTITY VERIFIED
      ↓
TRUST SCORE = 72
      ↓
CREDIT = $2,000
      ↓
AI PROPOSES $850 TRADE
      ↓
RISK CHECK
      ↓
POLICY CHECK
      ↓
APPROVED
      ↓
MONAD TRANSACTION
      ↓
TRADE PROFIT = +$42
      ↓
TRUST SCORE 72 → 74
      ↓
CREDIT $2,000 → $2,500
```

Then attempt:

```text
AI PROPOSES $15,000 TRADE
      ↓
POLICY CHECK
      ↓
❌ REJECTED
      ↓
Reason:
Credit limit = $2,500
Requested = $15,000
```

This demonstrates both the **finance** and **trust/AI infrastructure** tracks.

---

## 18. Core Product Statement

> **TrustTrade is the financial identity, reputation and programmable credit layer for autonomous AI agents. AI agents can propose financial actions, but onchain policies determine what they are actually allowed to do. Agents earn greater access to capital through verifiable, responsible behavior.**
