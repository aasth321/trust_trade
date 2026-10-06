# TrustTrade Architecture

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
---
## core flow
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


                           |
                           v
                          DEX
