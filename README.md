# Overview

Docker Compose for [Arc Mainnet](https://docs.arc.io/arc-chain) — a purpose-built Layer-1 blockchain
with USDC as gas, sub-second deterministic finality, and full EVM compatibility.

Arc node images are hosted on [Cloudsmith](https://cloudsmith.io/).
Docker login credentials for `docker.cloudsmith.io` are required and are provided by Chainlink Labs.

Meant to be used with [central-proxy-docker](https://github.com/CryptoManufaktur-io/central-proxy-docker) for traefik
and Prometheus remote write; use `:ext-network.yml` in `COMPOSE_FILE` inside `.env` in that case.

If you want the RPC ports exposed locally, use `rpc-shared.yml` in `COMPOSE_FILE` inside `.env`.

> **Note:** Arc Mainnet is currently in a private phase. All full nodes must be configured with trusted
> upstream RPC URLs provided by Chainlink Labs via a 1Password email note. Syncing from genesis is not
> supported; snapshots are mandatory.

## Quick Start

`./arcd install` brings in docker-ce, if you don't have Docker installed already.

`cp default.env .env`

`nano .env` and adjust variables as needed, particularly `EL_UPSTREAM_RPC` and
`CL_UPSTREAM_ENDPOINT_1/2/3` from the 1Password note sent by Chainlink Labs.
See the Jira ticket comment for which endpoint to use for `EL_UPSTREAM_RPC`.

`./arcd up`

On first start, monitor the snapshot download with `./arcd logs -f arc-snapshots`.
Once snapshots are complete, `arc` and `arc-consensus` will start automatically.

## Verify the node

Check block height is non-zero and increasing:

```bash
curl -H "content-type: application/json" localhost:8545 \
  -d '{"jsonrpc":"2.0","id":1,"method":"eth_blockNumber","params":[]}'
```

## Software update

To update the software, run `./arcd update` and then `./arcd up`

## Customization

`custom.yml` is not tracked by git and can be used to override anything in the provided yml files.
If you use it, add it to `COMPOSE_FILE` in `.env`.

## Version
This is Arc Docker v1.0.0
