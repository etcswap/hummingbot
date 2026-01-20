# ETCswap Integration for Hummingbot

This documentation covers using ETCswap with Hummingbot for automated trading on Ethereum Classic.

## Overview

ETCswap is the leading decentralized exchange on Ethereum Classic, providing:
- **V2 AMM**: Traditional constant-product pools
- **V3 CLMM**: Concentrated liquidity pools
- **Universal Router**: Optimized routing across V2 and V3

## Quick Start

### 1. Setup Gateway

The ETCswap connector requires Hummingbot Gateway. Clone and set up the gateway:

```bash
git clone https://github.com/etcswap/hummingbot-gateway.git
cd hummingbot-gateway
git checkout etcswap
pnpm install && pnpm build
pnpm start
```

> **Note:** This uses the ETCswap fork. Once merged upstream, use `https://github.com/hummingbot/hummingbot-gateway.git`.

### 2. Connect Gateway in Hummingbot

Start Hummingbot and connect to gateway:

```
gateway connect
```

### 3. Add Ethereum Classic Wallet

```
gateway wallet-add ethereum classic YOUR_PRIVATE_KEY
```

### 4. Approve Tokens

Before trading, approve tokens for ETCswap:

```
gateway approve ethereum classic USC etcswap/router
```

### 5. Start Trading

You can now use ETCswap in your trading strategies!

## Supported Networks

| Network | Config Name | Chain ID | Native Token |
|---------|-------------|----------|--------------|
| Ethereum Classic | `classic` | 61 | ETC |
| Mordor Testnet | `mordor` | 63 | METC |

## Trading Pairs

Common pairs on ETCswap:

| Pair | V2 | V3 | Description |
|------|----|----|-------------|
| ETC/USC | Yes | Yes | ETC against Classic USD stablecoin |
| WETC/USC | Yes | Yes | Wrapped ETC against USC |

## Stablecoins

**USC (Classic USD)** is the primary stablecoin on Ethereum Classic:
- Address: `0xDE093684c796204224BC081f937aa059D903c52a`
- Decimals: 18
- 1:1 with USDC through [Brale Platform](https://brale.xyz)

## Strategies

### AMM Arbitrage

Use ETCswap with centralized exchanges for arbitrage:

```
create
# Select amm_arb strategy
# Primary: coinbase, ETC-USDC
# Secondary: etcswap_ethereum_classic, ETC-USC
```

### Liquidity Mining

Provide liquidity on ETCswap V3 and earn fees:

```
# Coming soon: V3 LP strategy support
```

## Key Differences from Uniswap

| Feature | Uniswap | ETCswap |
|---------|---------|---------|
| Chain | Ethereum | Ethereum Classic |
| Native Token | ETH | ETC |
| Wrapped Token | WETH | WETC |
| Stablecoin | USDC | USC |
| Chain ID | 1 | 61 |

## Gateway Endpoints

ETCswap uses these gateway endpoints:

| Endpoint | Description |
|----------|-------------|
| `/connectors/etcswap/router/*` | Universal Router (recommended) |
| `/connectors/etcswap/amm/*` | V2 AMM direct access |
| `/connectors/etcswap/clmm/*` | V3 CLMM direct access |

## Troubleshooting

### Gateway Connection Issues

```
# Verify gateway is running
curl http://localhost:15888/

# Check wallet is connected
curl http://localhost:15888/wallet?chain=ethereum&network=classic
```

### Token Not Found

Add tokens to the token list at:
- `hummingbot-gateway/src/chains/ethereum/token_lists/classic.json`

### Insufficient Allowance

Re-approve the token:
```
gateway approve ethereum classic TOKEN_SYMBOL etcswap/router
```

## Resources

- [ETCswap Website](https://etcswap.org)
- [ETCswap V3 App](https://v3.etcswap.org)
- [Gateway Documentation](../../hummingbot-gateway/docs/etcswap/)
- [Brale Platform](https://brale.xyz) - USC/USDC bridge
- [Ethereum Classic](https://ethereumclassic.org)

## Branch Information

This ETCswap integration is on the `etcswap` branch. It will remain here for the ETC ecosystem until merged into the official Hummingbot repositories.
