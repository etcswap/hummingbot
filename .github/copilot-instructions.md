# GitHub Copilot Instructions for Hummingbot

## Project Overview

Hummingbot is an open-source Python framework for automated trading strategies on CEX and DEX. DEX interactions happen via Gateway middleware.

## Tech Stack

- Python 3.10+, Cython for performance
- Conda for environment management
- aiohttp, pandas, pydantic, SQLAlchemy

## Commands

```bash
conda activate hummingbot    # Activate environment
./start                      # Run Hummingbot
make test                    # Run tests
make lint                    # Linting
```

## Key Patterns

### Connector Types
- **CEX**: `hummingbot/connector/exchange/` - Direct API integration
- **Derivatives**: `hummingbot/connector/derivative/` - Perpetuals
- **Gateway DEX**: `hummingbot/connector/gateway/` - Via Gateway middleware

### Gateway Integration
DEX connectors (Uniswap, ETCswap, etc.) are **implemented in Gateway, not here**.
- `GatewayHttpClient` - Communicates with Gateway API
- `GatewaySwap` - Generic swap operations
- `GatewayLp` - Generic LP operations

### CEX Connector Structure
```
hummingbot/connector/exchange/{name}/
├── {name}_exchange.py           # Main class
├── {name}_api_order_book_data_source.py
├── {name}_api_user_stream_data_source.py
├── {name}_auth.py
├── {name}_constants.py
└── {name}_utils.py
```

## ETCswap Note

ETCswap is implemented in **Gateway**, not this repo. Once Gateway has ETCswap configured, it will be available as:
- `etcswap/amm` - V2 swaps
- `etcswap/clmm` - V3 concentrated liquidity

## Protected Files

- `hummingbot/client/settings.py`
- `hummingbot/core/gateway/gateway_http_client.py`
- `setup.py`, `setup/environment.yml`

## Code Style

- Python 3.10+ type hints
- PEP 8, 4-space indentation
- Double quotes, docstrings for public methods
- async/await for async code

## Validation Before Commit

```bash
make lint && make test
```

## Commit Format

```
(<scope>) <description>
```

Scopes: `feat`, `fix`, `refactor`, `cleanup`, `doc`

## Common Pitfalls

- Don't implement DEX connectors here (use Gateway)
- Don't use synchronous code in async contexts
- Do follow existing connector patterns
- Do write tests (80% coverage minimum)
