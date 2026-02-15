# .github

Shared GitHub Actions workflows and community health files for [@robkerr1992](https://github.com/robkerr1992) repositories.

## Reusable Workflows

| Workflow | Description | Key Inputs |
|---|---|---|
| `laravel-lint.yml` | PHP Pint linting | `php-version` |
| `laravel-tests.yml` | Laravel PHPUnit tests | `php-versions`, `node-build` |
| `eslint.yml` | ESLint | `node-version`, `lint-path` |
| `typescript-check.yml` | TypeScript type check | `node-version` |
| `preview-deploy.yml` | Forge preview environments | `app-name`, `has-node-build`, `extra-env` |
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