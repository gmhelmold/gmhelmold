# Gustavo Schneiter

I build AI infrastructure in Rust — the systems layer that makes LLM agents fast, cheap, and verifiable.

Founder of **HuGR** (Human Guardrail): a content-addressed cache platform, the compute and container runtime that run on it, and a git-native forge for agent fleets — so identical work is never repeated and every artifact is addressed by what it is.

**[Site + writing →](https://humanguardrail.github.io/)**

## Building

**[lightr](https://github.com/HumanGuardrail/hugr-lightr)** — daemonless, CAS-native container runtime.
Cold-starts a fully isolated rootless container in **30.8 ms** (4× rootless podman at identical isolation). Memoized runs: a 20k-line C rebuild replays in **10.3 ms vs Docker's 20.6 s (~2,000×)**. Hydrates 1 GB of workspace in **322 ms vs 38.4 s (119×)**. Linux namespaces + macOS microVMs (Virtualization.framework). Every number is CI-signed with a reproduction path — [benchmarks](https://github.com/HumanGuardrail/hugr-lightr/blob/main/docs/benchmarks/RESULTS.md), and a [truth ledger](https://github.com/HumanGuardrail/hugr-lightr/blob/main/docs/spec/parity-audit.md) that records the three times my own audit made those numbers worse.

**[lightr-cri](https://github.com/HumanGuardrail/lightr-cri)** — stateless, crash-only Kubernetes CRI for lightr.
critest-conformant. No reconciliation loop: kernel + disk are the source of truth, so `kill -9` mid-operation loses nothing. **7.1 MB resident vs containerd's 65.9 MB**, no per-container shim.

**[datarail](https://github.com/HumanGuardrail/HuGR_Datarail)** — an end-to-end-sealed data rail; a Kafka-wire-compatible broker whose storage never sees plaintext.
Every record sealed into a per-record vault (X25519 key-wrap + AEAD, signed); delivery carries an offline-verifiable Merkle receipt; an unmodified Kafka client can produce and read back. An author-commissioned adversarial audit of the real broker measured 3.0 ms cold start and 3.3 MB idle RSS through a 50,000-message zero-loss cycle, with the data directory grepped clean of plaintext. The README states loudly what a single-node prototype does and doesn't prove.

**CoreLink** *(closed source)* — the content-addressed platform under all of it.
Multi-tenant cache + action cache (REAPI v2) on Cloudflare — Bazel, Turborepo, sccache/cargo, plus npm, pip, brew and OCI cache surfaces. Per-tenant BYOK across four KMS providers, tenant isolation model-checked in TLA+ (the invariant breaks CI on regression), and an offline-verifiable, Ed25519-signed **hash-chained** audit log a customer verifies without trusting my word for it. Design write-up: [Public packages only need to be downloaded once](https://humanguardrail.github.io/writing/public-mirror.html). Client: [corelink-cli](https://github.com/humangr-labs/corelink-cli) · [docs](https://corelink-docs.humangr.com).

**hugit** *(free product, closed source)* — a git-compatible forge for agent fleets.
Event-sourced refs make force-push data loss structurally unexpressible (`undo` is a compensating event, not a hack). Memoized CI never re-tests a tree it has already verified. Every commit carries model, instruction, and cost attestation — SLSA-class provenance at the model layer.

## How I work

- **Measured claims only.** A performance number exists when CI signs it on named hardware with a reproduction path — otherwise it's a target, and it's labeled as one.
- **Attack my own claims first.** The strongest thing in my repos is a ledger of the bugs I found by auditing work I'd already called validated.
- **No daemons.** Nothing runs when nothing runs; `ps` proves it.
- **Facts over verdicts.** Tools that feed LLMs should hand them ground truth, not confidence scores — a confidently wrong verdict makes a strong model stop investigating.
- **Crash-only.** State lives in the kernel and on disk; recovery is a restart, not a reconciliation.

## Contact

[gustavo@humangr.com](mailto:gustavo@humangr.com) · [Site](https://humanguardrail.github.io/) · [@corelinkdev](https://twitter.com/corelinkdev)
