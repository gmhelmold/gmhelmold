# Gustavo Schneiter

I build AI infrastructure in Rust — the systems layer that makes LLM agents fast, cheap, and verifiable.

Founder of **HuGR** (Human Guardrail): a content-addressed cache platform, the compute and container runtime that run on it, and a git-native forge for agent fleets — so identical work is never repeated and every artifact is addressed by what it is.

**[Site + writing →](https://humanguardrail.github.io/)**

## In production

**CoreLink** *(live · closed source)* — the content-addressed platform under all of it.
Multi-tenant cache + action cache (REAPI v2) on Cloudflare — Bazel, Turborepo, sccache/cargo, plus npm, pip, brew and OCI cache surfaces. Per-tenant BYOK across four KMS providers, tenant isolation model-checked in TLA+ (the invariant breaks CI on regression), and an offline-verifiable, Ed25519-signed **hash-chained** audit log a customer verifies without trusting my word for it. Design write-up: [Public packages only need to be downloaded once](https://humanguardrail.github.io/writing/public-mirror.html). Client: [corelink-cli](https://github.com/humangr-labs/corelink-cli) · [docs](https://corelink-docs.humangr.com).

## Open source

**[lightr](https://github.com/HumanGuardrail/hugr-lightr)** — daemonless, CAS-native container runtime.
Cold-starts a fully isolated rootless container in **30.8 ms** (4× rootless podman at identical isolation). Memoized runs: a 20k-line C rebuild replays in **10.3 ms vs Docker's 20.6 s (~2,000×)**. Hydrates 1 GB of workspace in **322 ms vs 38.4 s (119×)**. Linux namespaces + macOS microVMs (Virtualization.framework). Every number is CI-signed with a reproduction path — [benchmarks](https://github.com/HumanGuardrail/hugr-lightr/blob/main/docs/benchmarks/RESULTS.md), and a [truth ledger](https://github.com/HumanGuardrail/hugr-lightr/blob/main/docs/spec/parity-audit.md) that records the three times my own audit made those numbers worse.

**[lightr-cri](https://github.com/HumanGuardrail/lightr-cri)** — stateless, crash-only Kubernetes CRI for lightr.
critest-conformant. No reconciliation loop: kernel + disk are the source of truth, so `kill -9` mid-operation loses nothing. **7.1 MB resident vs containerd's 65.9 MB**, no per-container shim.

**[datarail](https://github.com/HumanGuardrail/HuGR_Datarail)** — an end-to-end-sealed data rail; a Kafka-wire-compatible broker whose storage never sees plaintext.
Every record sealed into a per-record vault (X25519 key-wrap + AEAD, signed); delivery carries an offline-verifiable Merkle receipt; an unmodified Kafka client can produce and read back. An author-commissioned adversarial audit of the real broker measured 3.0 ms cold start and 3.3 MB idle RSS through a 50,000-message zero-loss cycle, with the data directory grepped clean of plaintext. The README states loudly what a single-node prototype does and doesn't prove.

## Products

**hugit** *(free · closed source)* — a git-compatible forge for agent fleets.
Event-sourced refs make force-push data loss structurally unexpressible (`undo` is a compensating event, not a hack). Memoized CI never re-tests a tree it has already verified. Every commit carries model, instruction, and cost attestation — SLSA-class provenance at the model layer.

## Writing

**[Anchored Stability](https://github.com/HumanGuardrail/anchored-stability)** — garbage-collecting Byzantine, open-membership CRDTs without a quorum.
A self-published concept paper with a runnable artifact: every replica *recomputes* the causal-stability certificate from evidence it verifies itself, so Byzantine writers are detected and fenced, not out-voted — no quorum on the stability path. *Checked, not proven* (a bounded model-checker + an independent TLA+/TLC model over 2,299,968 states, not an unbounded proof), scoped to LWW registers. I found three convergence bugs attacking my own claim; each ships a runnable reproduction, and every number regenerates from a clean clone. [DOI](https://doi.org/10.5281/zenodo.21209725).

## How I build

I run fleets of coding agents under tight gates — judgment centralized, every result verified rather than trusted. The method is **working backwards** from a frozen definition of done, **spec-driven development**, and **zero-decision briefs**: work sliced into atomic, conflict-free packages with interfaces frozen upfront, so agents transcribe decisions instead of making them. Before an agent is spawned, its definition of done is compiled into an ordered chain of gates and bound to it by a token carried in its own brief. A runtime hook intercepts the moment the agent tries to declare itself done, recovers the token from its own transcript, and re-runs the real checks — compilers, tests — live on the machine. An agent can *say* it finished, but it can never finish on its own word: the spec and its verification are the same object. One orchestrator drives 20+ agents across different models in parallel this way.

## How I work

- **Measured claims only.** A performance number exists when CI signs it on named hardware with a reproduction path — otherwise it's a target, and it's labeled as one.
- **Attack my own claims first.** The strongest thing in my repos is a ledger of the bugs I found by auditing work I'd already called validated.
- **No daemons.** Nothing runs when nothing runs; `ps` proves it.
- **Facts over verdicts.** Tools that feed LLMs should hand them ground truth, not confidence scores — a confidently wrong verdict makes a strong model stop investigating.
- **Crash-only.** State lives in the kernel and on disk; recovery is a restart, not a reconciliation.

## Contact

[gustavo@humangr.com](mailto:gustavo@humangr.com) · [Site](https://humanguardrail.github.io/) · [@corelinkdev](https://twitter.com/corelinkdev)
