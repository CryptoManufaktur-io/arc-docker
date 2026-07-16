# Overview

Docker Compose for [Arc Mainnet](https://docs.arc.io/arc-chain) — a purpose-built Layer-1 blockchain
with USDC as gas, sub-second deterministic finality, and full EVM compatibility.

Meant to be used with [central-proxy-docker](https://github.com/CryptoManufaktur-io/central-proxy-docker) for traefik
and Prometheus remote write; use `:ext-network.yml` in `COMPOSE_FILE` inside `.env` in that case.

If you want the RPC ports exposed locally, use `rpc-shared.yml` in `COMPOSE_FILE` inside `.env`.

> **Note:** Arc Mainnet is currently in a private phase. All full nodes must be configured with trusted
> upstream RPC URLs provided by Chainlink Labs via a 1Password email note. Syncing from genesis is not
> supported; snapshots are mandatory.

## Quick Start

`cp default.env .env`

`nano .env` and adjust variables as needed, particularly `EL_UPSTREAM_RPC` and
`CL_UPSTREAM_ENDPOINT_1/2/3` from the 1Password note sent by Chainlink Labs.

See the Jira ticket comment for which endpoint to use for `EL_UPSTREAM_RPC`.

The `CL_UPSTREAM_ENDPOINT` values must use the format `https://...,wss=hostname/path` — note `wss=` without `://`.

On first start, fix volume permissions before bringing the node up:

```bash
docker run --rm -v arc_arc-data:/data alpine chown -R 999:999 /data
docker run --rm -v arc_arc-ipc:/run/arc alpine chown -R 999:999 /run/arc
```

`./arcd up`

Monitor the snapshot download:

```bash
./arcd logs -f arc-snapshots
```

Once snapshots are complete, `arc-init` generates the consensus key automatically (safe to run on every
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