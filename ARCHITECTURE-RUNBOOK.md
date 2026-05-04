# Nostr Relay Core Architecture Runbook

Last updated: 2026-05-04

## Purpose

This runbook defines the core relay architecture and release contract for the `satwise/nostr-rs-relay` codebase.

This repository is the source of truth for:

- protocol behavior
- database/runtime behavior
- performance and stability fixes
- release images consumed by platform packaging repos

## Repository Role

Repository: `https://github.com/satwise/nostr-rs-relay`

Organization: `https://github.com/satwise`

This repo should stay platform-neutral. Umbrel, Debian, and other platform-specific concerns belong in packaging repos.

## Runtime Architecture (Core)

- Rust relay process, websocket entrypoint on port `8080`
- Persistence via SQLite (primary) and optional Postgres paths where supported
- NIP handling implemented at relay layer
- Release artifact built as multi-arch container image

## Build and Release Contract

Container registry:

- `ghcr.io/satwise/nostr-rs-relay`

Tag policy:

- Use stable tags such as `0.9.0-nip50-nip77-r1`.
- Do not overwrite release tags after publication.
- If rebuild is required, increment revision (`r2`, `r3`, ...).

Digest policy:

- Production consumers pin digest (`image:tag@sha256:...`).
- Release notes must include tag, digest, short changelog, and rollback target digest.

## Supported Consumption Paths

- Umbrel packaging repo consumes the same relay image digest.
- Non-Umbrel deployment repo consumes the same relay image digest.

The relay source branch and feature history should not diverge by platform unless absolutely required.

## Support Boundaries

Handled in this core repo:

- protocol correctness
- NIP implementation and behavior
- relay bugs and regressions
- runtime reliability

Not handled in this core repo:

- Umbrel app proxy wiring
- Umbrel app lifecycle scripts
- Debian/systemd bootstrap scripts

## Promotion Flow

1. Merge feature/fix into release branch.
2. Build and publish image.
3. Validate on `linux/arm64` (Pi5) and `linux/amd64`.
4. Promote digest to Umbrel packaging repo.
5. Promote same digest to non-Umbrel deployment repo.

## Rollback Contract

If a release regresses:

1. Re-pin platform repos to last known good digest.
2. Mark bad release as withdrawn in release notes.
3. Keep release tags immutable.

## Cross-Repo Architecture Links

- Umbrel packaging runbook:
  `https://github.com/satwise/umbrel-apps/blob/master/nostr-relay/ARCHITECTURE-RUNBOOK.md`
- Non-Umbrel deployment/app runbook:
  `https://github.com/satwise/nostrrelay/blob/main/ARCHITECTURE-RUNBOOK.md`
