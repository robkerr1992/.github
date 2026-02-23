# .github

Shared GitHub Actions workflows and community health files for [@robkerr1992](https://github.com/robkerr1992) repositories.

## Reusable Workflows

| Workflow | Description | Key Inputs |
|---|---|---|
| `laravel-lint.yml` | PHP Pint linting | `php-version` |
| `laravel-tests.yml` | Laravel PHPUnit tests | `php-versions`, `node-build` |
| `eslint.yml` | ESLint | `node-version`, `lint-path` |
| `typescript-check.yml` | TypeScript type check | `node-version` |
| `preview-deploy.yml` | Forge preview environments | `app-name`, `has-node-build`, `extra-env`, `debug-deploy` |
| `preview-teardown.yml` | Tear down preview environments | — |

### Usage

```yaml
jobs:
  lint:
    uses: robkerr1992/.github/.github/workflows/laravel-lint.yml@main
```

For workflows that need secrets (preview deploy/teardown):
```yaml
jobs:
  deploy:
    uses: robkerr1992/.github/.github/workflows/preview-deploy.yml@main
    with:
      app-name: MyApp
    secrets: inherit
```

### Preview Deploy

Provisions a Forge preview site for pull requests with `[preview]` in the title.

**What it does:**
1. Provisions the site via [Laravel Harbor](https://github.com/mehrancodes/laravel-harbor)
2. Writes the `.env` via the Forge API (with DB, app URL, and optional Stripe credentials)
3. Provisions Let's Encrypt SSL if the site is not already secured
4. Sets the deploy script via the Forge API (Harbor does not persist it)
5. Enables quick deploy and triggers a deployment
6. Comments the PR with the preview URL (once)

**Inputs:**

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `app-name` | string | *required* | Application name (used in `.env` `APP_NAME` and `VITE_APP_NAME`) |
| `has-node-build` | boolean | `true` | Whether the app has a Node build step (`npm ci` + `npm run build`) |
| `extra-env` | string | `''` | Additional `.env` lines to append |
| `debug-deploy` | boolean | `false` | Wait for deploy to complete and output the Forge deploy log |

**Required secrets:** `FORGE_TOKEN`, `FORGE_SERVER`, `FORGE_DB_PASSWORD`

**Optional secrets:** `FORGE_STRIPE_KEY`, `FORGE_STRIPE_SECRET`, `FORGE_STRIPE_WEBHOOK_SECRET`

**Debugging deploys:**

When a deploy isn't working as expected, enable `debug-deploy` to see the Forge server-side deploy log in the GitHub Actions output:

```yaml
with:
  app-name: MyApp
  debug-deploy: true
```

This polls the Forge API until the deploy completes (up to 4 minutes), then fetches and outputs the full deploy log. Disable it once you've diagnosed the issue to keep workflow runs fast.