[github_issues_atap.md](https://github.com/user-attachments/files/27973361/github_issues_atap.md)
# ATAP GitHub Issues \- Complete Roadmap

**Project**: AI-Assisted Automated Trading Platform (ATAP)  
**Timeline**: 8 Weeks (MVP \+ Phase 2\)  
**Total Issues**: 35  
**Total Story Points**: 127

---

## 📊 Overview by Phase

| Phase | Sprint | Weeks | Issues | Story Points | Focus |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **MVP** | Sprint 1 | 1-2 | 8 | 28 | Foundation & Auth |
| **MVP** | Sprint 2 | 2-3 | 10 | 32 | Core Features |
| **MVP** | Sprint 3 | 3-4 | 7 | 22 | Integration & Testing |
| **Phase 2** | Sprint 4 | 5-6 | 6 | 24 | Advanced Features |
| **Phase 2** | Sprint 5 | 7-8 | 4 | 21 | Marketplace & Polish |

---

# 🔧 SPRINT 1: Foundation (Weeks 1-2)

**Goal**: Establish project infrastructure, authentication, and basic user management  
**Story Points**: 28  
**Dependencies**: None (start here)

---

## ATAP-001: Project Setup & Infrastructure

**Type**: Task  
**Priority**: P0 \- Critical  
**Story Points**: 3  
**Assignee**: Architect/You  
**Status**: Ready

### Description

Set up project scaffolding, Docker environment, and deployment infrastructure.

### Acceptance Criteria

- [ ] GitHub repo created with proper .gitignore  
- [ ] Docker Compose file for local development (PostgreSQL, Redis)  
- [ ] FastAPI project structure with app/api/models/schemas directories  
- [ ] Next.js project initialized with TypeScript  
- [ ] Environment variables configured (.env.example)  
- [ ] Database migrations setup (Alembic)  
- [ ] GitHub Actions CI/CD pipeline stubbed out  
- [ ] README with setup instructions

### Technical Notes

- Use Docker Compose for local dev: Python 3.11, PostgreSQL 15, Redis 7  
- FastAPI with Uvicorn  
- Next.js 14+ with TypeScript \+ Tailwind CSS  
- Database: PostgreSQL with SQLAlchemy ORM

### Deliverables

- GitHub repo with all scaffolding  
- Developers can `docker-compose up` and have working dev environment

---

## ATAP-002: Database Schema & Models

**Type**: Task  
**Priority**: P0 \- Critical  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-001

### Description

Design and implement complete PostgreSQL schema and SQLAlchemy models.

### Acceptance Criteria

- [ ] 11 core tables created (users, strategies, trades, positions, etc.)  
- [ ] All indexes created for query performance  
- [ ] Foreign keys and constraints properly set  
- [ ] Soft delete support (is\_deleted column)  
- [ ] Timestamps (created\_at, updated\_at) on all tables  
- [ ] SQLAlchemy models match schema exactly  
- [ ] Database migrations working (Alembic)  
- [ ] Sample data seed script for testing

### Database Tables

\- users (id, email, password\_hash, 2fa\_secret, tier, created\_at)

\- strategies (id, user\_id, name, type, parameters, status, created\_at)

\- backtests (id, user\_id, strategy\_id, date\_range, metrics, status)

\- trades (id, user\_id, symbol, entry\_price, exit\_price, pnl, created\_at)

\- positions (id, user\_id, symbol, qty, entry\_price, sl, tp, current\_price)

\- broker\_accounts (id, user\_id, broker\_name, credentials, is\_connected)

\- market\_data (id, symbol, date, open, high, low, close, volume)

\- notifications (id, user\_id, message, type, is\_read)

\- subscriptions (id, user\_id, tier, renewal\_date, amount)

\- marketplace\_strategies (id, strategy\_id, description, rating, followers)

\- audit\_log (id, user\_id, action, details, timestamp)

### Technical Notes

- Use TimescaleDB extension for time-series data (market data, positions history)  
- Partition market\_data table by symbol for performance  
- Create indexes on: user\_id, symbol, created\_at

### Deliverables

- Complete schema.sql  
- SQLAlchemy models  
- Alembic migrations  
- Seed data script

---

## ATAP-003: User Authentication \- Registration & Email Verification

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-002

### Description

Implement user registration with email verification flow.

### Acceptance Criteria

- [ ] POST /api/auth/register endpoint accepts email, password  
- [ ] Password validation: min 8 chars, 1 uppercase, 1 number  
- [ ] Email uniqueness check (return 400 if exists)  
- [ ] Password hashed with bcrypt (not stored plaintext)  
- [ ] Verification email sent via SendGrid within 1 second  
- [ ] Email contains verification link (token expires in 24h)  
- [ ] GET /api/auth/verify?token=XXX endpoint confirms email  
- [ ] User created in free tier after email confirmed  
- [ ] Welcome email sent after verification  
- [ ] Proper error messages (email exists, invalid password, etc.)  
- [ ] Unit tests for all scenarios

### API Endpoint

POST /api/auth/register

{

  "email": "trader@example.com",

  "password": "SecurePass123"

}

Response (201):

{

  "message": "Verification email sent. Check your inbox."

}

GET /api/auth/verify?token=abc123xyz

Response (200):

{

  "message": "Email verified. Account created.",

  "user\_id": "user\_123"

}

### Technical Notes

- Use `python-jose` for JWT token generation  
- SendGrid integration for email  
- Token stored in Redis (expires 24h)  
- Password hashing: bcrypt with salt rounds \= 12

### Deliverables

- Registration API endpoint  
- Email verification flow  
- Unit tests (registration, validation, email sending)  
- Integration test with SendGrid mock

---

## ATAP-004: User Authentication \- Login & JWT Tokens

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-003

### Description

Implement login with JWT access tokens and refresh tokens.

### Acceptance Criteria

- [ ] POST /api/auth/login accepts email, password  
- [ ] Password verified against bcrypt hash  
- [ ] JWT access token generated (15-minute expiry)  
- [ ] Refresh token generated (7-day expiry, stored in httpOnly cookie)  
- [ ] "Remember me" checkbox extends refresh token to 30 days  
- [ ] POST /api/auth/refresh endpoint renews access token  
- [ ] Invalid credentials return 401 (no account enumeration)  
- [ ] Account locked after 5 failed attempts (15-min timeout)  
- [ ] Logout endpoint invalidates refresh token  
- [ ] Unit tests for all scenarios

### API Endpoints

POST /api/auth/login

{

  "email": "trader@example.com",

  "password": "SecurePass123",

  "remember\_me": true

}

Response (200):

{

  "access\_token": "eyJ0eXAiOiJKV1QiLC...",

  "token\_type": "bearer",

  "expires\_in": 900

}

(Refresh token in httpOnly cookie)

POST /api/auth/refresh

Response (200):

{

  "access\_token": "eyJ0eXAiOiJKV1QiLC...",

  "expires\_in": 900

}

POST /api/auth/logout

Response (200):

{

  "message": "Logged out"

}

### Technical Notes

- Access token: 15 min expiry  
- Refresh token: 7 days (or 30 with remember\_me)  
- Failed login attempts tracked per IP  
- Rate limit: 5 attempts per 15 minutes per IP  
- Use `python-jose` \+ `passlib` for JWT/password

### Deliverables

- Login/logout endpoints  
- JWT token management  
- Rate limiting middleware  
- Unit tests \+ integration tests

---

## ATAP-005: Two-Factor Authentication (2FA) Setup

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-004

### Description

Implement 2FA using TOTP (Time-based One-Time Password) for account security.

### Acceptance Criteria

- [ ] Settings page has "Enable 2FA" button  
- [ ] Clicking button generates TOTP secret  
- [ ] QR code displayed for scanning (Google Authenticator, Authy, etc.)  
- [ ] User enters 6-digit code from authenticator  
- [ ] Code validated (must be exact)  
- [ ] 2FA enabled, 10 backup codes generated and displayed  
- [ ] On next login, prompt for 2FA code  
- [ ] 2FA code invalid 3x → account locked 15 mins  
- [ ] Can disable 2FA with password confirmation  
- [ ] Backup codes work if authenticator unavailable  
- [ ] Unit tests for TOTP generation/validation

### API Endpoints

POST /api/user/2fa/enable

Response (200):

{

  "secret": "JBSWY3DPEBLW64TMMQ======",

  "qr\_code": "data:image/png;base64,iVBORw0KGgo...",

  "backup\_codes": \["123456", "234567", "345678", ...\]

}

POST /api/user/2fa/verify

{

  "code": "123456"

}

Response (200):

{

  "message": "2FA enabled"

}

POST /api/user/2fa/disable

{

  "password": "MyPassword123"

}

Response (200):

{

  "message": "2FA disabled"

}

### Technical Notes

- Use `pyotp` library for TOTP generation  
- QR code generation: `qrcode` library  
- Backup codes: 10 random 6-digit codes  
- Store secret encrypted in database  
- Time tolerance: ±30 seconds for clock skew

### Deliverables

- 2FA setup endpoints  
- TOTP validation logic  
- Backup code generation/validation  
- Unit tests

---

## ATAP-006: User Profile Management

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-003

### Description

Implement user profile endpoints for viewing and updating account information.

### Acceptance Criteria

- [ ] GET /api/user/profile returns user data (name, email, tier, 2fa\_enabled)  
- [ ] PATCH /api/user/profile allows updating name, email  
- [ ] Email update requires re-verification (new verification email sent)  
- [ ] Password change endpoint (old password required)  
- [ ] Password reset via email (token expires in 1h)  
- [ ] Delete account endpoint (requires password, soft delete)  
- [ ] All endpoints require JWT authentication  
- [ ] Unit tests for all scenarios

### API Endpoints

GET /api/user/profile

Response (200):

{

  "user\_id": "user\_123",

  "name": "Raj Kumar",

  "email": "raj@example.com",

  "tier": "pro",

  "2fa\_enabled": true,

  "created\_at": "2026-05-18T10:00:00Z"

}

PATCH /api/user/profile

{

  "name": "Raj Kumar",

  "email": "newraj@example.com"

}

Response (200):

{

  "message": "Profile updated. Verify new email."

}

POST /api/user/password-change

{

  "current\_password": "OldPass123",

  "new\_password": "NewPass456"

}

Response (200):

{

  "message": "Password changed"

}

POST /api/auth/forgot-password

{

  "email": "raj@example.com"

}

Response (200):

{

  "message": "Reset link sent to email"

}

POST /api/auth/reset-password?token=XXX

{

  "new\_password": "NewPass456"

}

Response (200):

{

  "message": "Password reset"

}

### Deliverables

- Profile endpoints  
- Password change/reset flow  
- Email verification for changes  
- Unit tests

---

## ATAP-007: User Subscription Tier Management

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-006

### Description

Implement subscription tier system (Free, Pro, Enterprise) with tier-based feature access.

### Acceptance Criteria

- [ ] User created in free tier by default  
- [ ] Tier system: free (₹0), pro (₹99), enterprise (₹999)  
- [ ] Feature limits per tier:  
      - Free: 1 strategy, 1 backtest/day, paper trading only  
      - Pro: 10 strategies, unlimited backtests, live trading  
      - Enterprise: unlimited, API access, white-label  
- [ ] Middleware checks tier before allowing actions  
- [ ] GET /api/user/tier returns current tier and limits  
- [ ] Upgrade endpoint (integration with payment gateway, later)  
- [ ] Downgrade with warning about feature loss  
- [ ] Unit tests for tier-based access control

### Technical Notes

- Define tier constants: FREE=0, PRO=1, ENTERPRISE=2  
- Create middleware for tier checking  
- Feature flags per tier  
- Log feature attempts denied due to tier

### Deliverables

- Tier system implementation  
- Feature access control middleware  
- Tier info endpoint  
- Unit tests

---

## ATAP-008: Error Handling & Logging Infrastructure

**Type**: Task  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-001

### Description

Implement standardized error handling, logging, and monitoring across API.

### Acceptance Criteria

- [ ] Global exception handler for all API errors  
- [ ] Standardized error response format (code, message, details)  
- [ ] Logging configured (file \+ console \+ Sentry)  
- [ ] Sensitive data never logged (passwords, API keys)  
- [ ] Request/response logging for debugging  
- [ ] Error tracking: Sentry integration  
- [ ] Performance monitoring: response times logged  
- [ ] Health check endpoint: GET /api/health  
- [ ] API documentation auto-generated (OpenAPI/Swagger)  
- [ ] Unit tests for error scenarios

### Error Response Format

{

  "error": {

    "code": "AUTH\_INVALID\_CREDENTIALS",

    "message": "Email or password incorrect",

    "details": {},

    "timestamp": "2026-05-18T10:00:00Z",

    "request\_id": "req\_123abc"

  }

}

### Technical Notes

- Use `logging` module \+ `python-json-logger`  
- Sentry integration for error tracking  
- FastAPI exception handlers for all error types  
- OpenAPI schema auto-generated by FastAPI  
- Health check includes DB, Redis status

### Deliverables

- Global error handler  
- Logging configuration  
- Sentry integration  
- Health check endpoint  
- Unit tests

---

# 🎯 SPRINT 2: Core Features (Weeks 2-3)

**Goal**: Implement strategy management, backtesting engine, and basic trading logic  
**Story Points**: 32  
**Dependencies**: Sprint 1 complete

---

## ATAP-009: Broker Account Connection \- Angel One

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 8  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-002, ATAP-004

### Description

Implement secure connection to Angel One broker using API credentials.

### Acceptance Criteria

- [ ] Modal shows Angel One setup instructions  
- [ ] User enters: API Key, Client ID, TOTP secret  
- [ ] Credentials validated with Angel One API (test call)  
- [ ] If valid: Save encrypted credentials in database  
- [ ] If invalid: Return helpful error message  
- [ ] Account info displayed: portfolio value, cash, margin  
- [ ] Disconnect button securely deletes credentials  
- [ ] Session token management (refresh when expired)  
- [ ] Multiple broker support (same user can connect 2+ brokers)  
- [ ] Audit log entry for connection/disconnection  
- [ ] Unit tests \+ integration tests with mock Angel One API

### API Endpoints

POST /api/brokers/angel-one/connect

{

  "api\_key": "abc123",

  "client\_id": "client\_123",

  "totp\_secret": "JBSWY3DPEBLW64TMMQ"

}

Response (201):

{

  "broker\_account\_id": "ba\_123",

  "broker": "angel\_one",

  "portfolio\_value": 500000,

  "available\_cash": 250000,

  "margin\_used": 150000

}

GET /api/brokers/angel-one/account

Response (200):

{

  "portfolio\_value": 500000,

  "available\_cash": 250000,

  "margin\_used": 150000,

  "connected\_at": "2026-05-18T10:00:00Z"

}

DELETE /api/brokers/angel-one

Response (200):

{

  "message": "Broker disconnected"

}

### Technical Notes

- Angel One Smart API authentication  
- Use `cryptography` library to encrypt credentials (AES-256)  
- Credentials never logged or exposed  
- Create abstract broker adapter class (reuse for Zerodha)  
- Mock Angel One API for testing (avoid real API calls in tests)

### Deliverables

- Angel One broker adapter  
- Connect/disconnect flow  
- Account info endpoints  
- Unit \+ integration tests with mocks  
- Error handling for auth failures

---

## ATAP-010: Broker Account Connection \- Zerodha

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-009

### Description

Implement secure connection to Zerodha Kite API.

### Acceptance Criteria

- [ ] Modal shows Zerodha setup instructions  
- [ ] User enters: API key, access token  
- [ ] Credentials validated with Zerodha API  
- [ ] Account info displayed (portfolio, cash, margin)  
- [ ] Disconnect works same as Angel One  
- [ ] User can have both Angel One \+ Zerodha connected  
- [ ] When deploying strategy, select which broker to use  
- [ ] Unit tests with Zerodha API mocks

### API Endpoints

POST /api/brokers/zerodha/connect

{

  "api\_key": "api\_key\_abc123",

  "access\_token": "token\_xyz789"

}

Response (201):

{

  "broker\_account\_id": "ba\_456",

  "broker": "zerodha",

  "portfolio\_value": 500000,

  "available\_cash": 250000,

  "margin\_used": 150000

}

GET /api/brokers

Response (200):

{

  "brokers": \[

    {

      "broker": "angel\_one",

      "portfolio\_value": 500000,

      "connected\_at": "2026-05-18T10:00:00Z"

    },

    {

      "broker": "zerodha",

      "portfolio\_value": 600000,

      "connected\_at": "2026-05-18T11:00:00Z"

    }

  \]

}

### Technical Notes

- Reuse broker adapter pattern from ATAP-009  
- Zerodha Kite API documentation  
- Handle rate limiting (Zerodha has strict limits)  
- Mock testing for all scenarios

### Deliverables

- Zerodha broker adapter  
- Connect/disconnect endpoints  
- Multi-broker account listing  
- Unit tests

---

## ATAP-011: Strategy Creation \- EMA Crossover

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-007

### Description

Implement EMA Crossover strategy creation with no-code UI.

### Acceptance Criteria

- [ ] POST /api/strategies endpoint accepts: name, fast\_ema, slow\_ema, symbols  
- [ ] Strategy validation: name unique per user, EMA periods \> 0  
- [ ] Strategy stored as draft (status \= "draft")  
- [ ] User can edit strategy before deploying  
- [ ] User can clone strategy for variation testing  
- [ ] Strategy summary shows entry/exit rules  
- [ ] Tier checking: Free users max 1 strategy, Pro users max 10  
- [ ] Get strategy details endpoint  
- [ ] Delete strategy (only if no active trades)  
- [ ] Unit tests

### API Endpoints

POST /api/strategies

{

  "name": "My EMA 5-20",

  "strategy\_type": "ema\_crossover",

  "parameters": {

    "fast\_ema": 5,

    "slow\_ema": 20

  },

  "symbols": \["INFY", "TCS"\]

}

Response (201):

{

  "strategy\_id": "strat\_123",

  "name": "My EMA 5-20",

  "status": "draft",

  "summary": "Buy when EMA5 \> EMA20, Sell when EMA5 \< EMA20",

  "created\_at": "2026-05-18T10:00:00Z"

}

GET /api/strategies/{strategy\_id}

Response (200):

{

  "strategy\_id": "strat\_123",

  "name": "My EMA 5-20",

  "status": "draft",

  "parameters": {

    "fast\_ema": 5,

    "slow\_ema": 20

  },

  "symbols": \["INFY", "TCS"\],

  "created\_at": "2026-05-18T10:00:00Z"

}

PATCH /api/strategies/{strategy\_id}

{

  "fast\_ema": 7,

  "slow\_ema": 21

}

Response (200):

{

  "message": "Strategy updated"

}

POST /api/strategies/{strategy\_id}/clone

Response (201):

{

  "strategy\_id": "strat\_124",

  "name": "My EMA 5-20 (copy)"

}

DELETE /api/strategies/{strategy\_id}

Response (200):

{

  "message": "Strategy deleted"

}

### Technical Notes

- EMA calculation formula  
- Validate parameters before storing  
- Strategy type enum: EMA\_CROSSOVER, RSI, BREAKOUT  
- Support for 1-5 symbols per strategy (MVP limit)

### Deliverables

- Strategy CRUD endpoints  
- EMA strategy validation  
- Tier-based limits middleware  
- Unit tests

---

## ATAP-012: Strategy Creation \- RSI Momentum

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-011

### Description

Implement RSI momentum strategy creation.

### Acceptance Criteria

- [ ] Support RSI strategy type in create/edit  
- [ ] Parameters: rsi\_period (default 14), oversold (default 30), overbought (default 70\)  
- [ ] Validation: period \> 0, 0 \< oversold \< overbought \< 100  
- [ ] Entry rule: RSI \< oversold  
- [ ] Exit rule: RSI \> overbought  
- [ ] Same CRUD operations as EMA strategy  
- [ ] Unit tests

### API Endpoints

POST /api/strategies

{

  "name": "RSI Oversold Entry",

  "strategy\_type": "rsi",

  "parameters": {

    "rsi\_period": 14,

    "oversold": 30,

    "overbought": 70

  },

  "symbols": \["RELIANCE"\]

}

Response (201):

{

  "strategy\_id": "strat\_125",

  "summary": "Buy when RSI \< 30, Sell when RSI \> 70"

}

### Deliverables

- RSI strategy support  
- Parameter validation  
- Unit tests

---

## ATAP-013: Strategy Creation \- Breakout

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-011

### Description

Implement breakout strategy creation.

### Acceptance Criteria

- [ ] Support Breakout strategy type  
- [ ] Parameters: lookback\_period (default 20\)  
- [ ] Entry rule: Buy on 20-day high breakout  
- [ ] Exit rule: Sell on 20-day low breakdown  
- [ ] Validation: period \> 0  
- [ ] Same CRUD operations as EMA  
- [ ] Unit tests

### API Endpoints

POST /api/strategies

{

  "name": "20-Day Breakout",

  "strategy\_type": "breakout",

  "parameters": {

    "lookback\_period": 20

  },

  "symbols": \["INFY", "WIPRO"\]

}

Response (201):

{

  "strategy\_id": "strat\_126",

  "summary": "Buy on 20-day high, Sell on 20-day low"

}

### Deliverables

- Breakout strategy support  
- Parameter validation  
- Unit tests

---

## ATAP-014: Backtest Engine \- Core Simulation

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 8  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-011, ATAP-012, ATAP-013

### Description

Implement backtesting engine that simulates strategy execution on historical data.

### Acceptance Criteria

- [ ] POST /api/backtests endpoint accepts: strategy\_id, date\_range, slippage  
- [ ] Load historical OHLC data for backtest period  
- [ ] Simulate strategy on daily basis (one trade per day max initially)  
- [ ] Track all trades: entry\_price, exit\_price, entry\_date, exit\_date, P\&L  
- [ ] Calculate metrics: win\_rate, profit\_factor, avg\_trade\_duration  
- [ ] Generate equity curve (daily portfolio value over time)  
- [ ] Async job processing (Celery) \- backtest runs in background  
- [ ] Status tracking: pending → running → completed/failed  
- [ ] Email notification when backtest complete  
- [ ] Store results in database for later viewing  
- [ ] Handle errors gracefully (bad data, strategy issues)  
- [ ] Unit tests with mock market data

### API Endpoints

POST /api/backtests

{

  "strategy\_id": "strat\_123",

  "date\_range": {

    "start\_date": "2025-01-01",

    "end\_date": "2026-05-18"

  },

  "slippage": 0.005

}

Response (202):

{

  "backtest\_id": "bt\_123",

  "status": "running",

  "created\_at": "2026-05-18T10:00:00Z"

}

GET /api/backtests/{backtest\_id}

Response (200):

{

  "backtest\_id": "bt\_123",

  "status": "completed",

  "metrics": {

    "win\_rate": 0.52,

    "profit\_factor": 1.8,

    "sharpe\_ratio": 1.2,

    "max\_drawdown": 0.08,

    "avg\_trade\_duration": 2.5,

    "total\_return": 0.25

  },

  "trades": \[

    {

      "entry\_date": "2025-01-02",

      "entry\_price": 1500,

      "exit\_date": "2025-01-03",

      "exit\_price": 1520,

      "pnl": 20,

      "pnl\_percent": 0.0133

    }

  \],

  "equity\_curve": \[

    {"date": "2025-01-01", "value": 100000},

    {"date": "2025-01-02", "value": 100020},

    ...

  \]

}

### Technical Notes

- Celery for async job processing  
- Backtest logic: iterate through dates, evaluate strategy signals  
- Metrics calculation:  
  - Win rate: (winning trades / total trades)  
  - Profit factor: (total profit / total loss)  
  - Sharpe ratio: (mean return \- risk\_free\_rate) / std\_dev  
  - Max drawdown: (peak \- trough) / peak  
- Slippage: subtract % from entry price, add % to exit price  
- Error handling: if strategy.evaluate() throws error, mark backtest as failed

### Deliverables

- Backtest async job handler  
- Metrics calculation logic  
- Trade simulation engine  
- Equity curve generation  
- Unit tests with mock data  
- Error handling & logging

---

## ATAP-015: Backtest Engine \- Advanced Metrics

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-014

### Description

Implement advanced backtesting metrics (Sharpe ratio, Sortino ratio, Calmar ratio).

### Acceptance Criteria

- [ ] Calculate Sharpe ratio (risk-adjusted return)  
- [ ] Calculate Sortino ratio (downside-adjusted return)  
- [ ] Calculate Calmar ratio (return / max drawdown)  
- [ ] Calculate largest win/loss trades  
- [ ] Calculate recovery factor (total return / max drawdown)  
- [ ] Risk-free rate: use 6% annual (India sovereign bond yield)  
- [ ] All metrics in backtest results  
- [ ] Unit tests for metric calculations

### Technical Notes

- Sharpe \= (mean\_return \- risk\_free\_rate) / std\_dev  
- Sortino \= (mean\_return \- risk\_free\_rate) / downside\_std\_dev (only negative returns)  
- Calmar \= annual\_return / max\_drawdown  
- Validate calculations against manual examples

### Deliverables

- Advanced metrics calculation  
- Unit tests for each metric  
- Integration with backtest results

---

## ATAP-016: Backtest Results Visualization

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 5  
**Assignee**: Frontend Lead  
**Status**: Ready  
**Depends on**: ATAP-014, ATAP-015

### Description

Implement frontend UI for backtest results display.

### Acceptance Criteria

- [ ] Results page shows all metrics in card layout  
- [ ] Equity curve chart (line graph, interactive)  
- [ ] Trades list table (sortable, filterable)  
- [ ] Metrics cards: win rate %, profit factor, Sharpe, drawdown  
- [ ] Trade summary: \# trades, avg duration, largest win/loss  
- [ ] Color coding: green for profit, red for loss  
- [ ] Export button (PDF/CSV)  
- [ ] Responsive design (mobile-friendly)  
- [ ] Load backtest data via API  
- [ ] Skeleton loaders while data loads  
- [ ] Error state if backtest failed

### UI Components Needed

- MetricsCard (displays single metric)  
- EquityCurveChart (Recharts line chart)  
- TradesTable (sortable, paginated)  
- ExportButton (PDF/CSV download)  
- BacktestResults layout

### Deliverables

- Results page component  
- Chart components (Recharts)  
- Export functionality  
- Responsive design  
- Unit/integration tests

---

## ATAP-017: Signal Generator \- Calculate Indicators

**Type**: Task  
**Priority**: P1 \- High  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-002

### Description

Implement technical indicator calculations (EMA, RSI, 52-week high/low).

### Acceptance Criteria

- [ ] EMA calculation with proper smoothing  
- [ ] RSI calculation (14-period default)  
- [ ] 52-week high/low tracking  
- [ ] All indicators support custom periods  
- [ ] Validation: period \>= 1  
- [ ] Tests with known data (validate against external sources)  
- [ ] Performance: calculate for 500+ stocks in \< 2 seconds  
- [ ] Handle missing data gracefully (NaN handling)

### Technical Notes

- EMA formula: EMA \= (Close \- EMA\_prev) × 2/(N+1) \+ EMA\_prev  
- RSI \= 100 \- (100 / (1 \+ RS)) where RS \= avg\_gain / avg\_loss  
- Use NumPy/Pandas for performance  
- Validate against TA-Lib or similar library

### Deliverables

- Indicator calculation module  
- Tests with known data  
- Performance benchmarks  
- Documentation

---

# 🔗 SPRINT 3: Integration & Testing (Weeks 3-4)

**Goal**: Connect all components, implement live trading, and comprehensive testing  
**Story Points**: 22  
**Dependencies**: Sprint 1 \+ Sprint 2 complete

---

## ATAP-018: Order Execution Service

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 8  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-009, ATAP-010

### Description

Implement automatic order execution when trading signals are generated.

### Acceptance Criteria

- [ ] Signal triggers order placement (market order)  
- [ ] Order includes: symbol, qty, SL price, TP price  
- [ ] Qty calculated from position size (in INR) and current price  
- [ ] Orders sent to broker API within 1 second of signal  
- [ ] Broker order ID stored in database  
- [ ] Order status tracking: pending → executed → filled  
- [ ] Retries on broker API failure (exponential backoff, max 3 attempts)  
- [ ] If all retries fail: alert user, log error  
- [ ] Atomic transaction: entry order \+ SL/TP orders  
- [ ] Test with mocked broker API

### Flow

Signal Generated (EMA5 \> EMA20)

    ↓

Check: Account has available cash?

    ↓

Calculate qty from position size

    ↓

Send Market Order \+ SL/TP to Broker

    ↓

Get Broker Order ID

    ↓

Store in database

    ↓

User notified (order executed)

### Technical Notes

- Position size validation: size \<= available cash  
- Use broker.place\_order() adapter method  
- SL/TP as separate orders (or BO for broker that supports it)  
- Idempotency key to prevent duplicate orders  
- Timeout: 5 seconds for broker response, then retry

### Deliverables

- Order execution service  
- Broker order adapter methods  
- Retry logic with exponential backoff  
- Unit tests with mocked broker

---

## ATAP-019: Position Tracking & Real-Time Dashboard

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 8  
**Assignee**: Backend Lead \+ Frontend Lead  
**Status**: Ready  
**Depends on**: ATAP-018

### Description

Implement position tracking and real-time dashboard display.

### Acceptance Criteria

- [ ] GET /api/positions returns all open positions  
- [ ] Each position shows: symbol, qty, entry\_price, current\_price, P\&L, SL, TP  
- [ ] Current price updated from broker API every 5 seconds  
- [ ] P\&L calculated in real-time (current\_price \- entry\_price) \* qty  
- [ ] Dashboard layout: summary \+ positions table \+ recent trades  
- [ ] WebSocket endpoint for real-time updates (positions, P\&L)  
- [ ] Summary shows: total portfolio value, today's P\&L, monthly P\&L  
- [ ] Recent trades: last 5 closed trades with P\&L  
- [ ] Color coding: green (profit), red (loss)  
- [ ] Responsive design  
- [ ] Error handling if broker API unavailable

### API Endpoints

GET /api/positions

Response (200):

{

  "positions": \[

    {

      "position\_id": "pos\_123",

      "symbol": "INFY",

      "qty": 10,

      "entry\_price": 1500,

      "current\_price": 1520,

      "pnl": 200,

      "pnl\_percent": 1.33,

      "sl\_price": 1450,

      "tp\_price": 1600,

      "entry\_time": "2026-05-18T10:05:00Z"

    }

  \],

  "summary": {

    "portfolio\_value": 500000,

    "today\_pnl": 2500,

    "today\_pnl\_percent": 0.5,

    "monthly\_pnl": 15000,

    "monthly\_pnl\_percent": 3.0

  }

}

WS /api/ws/positions

Receives every 5 seconds:

{

  "positions": \[...\],

  "summary": {...},

  "timestamp": "2026-05-18T10:10:00Z"

}

### Technical Notes

- Use Redis pub/sub for real-time updates  
- Market data: use broker API or external price source  
- WebSocket: fastapi-socketio or websockets library  
- Caching: cache market data for 5 seconds (avoid multiple API calls)  
- Error resilience: if price fetch fails, use last known price

### Deliverables

- Position tracking service  
- Real-time price updates  
- WebSocket endpoint  
- Dashboard frontend  
- Unit/integration tests

---

## ATAP-020: Risk Management \- Position Sizing & SL/TP

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-018

### Description

Implement risk management: position sizing and automatic stop-loss/take-profit.

### Acceptance Criteria

- [ ] Fixed position sizing: user sets position size in INR  
- [ ] Position size validation: must be \<= available cash  
- [ ] SL/TP calculation: user specifies SL % and TP %  
- [ ] SL price \= entry\_price \* (1 \- SL%)  
- [ ] TP price \= entry\_price \* (1 \+ TP%)  
- [ ] SL/TP required: can't place order without both  
- [ ] Risk-reward ratio shown: TP % / SL %  
- [ ] Daily loss limit: pause all strategies if daily loss \> limit %  
- [ ] Daily loss tracked in real-time  
- [ ] User can set/modify daily loss limit in settings  
- [ ] Strategies auto-resume next trading day  
- [ ] User can manually resume before end of day  
- [ ] Audit log: track when daily limit hit

### User Settings

POST /api/settings/risk

{

  "position\_size": 50000,

  "daily\_loss\_limit": 10000,

  "max\_open\_positions": 5

}

GET /api/settings/risk

Response (200):

{

  "position\_size": 50000,

  "daily\_loss\_limit": 10000,

  "max\_open\_positions": 5,

  "today\_loss": 2500

}

### Technical Notes

- Position sizing: size / current\_price \= qty  
- Daily loss: sum of all closed trades P\&L for the day  
- Daily limit reset: at market open (9:15 AM IST)  
- Store daily\_loss in Redis (fast lookup)  
- Audit log every time daily limit checked

### Deliverables

- Position sizing calculation  
- SL/TP enforcement  
- Daily loss limit logic  
- Settings endpoints  
- Unit tests

---

## ATAP-021: Live Strategy Deployment

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-011, ATAP-018, ATAP-020

### Description

Implement strategy deployment to live trading mode.

### Acceptance Criteria

- [ ] POST /api/strategies/{id}/deploy accepts: broker\_id, position\_size, sl%, tp%  
- [ ] Strategy status changes from "draft" to "live"  
- [ ] Signal generator starts monitoring strategy  
- [ ] When signal triggered: auto-place order (via ATAP-018)  
- [ ] User can pause strategy (stops new signals, keeps open trades)  
- [ ] User can resume paused strategy  
- [ ] User can close all positions manually  
- [ ] Deployment logged with timestamp, broker, position size  
- [ ] Error if position size \> available cash  
- [ ] Error if already deployed  
- [ ] Unit tests

### API Endpoints

POST /api/strategies/{strategy\_id}/deploy

{

  "broker\_account\_id": "ba\_123",

  "position\_size": 50000,

  "sl\_percent": 0.02,

  "tp\_percent": 0.03

}

Response (200):

{

  "message": "Strategy deployed to live trading",

  "deployment\_id": "dep\_123",

  "status": "live",

  "deployed\_at": "2026-05-18T10:05:00Z"

}

POST /api/strategies/{strategy\_id}/pause

Response (200):

{

  "message": "Strategy paused. Open trades continue."

}

POST /api/strategies/{strategy\_id}/resume

Response (200):

{

  "message": "Strategy resumed"

}

POST /api/strategies/{strategy\_id}/stop

Response (200):

{

  "message": "All positions closed",

  "closed\_positions": 3

}

### Technical Notes

- Only 1 deployment per strategy (pause/resume for control)  
- Signal monitoring runs in background (Celery task)  
- Store deployment config for later retrieval  
- Support multiple brokers (select at deployment)

### Deliverables

- Deploy/pause/resume endpoints  
- Signal monitoring background task  
- Deployment history tracking  
- Unit tests

---

## ATAP-022: Manual Position Management

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-019

### Description

Allow manual position closing and SL/TP modification.

### Acceptance Criteria

- [ ] POST /api/positions/{id}/close sends market order to close position  
- [ ] User sees final P\&L for closed position  
- [ ] PATCH /api/positions/{id}/modify-sl-tp updates SL and/or TP  
- [ ] Validation: SL \< entry \< TP  
- [ ] New SL/TP sent to broker immediately  
- [ ] Notifications sent when position closed/modified  
- [ ] Audit log entry for manual actions  
- [ ] Error handling if broker API fails  
- [ ] Unit tests

### API Endpoints

POST /api/positions/{position\_id}/close

Response (200):

{

  "message": "Position closed",

  "exit\_price": 1520,

  "pnl": 200,

  "closed\_at": "2026-05-18T14:30:00Z"

}

PATCH /api/positions/{position\_id}/modify-sl-tp

{

  "sl\_price": 1450,

  "tp\_price": 1550

}

Response (200):

{

  "message": "SL/TP updated"

}

### Deliverables

- Close position endpoint  
- Modify SL/TP endpoint  
- Unit tests

---

## ATAP-023: Trade History & Audit Logging

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-021

### Description

Implement comprehensive trade history and audit logging.

### Acceptance Criteria

- [ ] GET /api/trades returns all closed trades with filters  
- [ ] Trades show: symbol, entry\_price, entry\_date, exit\_price, exit\_date, P\&L, exit\_reason  
- [ ] Filters: date range, symbol, strategy, profit/loss only  
- [ ] Sorting: by date, P\&L, symbol  
- [ ] Export to CSV  
- [ ] Click trade to view full details (broker order ID, exact times)  
- [ ] Audit log tracks: all user actions (login, deploy, trade, settings)  
- [ ] Audit log immutable (no deletion)  
- [ ] Only user can see their own trades/audit log  
- [ ] Pagination: 50 trades per page  
- [ ] Unit tests

### API Endpoints

GET /api/trades?start\_date=2026-05-01\&end\_date=2026-05-18\&symbol=INFY\&sort=-pnl

Response (200):

{

  "trades": \[

    {

      "trade\_id": "t\_123",

      "symbol": "INFY",

      "entry\_price": 1500,

      "entry\_date": "2026-05-18T10:05:00Z",

      "exit\_price": 1520,

      "exit\_date": "2026-05-18T14:30:00Z",

      "pnl": 200,

      "pnl\_percent": 1.33,

      "exit\_reason": "TP",

      "strategy\_id": "strat\_123"

    }

  \],

  "total": 45,

  "page": 1,

  "per\_page": 50

}

GET /api/audit-log?user\_id=user\_123

Response (200):

{

  "logs": \[

    {

      "action": "strategy\_deployed",

      "details": {"strategy\_id": "strat\_123"},

      "timestamp": "2026-05-18T10:05:00Z"

    }

  \]

}

### Deliverables

- Trades list endpoint with filters/sorting  
- Trade detail endpoint  
- CSV export  
- Audit log endpoints  
- Unit tests

---

## ATAP-024: Email Notifications

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-018, ATAP-023

### Description

Send email notifications for critical trading events.

### Acceptance Criteria

- [ ] Email on trade execution (symbol, entry, P\&L target)  
- [ ] Email on SL/TP hit (exit price, P\&L)  
- [ ] Email on backtest completion (results link)  
- [ ] Email on daily loss limit breach (alert)  
- [ ] Email digest: daily summary of trades (optional, user preference)  
- [ ] Unsubscribe option in email footer  
- [ ] Email templates with branding  
- [ ] Sending via SendGrid  
- [ ] Batch send: collect emails, send once per minute  
- [ ] Error handling: retry on failure  
- [ ] Unit tests with SendGrid mocks

### Email Types

Trade Executed:

Subject: "INFY Bought @ ₹1500 | Target: ₹1545 | Risk: ₹1450"

Body: Summary \+ trade details \+ link to dashboard

SL/TP Hit:

Subject: "INFY Sold @ ₹1520 | Profit: ₹200 (+1.33%)"

Body: Trade summary \+ P\&L

Daily Loss Limit:

Subject: "Daily Loss Limit Exceeded | ₹12K lost"

Body: Alert \+ details \+ link to settings

Backtest Complete:

Subject: "Backtest Done: EMA 5-20 | Win Rate 52% | Sharpe 1.2"

Body: Results summary \+ link to results page

### Technical Notes

- Use Celery to queue emails (async)  
- SendGrid templates for consistency  
- Include unsubscribe token in footer  
- Rate limit: max 100 emails/user/day

### Deliverables

- Email notification service  
- Email template system  
- SendGrid integration  
- Unsubscribe handling  
- Unit tests

---

## ATAP-025: In-App Notifications

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 2  
**Assignee**: Backend Lead \+ Frontend Lead  
**Status**: Ready  
**Depends on**: ATAP-018, ATAP-023

### Description

Implement in-app notification center with notification history.

### Acceptance Criteria

- [ ] POST /api/notifications creates notification (internal)  
- [ ] GET /api/notifications returns all notifications for user  
- [ ] Notifications show in order (newest first)  
- [ ] Mark as read: PATCH /api/notifications/{id}/read  
- [ ] Delete notification: DELETE /api/notifications/{id}  
- [ ] Notification types: trade, alert, backtest, system  
- [ ] Frontend: bell icon with unread count badge  
- [ ] Click notification to jump to relevant page (trade, backtest)  
- [ ] Notifications persist: not deleted until user deletes  
- [ ] Max 500 notifications per user (auto-delete oldest)

### API Endpoints

GET /api/notifications?limit=20\&unread=true

Response (200):

{

  "notifications": \[

    {

      "notification\_id": "notif\_123",

      "type": "trade\_executed",

      "title": "INFY Bought",

      "message": "INFY @ ₹1500, Target ₹1545",

      "link": "/trades/t\_123",

      "is\_read": false,

      "created\_at": "2026-05-18T10:05:00Z"

    }

  \],

  "unread\_count": 5

}

PATCH /api/notifications/{notification\_id}/read

Response (200):

{

  "message": "Marked as read"

}

### Deliverables

- Notification storage in database  
- Notification CRUD endpoints  
- Frontend notification center UI  
- Real-time notification via WebSocket  
- Unit tests

---

## ATAP-026: End-to-End Testing Plan

**Type**: Task  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: QA Tester  
**Status**: Ready  
**Depends on**: All of Sprint 2 & 3

### Description

Create comprehensive end-to-end tests for critical user flows.

### Acceptance Criteria

- [ ] Test: User registers → creates strategy → backtests → deploys live  
- [ ] Test: Strategy executes → order placed → position tracked → manual close  
- [ ] Test: SL/TP hit → position closed → email sent → trade in history  
- [ ] Test: Daily loss limit → strategies paused → manual resume  
- [ ] Test: Error scenarios (broker API down, market data unavailable)  
- [ ] Tests run in CI/CD pipeline on every commit  
- [ ] Tests use mocked broker APIs (no real API calls)  
- [ ] Tests use test database (separate from production)  
- [ ] Test coverage: 80%+ of critical paths  
- [ ] Automated regression test suite

### Technical Notes

- Use Playwright for frontend E2E tests  
- Use pytest for backend integration tests  
- Mock all external APIs (broker, price data)  
- Parallel test execution for speed  
- Test database: PostgreSQL container

### Deliverables

- Playwright E2E test suite  
- Pytest integration tests  
- CI/CD pipeline configuration  
- Test documentation

---

# 🚀 SPRINT 4: Phase 2 \- Advanced Features (Weeks 5-6)

**Goal**: Expand to multiple strategies, advanced analytics, marketplace foundation  
**Story Points**: 24  
**Dependencies**: Sprint 1-3 complete

---

## ATAP-027: Portfolio Analytics Dashboard

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 5  
**Assignee**: Frontend Lead  
**Status**: Ready  
**Depends on**: ATAP-023

### Description

Implement detailed portfolio analytics and performance metrics.

### Acceptance Criteria

- [ ] Dashboard shows: Monthly P\&L chart, YTD return %, performance vs benchmark  
- [ ] Strategy breakdown: table with strategy, win rate, Sharpe, monthly P\&L, \# trades  
- [ ] Sector allocation: pie chart showing % by sector (IT, Finance, Energy, etc.)  
- [ ] Trade distribution: bar chart showing \# trades per symbol  
- [ ] Equity curve: line chart of portfolio value over 3/6/12 months  
- [ ] Filters: date range, strategy, sector  
- [ ] Exportable as PDF  
- [ ] Responsive design  
- [ ] Data auto-updates (refresh every hour)

### UI Components

- Charts: Recharts (line, bar, pie)  
- Filters: date picker, multi-select  
- Tables: strategy metrics  
- Export: PDF generation

### Deliverables

- Analytics page component  
- API endpoints for metrics  
- Chart components  
- PDF export  
- Unit tests

---

## ATAP-028: Risk Management \- Max Positions & Leverage

**Type**: Feature  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-020

### Description

Implement position and leverage limits for risk control.

### Acceptance Criteria

- [ ] Max open positions setting: e.g., max 5 concurrent positions  
- [ ] Before order placement: check \# open positions \< max  
- [ ] If limit reached: reject order with clear message  
- [ ] Account leverage limit: max 3x leverage (position size / available cash)  
- [ ] Before order: check leverage \<= 3x  
- [ ] If leverage exceeded: reject order  
- [ ] User can adjust limits in settings  
- [ ] Audit log when limits enforced  
- [ ] Unit tests

### Settings Endpoint

PATCH /api/settings/risk

{

  "max\_open\_positions": 5,

  "max\_leverage": 3.0

}

### Deliverables

- Leverage calculation  
- Position limit enforcement  
- Leverage limit enforcement  
- Settings endpoints  
- Unit tests

---

## ATAP-029: MT5 Connector (Windows Integration)

**Type**: Feature  
**Priority**: P0 \- Critical  
**Story Points**: 8  
**Assignee**: Backend Lead (hands-on with Windows VPS)  
**Status**: Ready  
**Depends on**: ATAP-009, ATAP-018

### Description

Implement REST API wrapper for MetaTrader5 terminal for alternative broker execution.

### Acceptance Criteria

- [ ] Windows VPS with MT5 terminal running 24/7  
- [ ] Python REST API wrapper (Flask) on VPS  
- [ ] Endpoints: place\_order, close\_position, get\_positions, get\_account\_info  
- [ ] Main server calls REST API on VPS (not direct MT5 SDK)  
- [ ] Order status: pending → executed → closed  
- [ ] Error handling: VPS offline, MT5 crash, network timeout  
- [ ] Auto-restart if MT5 crashes  
- [ ] Heartbeat monitoring (ping VPS every 60 seconds)  
- [ ] Fallback: if VPS down, queue orders, retry when online  
- [ ] Integration tests (real MT5 testing, manual)  
- [ ] Documentation for VPS setup

### MT5 API Endpoints

POST /api/mt5/place-order

{

  "symbol": "EURUSD",

  "volume": 0.1,

  "price": 1.0850,

  "sl": 1.0800,

  "tp": 1.0900

}

Response:

{

  "order\_id": 123456,

  "status": "pending"

}

POST /api/mt5/close-order

{

  "order\_id": 123456

}

GET /api/mt5/positions

Response:

{

  "positions": \[

    {

      "order\_id": 123456,

      "symbol": "EURUSD",

      "volume": 0.1,

      "price\_open": 1.0850,

      "price\_current": 1.0860,

      "pnl": 100

    }

  \]

}

GET /api/mt5/account-info

Response:

{

  "balance": 50000,

  "equity": 51000,

  "free\_margin": 40000,

  "margin\_level": 127.5

}

### Technical Notes

- MT5 Python package (limited functionality)  
- Create Flask API on Windows VPS  
- Main server: communicate via HTTPS (SSL cert)  
- Retry logic: 3 attempts with 5-sec backoff  
- Heartbeat: if no response in 60 seconds, mark VPS as down  
- Order queue: store orders in Redis if VPS down, flush when online  
- Setup docs: VPS IP, port, SSL cert, firewall config

### Deliverables

- Windows VPS setup guide  
- Flask REST wrapper on VPS  
- Integration with main server  
- Health check/monitoring  
- Fallback logic  
- Documentation  
- Integration tests (manual on real VPS)

---

## ATAP-030: Advanced Strategy Types (Phase 2\)

**Type**: Feature  
**Priority**: P2 \- Medium  
**Story Points**: 5  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: ATAP-011

### Description

Support additional strategy types: Moving Average Ribbon, Stochastic, MACD.

### Acceptance Criteria

- [ ] Moving Average Ribbon: multiple EMAs (5, 10, 20, 50\)  
      - Entry: all MAs aligned (prices in order)  
      - Exit: ribbon breaks down  
- [ ] Stochastic: %K and %D lines  
      - Entry: %K \< 20 (oversold)  
      - Exit: %K \> 80 (overbought)  
- [ ] MACD: MACD line vs signal line  
      - Entry: MACD crosses above signal  
      - Exit: MACD crosses below signal  
- [ ] Backtest support for all new types  
- [ ] Parameter validation per strategy type  
- [ ] Unit tests

### Deliverables

- Strategy type implementations  
- Indicator calculations  
- Parameter validation  
- Unit tests

---

## ATAP-031: Marketplace Foundation \- Strategy Sharing

**Type**: Feature  
**Priority**: P2 \- Medium  
**Story Points**: 5  
**Assignee**: Backend Lead \+ Frontend Lead  
**Status**: Ready  
**Depends on**: ATAP-011, ATAP-023

### Description

Implement strategy marketplace where users can share and discover strategies.

### Acceptance Criteria

- [ ] Share button: publish strategy publicly (opt-in)  
- [ ] Marketplace page: browse community strategies  
- [ ] Strategy card: name, creator, win rate, Sharpe, followers, description  
- [ ] Search and filter: symbol, strategy type, win rate min  
- [ ] Sort: by popularity, win rate, Sharpe ratio  
- [ ] Detailed strategy page: backtest results, equity curve, sample trades  
- [ ] Follow button: user can follow creator's strategy  
- [ ] Rating system: 1-5 stars with reviews  
- [ ] Revenue share: 20% of Pro subscription goes to creator  
- [ ] Creator dashboard: followers, revenue earned, strategy stats  
- [ ] Disclaimer: "Past performance doesn't guarantee future results"  
- [ ] Unit tests

### API Endpoints

POST /api/marketplace/publish/{strategy\_id}

{

  "description": "EMA optimized for RELIANCE",

  "risk\_profile": "medium"

}

Response (201):

{

  "marketplace\_id": "mp\_123",

  "status": "published"

}

GET /api/marketplace/strategies?symbol=INFY\&sort=-win\_rate

Response (200):

{

  "strategies": \[

    {

      "marketplace\_id": "mp\_123",

      "name": "EMA 5-20",

      "creator": "raj\_trader",

      "win\_rate": 0.52,

      "sharpe\_ratio": 1.2,

      "followers": 45,

      "rating": 4.5,

      "description": "EMA optimized for RELIANCE"

    }

  \]

}

POST /api/marketplace/{marketplace\_id}/follow

Response (201):

{

  "message": "Following strategy"

}

POST /api/marketplace/{marketplace\_id}/rate

{

  "rating": 5,

  "review": "Great strategy, consistent profits\!"

}

### Deliverables

- Marketplace strategy model  
- Publish/unpublish endpoints  
- Marketplace listing page  
- Strategy detail page  
- Follow/unfollow logic  
- Rating system  
- Creator dashboard  
- Unit tests

---

## ATAP-032: Creator Revenue Dashboard

**Type**: Feature  
**Priority**: P2 \- Medium  
**Story Points**: 3  
**Assignee**: Frontend Lead  
**Status**: Ready  
**Depends on**: ATAP-031

### Description

Dashboard for strategy creators showing followers, earnings, and strategy performance.

### Acceptance Criteria

- [ ] My Strategies page: list of published strategies  
- [ ] For each: followers count, monthly revenue, rating, \# ratings  
- [ ] Total earnings: sum of all strategy revenue  
- [ ] Revenue chart: monthly earnings over time  
- [ ] Followers list: users following strategies  
- [ ] Strategy performance: compare backtest vs follower's live results  
- [ ] Payout history: when revenue paid out  
- [ ] Payout method: bank account, UPI  
- [ ] Responsive design

### Deliverables

- Creator dashboard page  
- Revenue analytics  
- Payout management  
- Unit tests

---

# ✨ SPRINT 5: Polish & Launch (Weeks 7-8)

**Goal**: Bug fixes, security, documentation, deployment  
**Story Points**: 21  
**Dependencies**: Sprint 1-4 complete

---

## ATAP-033: Security Audit & Hardening

**Type**: Task  
**Priority**: P0 \- Critical  
**Story Points**: 5  
**Assignee**: Security Review Lead  
**Status**: Ready  
**Depends on**: All previous sprints

### Description

Comprehensive security review and hardening of entire platform.

### Acceptance Criteria

- [ ] Code security review: SQL injection, XSS, CSRF prevention  
- [ ] Encryption audit: credentials, sensitive data at rest/transit  
- [ ] API security: rate limiting, CORS configuration, request validation  
- [ ] Authentication: JWT implementation, token expiry handling  
- [ ] Authorization: role-based access control verified  
- [ ] Secrets management: no API keys in code, environment variables used  
- [ ] Database security: parameterized queries, no hardcoded credentials  
- [ ] Network security: HTTPS enforced, SSL certificate valid  
- [ ] OWASP top 10: vulnerability checklist completed  
- [ ] Penetration testing: manual security testing  
- [ ] Security audit report: findings \+ fixes

### Checklist

☐ SQL Injection: All queries use parameterized statements

☐ XSS Prevention: Input validation, output encoding

☐ CSRF Protection: CSRF tokens on state-changing requests

☐ Credentials: Encrypted at rest, never logged

☐ Passwords: Hashed with bcrypt, proper salt

☐ JWT Tokens: Expires, secure signature, httpOnly cookies

☐ API Rate Limiting: 100 requests/min per user

☐ CORS: Only allowed origins configured

☐ HTTPS: All traffic encrypted

☐ SSL: Valid certificate, proper expiry

☐ Environment Variables: .env file, never committed

☐ Database: Backups encrypted, access controlled

☐ Logging: Sensitive data excluded

☐ Dependencies: No known vulnerabilities (check with safety/pip-audit)

### Deliverables

- Security audit report  
- Vulnerability fixes  
- Security testing checklist  
- Documentation

---

## ATAP-034: Performance Optimization

**Type**: Task  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Backend Lead  
**Status**: Ready  
**Depends on**: Sprint 3

### Description

Optimize performance for production deployment.

### Acceptance Criteria

- [ ] Database: Query optimization, index review  
- [ ] API response time: p95 \< 200ms  
- [ ] Backtest speed: 1 year of data \< 30 seconds  
- [ ] Caching: Redis caching for frequently accessed data  
- [ ] Load testing: 1000 concurrent users, 100 trades/sec  
- [ ] Frontend bundle: minified, optimized images  
- [ ] Database connections: connection pooling  
- [ ] Async tasks: Celery performance tuning  
- [ ] Monitoring: APM configured (DataDog/New Relic)  
- [ ] Load tests documented

### Deliverables

- Performance benchmarks  
- Optimization report  
- Load test results  
- Monitoring setup

---

## ATAP-035: Documentation & User Guides

**Type**: Task  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: Tech Writer  
**Status**: Ready  
**Depends on**: All features complete

### Description

Create comprehensive documentation for users and developers.

### Acceptance Criteria

- [ ] User guide: how to create strategies, backtest, deploy live  
- [ ] API documentation: all endpoints documented  
- [ ] Deployment guide: AWS setup, Docker, environment config  
- [ ] Developer guide: architecture, code structure, testing  
- [ ] Architecture diagrams: system design, data flow  
- [ ] FAQ: common questions and troubleshooting  
- [ ] Video tutorials: quick start, key features  
- [ ] Troubleshooting guide: common issues \+ solutions  
- [ ] All docs reviewed by product team

### Documentation Structure

/docs

├── user-guide/

│   ├── getting-started.md

│   ├── creating-strategies.md

│   ├── backtesting.md

│   ├── live-trading.md

│   └── portfolio-analytics.md

├── api/

│   ├── authentication.md

│   ├── strategies.md

│   ├── backtesting.md

│   └── positions.md

├── deployment/

│   ├── aws-setup.md

│   ├── docker-config.md

│   └── environment-variables.md

├── developer/

│   ├── architecture.md

│   ├── code-structure.md

│   ├── testing.md

│   └── contributing.md

└── faq.md

### Deliverables

- Complete user documentation  
- API documentation  
- Deployment guides  
- Developer guides  
- Video tutorials

---

## ATAP-036: Deployment to AWS

**Type**: Task  
**Priority**: P0 \- Critical  
**Story Points**: 5  
**Assignee**: DevOps/Architect  
**Status**: Ready  
**Depends on**: ATAP-033

### Description

Deploy platform to AWS for production use.

### Acceptance Criteria

- [ ] Infrastructure as Code: Terraform/CloudFormation  
- [ ] Services: EC2 (app), RDS (database), ElastiCache (Redis)  
- [ ] Load balancer: distribute traffic across app instances  
- [ ] Auto-scaling: scale based on CPU/memory  
- [ ] Monitoring: CloudWatch dashboards  
- [ ] Logging: CloudWatch Logs, centralized logging  
- [ ] Backups: automated daily RDS backups, 30-day retention  
- [ ] SSL certificate: AWS Certificate Manager  
- [ ] Domain: Route53 DNS configuration  
- [ ] CI/CD: GitHub Actions → AWS deployment  
- [ ] Database migrations: automated on deployment  
- [ ] Rollback strategy: previous version can be deployed  
- [ ] Documentation: AWS setup guide

### AWS Architecture

Internet

    ↓

CloudFront (CDN for static assets)

    ↓

Application Load Balancer

    ↓

EC2 (Auto Scaling Group) \- FastAPI servers

    ↓

RDS PostgreSQL (Multi-AZ for HA)

ElastiCache Redis (Cluster for HA)

    ↓

S3 (backups, exports)

### Deliverables

- Terraform/CloudFormation code  
- Deployment documentation  
- Monitoring dashboards  
- Backup/restore procedures  
- CI/CD pipeline

---

## ATAP-037: Testing & Bug Fixes

**Type**: Task  
**Priority**: P1 \- High  
**Story Points**: 3  
**Assignee**: QA Tester  
**Status**: Ready  
**Depends on**: All features

### Description

Final comprehensive testing and bug fixes before launch.

### Acceptance Criteria

- [ ] Regression testing: all features tested  
- [ ] Browser testing: Chrome, Firefox, Safari, Edge  
- [ ] Mobile testing: iOS, Android (responsive design)  
- [ ] Real broker testing: Angel One API (demo account)  
- [ ] Real broker testing: Zerodha API (demo account)  
- [ ] Load testing: 1000 concurrent users  
- [ ] Edge cases: network failures, broker outages, etc.  
- [ ] User acceptance testing: 5+ beta users test  
- [ ] Bug reporting: all issues logged in GitHub  
- [ ] Critical bugs: P0 fixed before launch  
- [ ] Non-critical bugs: P1/P2 tracked for post-launch

### Testing Matrix

Feature | Chrome | Firefox | Safari | Edge | iOS | Android

────────────────────────────────────────────────────────

Auth    |   ✓    |   ✓    |   ✓   |  ✓  |  ✓  |   ✓

Strategy|   ✓    |   ✓    |   ✓   |  ✓  |  ✓  |   ✓

Backtest|   ✓    |   ✓    |   ✓   |  ✓  |  ✓  |   ✓

...

### Deliverables

- Test report  
- Bug list with fixes  
- Beta feedback summary

---

## ATAP-038: Beta Launch & Community Feedback

**Type**: Task  
**Priority**: P1 \- High  
**Story Points**: 2  
**Assignee**: Product Manager  
**Status**: Ready  
**Depends on**: ATAP-036, ATAP-037

### Description

Soft launch to beta users, gather feedback, iterate.

### Acceptance Criteria

- [ ] Invite 50-100 beta users (trading communities, personal network)  
- [ ] Collect feedback via survey and in-app feedback form  
- [ ] Monitor user behavior (analytics)  
- [ ] Track bug reports  
- [ ] Weekly feedback summary  
- [ ] Address critical issues immediately  
- [ ] Build public roadmap  
- [ ] Announce upcoming features  
- [ ] Beta success criteria: 30% activation, positive NPS

### Launch Checklist

☐ Beta access link ready

☐ Onboarding email sent

☐ Support channel (Discord, email)

☐ Feedback form deployed

☐ Analytics tracking active

☐ Daily monitoring of errors/issues

☐ Weekly feedback review meeting

☐ Public roadmap published

☐ Community engagement (Twitter, LinkedIn posts)

### Deliverables

- Beta user invite list  
- Feedback summary  
- Roadmap document  
- Community channel setup

---

## ATAP-039: Post-Launch Monitoring & Support

**Type**: Task  
**Priority**: P1 \- High  
**Story Points**: 2  
**Assignee**: Operations/Support  
**Status**: Ready  
**Depends on**: ATAP-036

### Description

Monitor production system and provide user support.

### Acceptance Criteria

- [ ] 24/7 monitoring: alerts for errors, downtime  
- [ ] On-call support: immediate response to critical issues  
- [ ] User support channel: email, Discord, Twitter  
- [ ] SLA: respond to support within 4 hours  
- [ ] Bug tracking: prioritize and fix quickly  
- [ ] Metrics dashboard: user count, trades executed, errors  
- [ ] Weekly health report: uptime, errors, performance  
- [ ] Quarterly review: feature adoption, user feedback

### Monitoring Dashboard

Real-time metrics:

\- Uptime (target: 99.9%)

\- API latency (p95: 200ms)

\- Error rate (target: \< 0.5%)

\- Active users

\- Trades executed

\- Revenue (MRR)

### Deliverables

- Monitoring setup  
- On-call rotation schedule  
- Support processes  
- Health dashboards

---

# 📊 Summary Table

| Sprint | Weeks | Issues | Story Points | Focus |
| :---- | :---- | :---- | :---- | :---- |
| 1 | 1-2 | 8 | 28 | Foundation & Auth |
| 2 | 2-3 | 10 | 32 | Core Features |
| 3 | 3-4 | 7 | 22 | Integration & Live Trading |
| 4 | 5-6 | 6 | 24 | Phase 2 Advanced |
| 5 | 7-8 | 8 | 21 | Polish & Launch |
| **Total** | **8** | **39** | **127** | **MVP \+ Phase 2** |

---

# 🎯 Critical Path (Must Do First)

**Sprint 1** (Foundation) → ATAP-001 to ATAP-008 **Sprint 2** (Core) → ATAP-009 to ATAP-017 **Sprint 3** (Integration) → ATAP-018 to ATAP-026 → **MVP READY** after Sprint 3

**Sprint 4** (Phase 2\) → ATAP-027 to ATAP-032 **Sprint 5** (Launch) → ATAP-033 to ATAP-039 → **Full platform ready** after Sprint 5

---

# 📋 How to Use This Document

1. **Copy issues one-by-one to GitHub**  
2. **Assign to team members**  
3. **Estimate story points** (you can override my estimates)  
4. **Create milestones**: Sprint 1, Sprint 2, etc.  
5. **Create project board**: Backlog → In Progress → In Review → Done  
6. **Track velocity**: measure story points completed per sprint  
7. **Update on sprint completion**

---

**Ready to copy these into GitHub? Just say "yes" and I'll format them as GitHub issue templates\! 🚀**  
