# Overview

Docker Compose for [Arc Mainnet](https://docs.arc.io/arc-chain) — a purpose-built Layer-1 blockchain
with USDC as gas, sub-second deterministic finality, and full EVM compatibility.

Meant to be used with [central-proxy-docker](https://github.com/CryptoManufaktur-io/central-proxy-docker) for traefik
and Prometheus remote write; use `:ext-network.yml` in `COMPOSE_FILE` inside `.env` in that case.

If you want the RPC ports exposed locally, use `rpc-shared.yml` in `COMPOSE_FILE` inside `.env`.

> **Note:** Arc Mainnet is public. Non-validator nodes only run in follow mode (P2P discovery disabled):
> consensus fetches blocks from `CL_UPSTREAM_ENDPOINT_1/2/3` and execution forwards every submitted
> transaction to `EL_UPSTREAM_RPC`. If that upstream is down, the node still syncs but cannot send
> transactions. `default.env` ships the official public endpoints.

## Quick Start

`cp default.env .env`

`nano .env` and adjust variables as needed. The default upstreams are the official public endpoints;
swap in a provider endpoint if you need higher rate limits.

The `CL_UPSTREAM_ENDPOINT` values must use the format `https://...,wss=hostname/path` — note `wss=` without `://`.

Snapshots are only needed for the first bootstrap. Set `EXECUTION_SNAPSHOT_URL`/`CONSENSUS_SNAPSHOT_URL`
to download them; leave them empty to skip `arc-snapshots` and `arc-init` and start from existing data.
Clear them after the initial sync, since snapshot links expire and an expired link blocks `up`.

`./arcd up`

Volume permissions are fixed automatically by the `arc-permissions` service before anything else runs.

Monitor the snapshot download:

```bash
./arcd logs -f arc-snapshots
```

Once snapshots are complete (when snapshot URLs are set), `arc-init` generates the consensus key automatically (safe to run on every
`up` — it skips if the key already exists), then `arc-execution` and `arc-consensus` start syncing automatically.

## Verify the node

Check block height is non-zero and increasing:

```bash
curl -H "content-type: application/json" localhost:8545 \
  -d '{"jsonrpc":"2.0","id":1,"method":"eth_blockNumber","params":[]}'
```

## Software update

To update the software, run `./arcd update` and then `./arcd up`

## Version

This is Arc Docker v1.0.0