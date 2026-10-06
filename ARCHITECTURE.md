# trust_trade Architecture

## High-Level Architecture

```text
                         USER
                           |
                           v
                +----------------------+
                | Next.js / React      |
                | Frontend             |
                +----------+-----------+
                           |
                           v
                +----------------------+
                | FastAPI Backend      |
                +----------+-----------+
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
   +-------------+   +-------------+   +-------------+
   | Market      |   | Trading     |   | Risk Engine |
   | Analyst AI  |   | Agent AI    |   |             |
   +------+------+   +------+------+   +------+------+
          |                |                |
          +----------------+----------------+
                           |
                           v
                +----------------------+
                | Trust Engine         |
                | Identity             |
                | Reputation           |
                | Risk Score           |
                | Credit Limit         |
                +----------+-----------+
                           |
                           v
                +----------------------+
                | Policy Engine        |
                | Trade Limits         |
                | Loss Limits          |
                | Exposure Limits      |
                | Allowed Assets       |
                +----------+-----------+
                           |
                           v
                +----------------------+
                | Smart Contracts      |
                | AgentRegistry        |
                | ReputationRegistry   |
                | PolicyManager        |
                | TradeExecutor        |
                +----------+-----------+
                           |
                           v
                         MONAD
                           |
                           v
                          DEX
```
##Core Flow
```text
AI analyzes market
        ↓
AI proposes trade
        ↓
Risk Engine
        ↓
Policy Engine
        ↓
  ┌─────┴─────┐
REJECT      APPROVE
  |            |
  ↓            ↓
Stop      TradeExecutor
               ↓
              DEX
               ↓
             Monad
               ↓
       Update Reputation
               ↓
       Update Credit Limit
```
##Core Components
###Frontend
  Next.js
  React
  TypeScript
  Tailwind CSS
  wagmi / viem
###Backend
   FastAPI
   Python
  PostgreSQL
  SQLAlchemy
  Web3.py
###AI Layer
    Market Analyst
    Trading Agent
    Risk Engine
    Trust/Reputation Engine
###Smart Contracts
    AgentRegistry.sol
    ReputationRegistry.sol
    PolicyManager.sol
    TradeExecutor.sol
###Database
  agents
  trades
  risk_assessments
  reputation_history










