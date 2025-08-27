# x402 Axios Starter

Starter for running an x402 axios client.

You can find the upstream example at [coinbase/x402 → examples/typescript/clients/axios](https://github.com/coinbase/x402/tree/main/examples/typescript/clients/axios).

## Getting Started

### Requirements

- **Node.js**: 18 or newer

### Create a new app using the template

Use your preferred package manager to scaffold:

#### npm (npx)
```bash
npm exec @payai/x402-axios-starter -- my-x402-app
```

#### pnpm
```bash
pnpm dlx @payai/x402-axios-starter my-x402-app
```

#### bun
```bash
bunx @payai/x402-axios-starter my-x402-app
```

Then inside your new app:

```bash
npm run dev
```

### How the created client example works

When you run the generated app, `index.ts` will:

- Load environment variables from `.env` (for example: `RESOURCE_SERVER_URL`, `ENDPOINT_PATH`, `PRIVATE_KEY`).
- Create an Axios instance and attach x402 payment interceptor using `withPaymentInterceptor`.
- Call your configured endpoint at `${RESOURCE_SERVER_URL}${ENDPOINT_PATH}`.
- Log two things to the console:
  - The parsed JSON response body
  - The decoded `x-payment-response` headers (useful for inspecting payment receipts/metadata)


## The Starter Itself

Below are notes on the starter itself, which creates the example that devs use to get started.

### How sync works

- Workflow: `.github/workflows/sync.yml`
- Triggered hourly (cron) and on manual dispatch.
- Steps (high level):
  - Sparse clone upstream `coinbase/x402` and restrict to `examples/typescript/clients/axios`.
  - Resolve latest `x402-axios` version from npm (best-effort).
  - Mirror files into `vendor/upstream/` (transient; ignored in git and cleaned up).
  - Run `scripts/sanitize.sh` to:
    - Copy all files from `vendor/upstream/` into `template/` (root of the template), preserving structure.
    - Remove any legacy `template/src/` directory.
    - Refresh `NOTICE` with the upstream commit and clean up `vendor/` and `upstream/` directories.
  - Inject the resolved `x402-axios` version into `template/package.json` (replacing any workspace reference).
  - Open a PR with the changes using `peter-evans/create-pull-request`.

Notes:

- If `npm view x402-axios version` fails, the workflow falls back to `0.0.0` and will skip injecting the dependency until it is available.
- The template mirrors the upstream example at the template root (no `src/` in the template). Your generated app runs from its root.

### Local development of this starter

```bash
# run the sanitize/mapping script locally (after an upstream sync or manual vendor update)
scripts/sanitize.sh examples/typescript/clients/axios <commit-sha>
```

Key files:

- `template/` – shipped starter template; mirrors upstream example at root
- `vendor/upstream/` – transient mirror used during sync (gitignored and cleaned)
- `.github/workflows/sync.yml` – sync/PR workflow
- `scripts/sanitize.sh` – maps upstream example into `template/` (root)
- `bin/create.js` – CLI that scaffolds a new project from `template/`

### Releasing this starter to npm (optional)

- The `Release` workflow publishes on pushes to `main`.
- Requires `NPM_TOKEN` secret configured in the repo.

### License and attribution

Apache-2.0. Portions are derived from `coinbase/x402` (see `NOTICE`, `LICENSE`, and upstream notices).
