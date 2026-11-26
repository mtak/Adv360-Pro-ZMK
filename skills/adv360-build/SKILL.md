---
name: adv360-build
description: Build Kinesis Advantage 360 Pro ZMK firmware from this repository's keymap and configuration using its local Docker build or GitHub Actions workflow.
---

# Build Advantage 360 Pro firmware

Use this skill when the user asks to compile or validate firmware for this repository. Read the repository `README.md`, `Makefile`, and `.github/workflows/build.yml` when build behavior may have changed.

## Local build

The repository's supported local build uses Docker (or Podman) and Make:

- `make` builds both halves and writes UF2 files into `firmware/`.
- `make left` builds only the left half.
- The build image is `zmkfirmware/zmk-build-arm:stable`; West dependencies are pinned through `config/west.yml`.
- `make clean` removes generated firmware and the local build image. Run it only when cleanup is requested or needed to resolve a stale image/build.

The Makefile runs `bin/get_version_local.sh` before building. This generates `config/version.dtsi` using the current date, branch, and commit. Treat this file as generated build input and avoid including its transient contents in unrelated source changes. The Makefile restores it after a successful container run; if a build stops before that step, restore only this generated file to its prior state before committing.

For constrained environments, Docker supports `DOCKER_CONFIG=/tmp/<task-specific-dir>` to put its CLI state in a writable temporary directory. The Makefile's container run uses `-it`, so invoke it with a PTY when the environment requires one.

## GitHub Actions

`.github/workflows/build.yml` builds both legacy and Clique firmware for both halves on pushes and pull requests. Use the workflow when the user wants the hosted build or when a local Docker build is unavailable. Check the resulting run and artifacts before reporting success.

## Reporting

State which build path and targets completed. A successful image build alone does not mean firmware compiled; confirm the `west build` steps completed and the expected UF2 files were produced.
