# dYdX GraphQL API

## Description

dYdX is a decentralized perpetuals exchange built on its own Cosmos-based L1 blockchain (dYdX Chain, v4). The indexer service provides a read-only GraphQL interface on top of the same data model exposed by the REST Comlink API, enabling structured queries across markets, orders, fills, positions, candles, transfers, and funding history.

The GraphQL endpoint is served by the Comlink service within the dYdX Indexer — the off-chain service that indexes on-chain events and exposes them through both REST and GraphQL surfaces.

## Endpoint

```
https://indexer.dydx.trade/graphql
```

Alternative (trading company-operated) endpoint:
```
https://indexer.dydx.trade/graphql
```

Community / testnet endpoint:
```
https://indexer.v4testnet.dydx.exchange/graphql
```

## Authentication

The public indexer GraphQL endpoint does not require authentication for read queries. The endpoint accepts standard GraphQL POST requests with `Content-Type: application/json`.

No API key is needed for read-only access to market data, orderbook snapshots, candles, trades, and public subaccount data.

Write operations (placing/canceling orders) are performed on-chain via the dYdX Chain node gRPC interface using a signed transaction, not through GraphQL.

## Schema Source

The schema in `dydx-schema.graphql` is a conceptual SDL derived from the TypeScript type definitions in the dYdX v4-chain open-source repository:

- Repository: https://github.com/dydxprotocol/v4-chain
- Type source: `indexer/packages/postgres/src/types/`
- Controller surface: `indexer/services/comlink/src/controllers/api/v4/`

The indexer REST API is the primary documented surface. The GraphQL schema mirrors the same data model.

## REST API Equivalent

The same data is accessible via the REST Comlink API:

```
https://indexer.dydx.trade/v4/
```

Key REST endpoints that map to GraphQL types:
- `GET /v4/perpetualMarkets` — PerpetualMarket
- `GET /v4/orderbooks/perpetualMarket/{ticker}` — OrderbookLevel
- `GET /v4/orders` — Order
- `GET /v4/fills` — Fill
- `GET /v4/trades/perpetualMarket/{ticker}` — Trade
- `GET /v4/candles/perpetualMarkets/{ticker}` — Candle
- `GET /v4/addresses/{address}/subaccountNumber/{subaccountNumber}` — Subaccount
- `GET /v4/addresses/{address}/subaccountNumber/{subaccountNumber}/perpetualPositions` — PerpetualPosition
- `GET /v4/addresses/{address}/subaccountNumber/{subaccountNumber}/assetPositions` — AssetPosition
- `GET /v4/transfers` — Transfer
- `GET /v4/historicalFunding/{ticker}` — FundingIndexUpdate
- `GET /v4/historicalPnl` — PnlTick
- `GET /v4/time` — Time
- `GET /v4/height` — Block

## Notes

- dYdX v4 is a sovereign Cosmos appchain, not an EVM chain. Addresses follow the `dydx1...` bech32 format.
- Subaccounts are the core account primitive: each wallet address can have multiple numbered subaccounts.
- All prices and sizes use string representation to preserve precision.
- The `clobPairId` field is the on-chain identifier for a market's central limit order book pair.
- Perpetual positions track margin, funding payments (`settledFunding`), and realized PnL.
- Candle resolutions: 1MIN, 5MINS, 15MINS, 30MINS, 1HOUR, 4HOURS, 1DAY.
- Funding rate updates occur every hour on dYdX Chain.
