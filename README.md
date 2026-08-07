# Docker build and push action
This project has two branches: `main` and `nix`.

The `main` branch builds a docker image using a Dockerfile and pushes it to a registry.

The `nix` branch first builds a docker image using a nix derivation from a flake, loads the image
into the docker daemon, then builds a new image using a Dockerfile and pushes it to a registry.

The purpose of the `nix` branch is to allow for using a nix built image as a base image.

## Requirements (`nix` branch)

This action no longer installs Nix. It expects a runner with Nix preinstalled
(our `hetzner` scale set) and assumes the caller has already configured
authentication and the binary cache. Callers must run both of these before
invoking the action:

1. `./.github/actions/nix-install` — exports `NIX_CONFIG` with an
   `access-tokens` entry so private `github:stafftastic/*` flake inputs resolve.
2. `Mic92/niks3-action` — points Nix at the shared binary cache, so the base
   image closure is substituted instead of rebuilt.

The job also needs `id-token: write` permission for the niks3 OIDC exchange.

```yaml
jobs:
  build:
    runs-on: hetzner
    permissions:
      contents: read
      id-token: write
    steps:
      - uses: actions/checkout@v4
      - uses: ./.github/actions/nix-install
        with:
          private-key: ${{secrets.NIX_REPOSITORY_ACCESS_PRIVATE_KEY}}
      - uses: Mic92/niks3-action@12d3e320b2a99b7938f9634bd337cd9fec104609 # v1.1.0
        with:
          server-url: https://nix-cache.stafftastic.dev
      - uses: stafftastic/docker-build-push-action@nix
        with: ...
```
