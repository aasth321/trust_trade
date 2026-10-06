# trust_trade Architecture

## High-Level Architecture

```text
                                      USER
                   |
                   v
          +----------------+
          |   Frontend     |
          | Next.js/React  |
          +-------+--------+
                  |
                  v
          +----------------+
          | FastAPI        |
          | Backend        |
          +-------+--------+
                  |
       +----------+----------+
       |          |          |
       v          v          v
   Market AI  Trading AI  Risk Engine
       |          |          |
       +----------+----------+
                  |
                  v
          +----------------+
          | Trust Engine   |
          | Reputation     |
          | Risk Score     |
          | Credit Limit   |
          +-------+--------+
                  |
                  v
          +----------------+
          | Policy Engine  |
          +-------+--------+
                  |
          APPROVE / REJECT
                  |
                  v
          +----------------+
          | Web3.py        |
          | Smart Contracts|
          +-------+--------+
                  |
                  v
                MONAD
                  |
                  v
                 DEX
```
##Core Flow
def core_trade_flow(agent, trade):

    # 1. AI analyzes the market
    market_signal = market_analyst.analyze(trade.asset)

    # 2. AI creates trade proposal
    proposal = trading_agent.create_proposal(
        agent=agent,
        signal=market_signal
    )

    # 3. Risk evaluation
    risk_result = risk_engine.evaluate(
        agent=agent,
        trade=proposal
    )

    if risk_result["decision"] == "REJECT":
        return {
            "status": "REJECTED",
            "reason": risk_result["reason"]
        }

    # 4. Policy validation
    if not policy_engine.validate(agent, proposal):
        return {
            "status": "REJECTED",
            "reason": "Policy limit exceeded"
        }

    # 5. Execute approved trade
    transaction = trade_executor.execute(proposal)

    # 6. Update reputation
    reputation_engine.update(
        agent=agent,
        transaction=transaction
    )

    # 7. Update credit limit
    credit_manager.update(agent)

    return {
        "status": "APPROVED",
        "transaction": transaction
    }

## Core Components

### 1. FastAPI Backend
- Handles API requests
- Connects frontend, AI, database, and blockchain

### 2. AI Agents
- **Market Analyst** – analyzes market data
- **Trading Agent** – creates trade proposals
- **Risk Engine** – evaluates trade risk

### 3. Trust Engine
- Agent identity
- Trust/reputation score
- Risk score
- Credit limit

### 4. Policy Engine
- Maximum trade limit
- Daily loss limit
- Exposure limit
- Allowed assets
- Minimum reputation

### 5. Database
- PostgreSQL
- Stores agents, trades, risk results, and reputation history

### 6. Blockchain Layer
- Python `Web3.py`
- Connects to Monad
- Executes approved trades
- Reads transaction events

### 7. Smart Contracts
- `AgentRegistry`
- `ReputationRegistry`
- `PolicyManager`
- `TradeExecutor`








