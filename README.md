# Gustavo Schneiter

I build AI infrastructure in Rust — the systems layer that makes LLM agents fast, cheap, and verifiable.

Founder of **HuGR** (Human Guardrail): a container runtime that makes redundant work cease to exist, a forge that records which model wrote what and at what cost, and a content-addressed cache platform underneath it all.

## Building

**[lightr](https://github.com/HumanGuardrail/hugr-lightr)** — daemonless, CAS-native container runtime.
Cold-starts a fully isolated rootless container in **30.8 ms** (4× faster than rootless podman at identical isolation). Memoized runs: a 20k-line C rebuild replays in **10.3 ms vs Docker's 20.6 s (~2,000×)**. Hydrates 1 GB of workspace in **322 ms vs 38.4 s (119×)**. Single ~5 MB binary vs Docker.app's 1.96 GB. Linux namespaces + macOS microVMs (Virtualization.framework). Every number is CI-signed with a reproduction path — [benchmarks](https://github.com/HumanGuardrail/hugr-lightr/blob/main/docs/benchmarks/RESULTS.md).

**[lightr-cri](https://github.com/HumanGuardrail/lightr-cri)** — stateless, crash-only Kubernetes CRI for lightr.
critest-conformant. No reconciliation loop: kernel + disk are the source of truth, so `kill -9` mid-operation loses nothing. **~7 MB resident vs containerd's ~66 MB**, no per-container shim.

**CoreLink** *(closed source)* — the platform under all of it.
Multi-tenant content-addressed cache (REAPI v2) on the Cloudflare edge — Bazel, Cargo, npm, pip, OCI. Per-tenant BYOK, TLA+-modelled tenant isolation (the invariant breaks CI on regression), Ed25519-signed append-only Merkle audit chain. Client: [corelink-cli](https://github.com/humangr-labs/corelink-cli) · [docs](https://corelink-docs.humangr.com) · [pilot](https://signup.corelink.humangr.com/pilot).

**hugit** *(closed beta)* — a git-compatible forge for agent fleets.
Event-sourced refs make force-push data loss structurally unexpressible (`undo` is a compensating event, not a hack). Memoized CI never re-tests a tree it has already verified. Every commit carries model, instruction, and cost attestation — SLSA-class provenance at the model layer.

## How I work

- **Measured claims only.** A performance number exists when CI signs it on named hardware with a reproduction path — otherwise it's a target, and it's labeled as one.
- **No daemons.** Nothing runs when nothing runs; `ps` proves it.
- **Facts over verdicts.** Tools that feed LLMs should hand them ground truth, not confidence scores — a confidently wrong verdict makes a strong model stop investigating.
- **Crash-only.** State lives in the kernel and on disk; recovery is a restart, not a reconciliation.

## Contact

[gustavo@humangr.com](mailto:gustavo@humangr.com) · [@corelinkdev](https://twitter.com/corelinkdev)
