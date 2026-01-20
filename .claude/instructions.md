# Hummingbot Development Instructions

Instructions for AI coding assistants working on Hummingbot trading bot.

## Project Vision

Hummingbot is an **open-source framework for building automated trading strategies** (bots) that run on centralized and decentralized exchanges. It's written in Python with some Cython components for performance.

**Current Focus:** Integrating with ETCswap on Ethereum Classic via the Gateway middleware.

**Architecture:** Hummingbot (Python client) communicates with Gateway (TypeScript middleware) for DEX interactions.

## Tech Stack

**Framework & Runtime:**
- Python: 3.10+
- Cython: Performance-critical components
- Conda: Environment management

**Key Dependencies:**
- aiohttp: Async HTTP client
- pandas: Data analysis
- pydantic: Data validation
- SQLAlchemy: Database ORM

## Quick Start

```bash
# Create conda environment
conda env create -f setup/environment.yml

# Activate environment
conda activate hummingbot

# Install in development mode
pip install -e .

# Run Hummingbot
./start

# Run tests
make test

# Run linting
make lint
```

## Core Architecture

### Connector Types

**CLOB CEX (Centralized Exchanges):**
- Location: `hummingbot/connector/exchange/`
- Base class: `ExchangePyBase`
- Examples: Binance, Coinbase, KuCoin

**CLOB DEX (Decentralized Order Books):**
- Location: `hummingbot/connector/exchange/`
- Examples: dYdX, Injective

**Perpetual Derivatives:**
- Location: `hummingbot/connector/derivative/`
- Base class: `PerpetualDerivativePyBase`

**Gateway DEX (AMM/CLMM via Gateway):**
- Location: `hummingbot/connector/gateway/`
- Uses Gateway middleware for DEX interactions
- Connectors: `gateway_swap.py`, `gateway_lp.py`

### Gateway Integration

Gateway connectors are **not implemented in Hummingbot directly**. They use:
- `GatewayHttpClient` - HTTP client for Gateway API
- `GatewaySwap` - Generic swap connector
- `GatewayLp` - Generic liquidity provider connector

**DEX connectors like ETCswap are implemented in the Gateway repo, not here.**

### Directory Structure

```
hummingbot/
├── client/           # CLI and user interface
├── connector/        # Exchange connectors
│   ├── exchange/     # Spot CEX connectors
│   ├── derivative/   # Perpetual connectors
│   ├── gateway/      # Gateway DEX connectors
│   └── utilities/    # Helper utilities
├── core/             # Core framework
│   ├── data_type/    # Order, Trade, OrderBook types
│   ├── event/        # Event system
│   ├── gateway/      # Gateway HTTP client
│   └── rate_oracle/  # Price feeds
├── strategy/         # Legacy strategies
├── strategy_v2/      # New strategy framework
└── templates/        # Configuration templates
```

### Connector File Structure (CEX)

```
hummingbot/connector/exchange/{name}/
├── __init__.py
├── {name}_exchange.py           # Main connector class
├── {name}_api_order_book_data_source.py
├── {name}_api_user_stream_data_source.py
├── {name}_auth.py               # Authentication
├── {name}_constants.py          # Constants, rate limits
├── {name}_utils.py              # Utilities
├── {name}_web_utils.py          # Web utilities
└── README.md
```

## Gateway Configuration

Gateway connectors are configured in Gateway, not Hummingbot. The `GatewayHttpClient` discovers available connectors from Gateway automatically.

**Key files:**
- `hummingbot/core/gateway/gateway_http_client.py` - Gateway API client
- `hummingbot/connector/gateway/gateway_base.py` - Base gateway connector
- `hummingbot/connector/gateway/gateway_swap.py` - Swap operations
- `hummingbot/connector/gateway/gateway_lp.py` - LP operations

## ETCswap Integration

ETCswap is available in Hummingbot via the Gateway middleware. The connector names are:
- `etcswap/router` - Universal Router for optimized cross-V2/V3 routing ✅
- `etcswap/amm` - V2 AMM swaps and liquidity ✅
- `etcswap/clmm` - V3 concentrated liquidity swaps ✅

**Networks supported:** `classic` (mainnet), `mordor` (testnet)

**No code changes needed in this repo for ETCswap** - it's all implemented in the Gateway repo.

**Documentation:** See [docs/etcswap/](../docs/etcswap/) for ETCswap integration guide.

**Cross-Chain Arbitrage:** Use the `amm_arb` strategy with:
- Primary: `coinbase` with `ETC-USDC`
- Secondary: `etcswap_ethereum_classic` with `ETC-USC`
- USC is 1:1 with USDC through [Brale Platform](https://brale.xyz)

## Protected Files

Do not modify without explicit request:
- `hummingbot/client/settings.py` - Global settings
- `hummingbot/core/gateway/gateway_http_client.py` - Gateway client
- `setup.py` - Package setup
- `setup/environment.yml` - Conda environment

## Coding Style

- Python 3.10+ type hints
- PEP 8 style guide
- 4-space indentation
- Double quotes for strings
- Docstrings for public methods
- Use `async/await` for async code

## Validation Requirements

**Before Any Commit:**
```bash
make lint      # Flake8 linting
make test      # Run tests
```

## Commit Format

```
(<scope>) <description>

Co-Authored-By: Claude <noreply@anthropic.com>
```

**Scopes (per CONTRIBUTING.md):**
- `feat` - New features
- `fix` - Bug fixes
- `refactor` - Code refactoring
- `cleanup` - Code cleanup
- `doc` - Documentation

**Examples:**
```
(feat) add support for ETCswap gateway connector
(doc) update gateway configuration guide
```

## Testing

```bash
# Run all tests
make test

# Run specific test
pytest test/path/to/test_file.py -v

# Run with coverage
make development-diff-cover
```

**Minimum 80% coverage for PRs.**

## Common Pitfalls

**Don't:**
- Implement DEX connectors here (use Gateway)
- Modify gateway_http_client.py without understanding the protocol
- Skip type hints
- Use synchronous code in async contexts

**Do:**
- Use Gateway for all DEX interactions
- Follow existing connector patterns
- Write tests for new code
- Use the logger (not print)

## Ecosystem Context

- **Hummingbot** (THIS PROJECT): Python trading bot
- **Gateway**: TypeScript DEX middleware (separate repo)
- **ETCswap**: DEX on Ethereum Classic (implemented in Gateway)

**Related Repos:**
- `hummingbot-gateway` - Gateway middleware
- `hummingbot/dashboard` - Web dashboard
- `hummingbot/deploy` - Deployment tools

---

Last updated: 2025-01-20
