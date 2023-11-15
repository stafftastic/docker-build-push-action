# Docker build and push action
This project has two branches: `main` and `nix`.

The `main` branch builds a docker image using a Dockerfile and pushes it to a registry.

The `nix` branch first builds a docker image using a nix derivation from a flake, loads the image
into the docker daemon, then builds a new image using a Dockerfile and pushes it to a registry.

The purpose of the `nix` branch is to allow for using a nix built image as a base image.
