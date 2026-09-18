# LatentAgentComm

> A living survey and evidence map for latent communication between LLM agents

LatentAgentComm tracks how LLM-based agents communicate through continuous internal representations—including embeddings, hidden states, KV caches, modality bridges, and temporary weight updates—instead of relying exclusively on natural-language messages.

This repository is designed as a **research map**, not only a paper list. For each work, we aim to record what crosses the agent boundary, how heterogeneous models are aligned, what information each agent can observe, how the receiver consumes the message, how much the message costs, and what evidence shows that task-relevant information was actually transferred.

> **Current snapshot:** 42 verified references, including 22 core latent-communication methods, 11 systems-level cache-reuse works, 4 safety or causal-audit papers, and 5 surveys or adjacent works. Literature cutoff: **2026-09-18**.

## Why another repository?

[Awesome Latent Communication](https://github.com/enochliu98/Awesome-Latent-Communication) is a useful, manually maintained collection of papers organized primarily around **WHAT** is communicated, **WHICH** representations or layers are aligned, and **HOW** information is fused.

LatentAgentComm is intended to be complementary. Its main goal is to maintain a structured and auditable account of the evidence behind each method.

| Dimension | Awesome-style collection | LatentAgentComm |
|---|---|---|
| Primary purpose | Discover relevant papers | Compare claims, assumptions, and evidence |
| Unit of organization | Paper and mechanism | Paper, experimental condition, and evidence |
| Taxonomy | Carrier, alignment, fusion | Carrier, alignment, context, fusion, topology, optimization, bandwidth, evidence |
| KV-cache work | Collected together | Separated into semantic communication and systems-level reuse |
| Evaluation | Reported headline results | Baselines, payload cost, receiver need, and causal controls |
| Safety | Related topic | Dedicated attack, integrity, privacy, and causal-audit track |
| Metadata | Bibliographic listing | Verified title, authors, year, venue/status, arXiv, code, and update history |

## Scope

We use the following operational definition:

> **Latent inter-agent communication** is a protocol in which a state produced by a sender agent, conditioned on its observation or computation, is delivered to a receiver as a continuous representation and is intended to alter the receiver's task-relevant computation without first being serialized into natural-language text.

### Included

- Probability-weighted embeddings and soft token representations
- Hidden states, activations, and state-delta trajectories
- Semantic KV-cache transfer and learned cache translation
- Shared latent spaces and modality-mediated communication
- Temporary weight-space messages
- Compression and sparse coding of latent messages
- Safety attacks, integrity mechanisms, and causal audits
- Systems work directly relevant to latent-state transport or reuse

### Tracked separately

- **Systems-level reuse:** prefix reuse, cache persistence, cache scheduling, edge handover, and distributed cache transport
- **Single-model latent reasoning:** continuous reasoning that never crosses an agent boundary
- **Internalized multi-agent reasoning:** debate or collaboration distilled into one model

These areas are technically related, but they do not provide the same evidence about agent-to-agent semantic transfer.

## Research questions

LatentAgentComm is organized around the following questions.

1. **What is the message?** Embedding, hidden state, KV cache, visual representation, parameter update, or another carrier?
2. **Who can communicate?** Identical checkpoints, fine-tuned variants, different model sizes, unrelated architectures, tokenizers, languages, or modalities?
3. **How is alignment achieved?** Identity mapping, fixed arithmetic, learned pairwise translator, shared hub, or closed-form alignment?
4. **What does each agent know?** Shared context, asymmetric information, context-unaware transfer, or overlapping-prefix reuse?
5. **How is the message consumed?** Addition, concatenation, continuous prefix, cross-attention, cache restoration, gating, or weight modulation?
6. **Does the receiver use the sender's information?** Does performance depend on the correct example-specific message, or only on the presence of a tensor?
7. **What does communication cost?** Bytes, FLOPs, memory, transfer latency, prefill latency, decode latency, and adapter-training cost?
8. **Is the channel safe?** Can it leak private context, carry adversarial directions, bypass text moderation, or be tampered with in transit?
9. **Does it scale?** What happens with many agents, long relays, changing models, distributed execution, and bidirectional communication?

## Taxonomy

Every core paper should be annotated along the following axes.

| Axis | Example values |
|---|---|
| Carrier | Expected embedding, hidden state, state delta, KV cache, visual hub, weight update |
| Sender–receiver relation | Same checkpoint, fine-tuned variant, same family, cross-family, cross-modal |
| Alignment | None/native, arithmetic, pairwise learned map, shared space, closed-form map |
| Context regime | Shared, asymmetric, context-unaware, overlapping prefix |
| Fusion | Add, concatenate, prepend, cross-attend, restore cache, gate, modulate weights |
| Topology | Pair, relay, debate, hub-and-spoke, shared memory, recursive loop, many-to-many |
| Optimization | Training-free, frozen backbones, adapter training, end-to-end training |
| Compression | Layer/token selection, quantization, pooling, low-rank, sparse coding |
| Evidence | End-task gain, reconstruction, probe, mismatched-message test, private-information test |
| Systems cost | Payload bytes, latency, FLOPs, peak memory, throughput, training/storage cost |
| Safety | Integrity, confidentiality, adversarial robustness, observability |

## The central distinction: communication or reuse?

KV caches have two roles that must not be conflated:

- **Semantic communication:** a sender possesses task-relevant information that the receiver would not otherwise have, and the transferred cache changes the receiver's answer because it contains that information.
- **Systems-level reuse:** the receiver already has the same logical content, while cache transfer avoids recomputing a shared prefix or restores previously computed state.

A speedup establishes useful reuse, but not necessarily semantic communication. Likewise, a performance gain from adding a latent tensor does not establish that the receiver used the correct sender's content.

## Evidence levels

We use an evidence ladder to make claims comparable.

| Level | Evidence |
|---|---|
| E0 | Interface proposed; no downstream communication test |
| E1 | Downstream gain over no communication |
| E2 | Gain over a stated text-communication baseline |
| E3 | Matched efficiency and payload accounting |
| E4 | Correct message beats zero and random-message controls |
| E5 | Correct message beats a mismatched-example message |
| E6 | Transfer remains when the receiver lacks sender-private information except through the channel |
| E7 | Independent reproduction across model families or implementations |

The key causal comparison is usually:

```text
correct message
  vs. mismatched-example message
  vs. moment-matched random message
  vs. zero message
  vs. self-generated message
```

## Main research directions

### 1. Causal validity of latent communication

Develop benchmarks where the receiver genuinely needs sender-private information. Measure content-attributable gain with mismatched-message, random-message, zero-message, and self-message interventions.

### 2. Heterogeneous model alignment

Study communication across unrelated model families, sizes, layers, KV geometries, tokenizers, languages, and modalities. Compare pairwise translators, shared latent hubs, vocabulary anchors, and training-free mappings.

### 3. Bandwidth-aware communication

Report transmitted bytes rather than token counts alone. Explore adaptive layer and token selection, quantization, low-rank summaries, sparse codes, and rate–accuracy trade-offs.

### 4. Communication–systems co-design

Jointly study semantic message design with serialization, transport, cache scheduling, placement, prefetching, and distributed execution—while keeping semantic transfer distinct from prefix reuse.

### 5. Multi-agent topology and scaling

Move beyond one sender and one receiver. Evaluate relays, debate, shared memory, hub-and-spoke systems, recursive collaboration, and many-to-many channels as the number of agents grows.

### 6. Safety, integrity, and privacy

Investigate latent prompt injection, adversarial steering, hidden collusion, information leakage, message tampering, model-version mismatch, authentication, and receiver-side validation.

### 7. Interpretability and emergent protocols

Determine what information is encoded, whether the receiver causally uses it, and whether jointly trained agents develop effective but opaque communication codes.

### 8. Reproducible evaluation

Build a common benchmark matrix with matched text baselines, direct-input oracles, public checkpoints, standardized payload accounting, repeated runs, and explicit venue/version metadata.

## Corpus organization

The repository is planned around a machine-readable literature database rather than a single Markdown list.

```text
LatentAgentComm/
├── README.md
├── survey/
│   ├── latent_communication_survey.tex
│   └── references.bib
├── data/
│   ├── papers.csv
│   ├── papers.yaml
│   └── schema.md
├── docs/
│   ├── taxonomy.md
│   ├── inclusion-criteria.md
│   ├── evaluation-protocol.md
│   └── research-agenda.md
├── figures/
├── scripts/
│   └── validate_metadata.py
└── CONTRIBUTING.md
```

Each paper record should eventually contain:

```yaml
id: stable-paper-id
title: Full paper title
authors: []
year: 2026
status: arXiv | accepted | published | under-review
venue: null
arxiv: null
code: null
category: semantic | systems-reuse | safety-audit | adjacent
carrier: []
alignment: []
context_regime: []
fusion: []
topology: []
training: []
payload_reported: false
causal_controls: []
models: []
tasks: []
notes: ""
last_verified: YYYY-MM-DD
```

## Current corpus

| Track | Papers | Purpose |
|---|---:|---|
| Core latent communication | 22 | Methods that claim sender-to-receiver semantic transfer |
| Systems-level reuse | 11 | Cache reuse, persistence, scheduling, and transport |
| Safety and causal audit | 4 | Attacks, integrity, and evidence attribution |
| Surveys and adjacent work | 5 | Broader communication and latent-reasoning context |
| **Total** | **42** | Verified references in the initial survey draft |

## Paper index

Titles link to a primary source. We prefer official proceedings pages when they are available and otherwise link to arXiv. Venue labels are used only when acceptance or publication has been verified; “under review” is not treated as an accepted venue.

### Core latent-communication methods

| Year | Status | Paper | Main carrier or contribution |
|---:|---|---|---|
| 2024 | ICLR | [Let Models Speak Ciphers: Multiagent Debate through Embeddings](https://arxiv.org/abs/2310.06272) | Probability-weighted output embeddings; CIPHER |
| 2025 | ICML | [Communicating Activations Between Language Model Agents](https://proceedings.mlr.press/v267/ramesh25a.html) | Intermediate activations |
| 2025 | EMNLP | [Augmenting Multi-Agent Communication with State Delta Trajectory](https://aclanthology.org/2025.emnlp-main.518/) | Hidden-state deltas plus text; SDE |
| 2025 | arXiv | [Mixture of Thoughts: Learning to Aggregate What Experts Think, Not Just What They Say](https://arxiv.org/abs/2509.21164) | Cross-attention over heterogeneous expert states |
| 2025 | NeurIPS Spotlight | [Thought Communication in Multiagent Collaboration](https://arxiv.org/abs/2510.20733) | Identifiable shared and private latent thoughts |
| 2026 | ACL | [Enabling Agents to Communicate Entirely in Latent Space](https://aclanthology.org/2026.acl-long.1248/) | Hidden-state prefixes; Interlat |
| 2026 | ICML Spotlight | [Latent Collaboration in Multi-Agent Systems](https://arxiv.org/abs/2511.20639) | Latent thoughts and shared KV working memory; LatentMAS |
| 2026 | ICLR | [KVComm: Enabling Efficient LLM Communication through Selective KV Sharing](https://arxiv.org/abs/2510.03346) | Selective KV-layer sharing |
| 2026 | ICLR | [Cache-to-Cache: Direct Semantic Communication Between Large Language Models](https://arxiv.org/abs/2510.03215) | Learned cross-model KV translation; C2C |
| 2026 | arXiv | [Latent Space Communication via K-V Cache Alignment](https://arxiv.org/abs/2601.06123) | Shared KV latent space; LatentK-V |
| 2026 | arXiv | [Agent Primitives: Reusable Latent Building Blocks for Multi-Agent Systems](https://arxiv.org/abs/2602.03695) | KV-based reusable agent primitives |
| 2026 | arXiv; under review | [Learning to Communicate: Toward End-to-End Optimization of Multi-Agent Language Systems](https://arxiv.org/abs/2604.21794) | End-to-end latent trajectory optimization; DiffMAS |
| 2026 | arXiv | [Recursive Multi-Agent Systems](https://arxiv.org/abs/2604.25917) | Recursive latent communication; RecursiveMAS |
| 2026 | arXiv; work in progress | [The Vision Wormhole: Latent-Space Communication in Heterogeneous Multi-Agent Systems](https://arxiv.org/abs/2602.15382) | Visual interface as a shared latent hub |
| 2026 | ICML | [Latent Cache Flow: Model-to-Model Communication Without Text](https://arxiv.org/abs/2605.22863) | Compressed and cross-context KV transfer; LCF/LCF-X |
| 2026 | arXiv | [See What I See, Know What I Think: Dense Latent Communication Across Heterogeneous Agents](https://arxiv.org/abs/2606.13594) | Dense cross-model cache alignment |
| 2026 | arXiv | [When Less Latent Leads to Better Relay: Information-Preserving Compression for Latent Multi-Agent LLM Collaboration](https://arxiv.org/abs/2604.13349) | Orthogonal BackFill cache compression; OBF |
| 2026 | arXiv | [Good Agentic Friends Do Not Just Give Verbal Advice: They Can Update Your Weights](https://arxiv.org/abs/2605.13839) | Temporary LoRA weight-space messages; TFlow |
| 2026 | arXiv | [Latent Communication Between Language Model Agents: Channels, Alignment, and the Limits of Text](https://arxiv.org/abs/2607.14103) | Dense/sparse channels and cross-architecture alignment |
| 2026 | COLM | [StateBridge: Training-free Hidden-state Alignment for Latent Communication in LLM Multi-Agent Systems](https://arxiv.org/abs/2608.13317) | Closed-form hidden-state alignment |
| 2026 | arXiv | [Dual-Cache Latent Space Communication between Heterogeneous Language Models](https://arxiv.org/abs/2608.20617) | Dual-cache heterogeneous translation; XKV |
| 2026 | arXiv | [Post-Hoc Sparse Coding of Latent Communication Between Vision-Language Model Agents](https://arxiv.org/abs/2608.10198) | Sparse coding of latent visual messages |

### Systems-level cache reuse and transport

These papers are important for deployment, but their primary contribution is cache reuse, storage, scheduling, or transport rather than demonstrated sender-private semantic transfer.

| Year | Status | Paper | Primary systems contribution |
|---:|---|---|---|
| 2026 | NSDI | [DroidSpeak: KV Cache Sharing Across Fine-tuned Model Variants](https://www.usenix.org/conference/nsdi26/presentation/liu-yuhan) | Cross-model prefix-cache reuse |
| 2025 | NeurIPS | [KVCOMM: Online Cross-context KV-cache Communication for Efficient LLM-based Multi-agent Systems](https://arxiv.org/abs/2510.12872) | Anchor-based cache-offset correction |
| 2026 | ICML | [LRAgent: Efficient KV Cache Sharing for Multi-LoRA LLM Agents](https://arxiv.org/abs/2602.01053) | Shared-base and low-rank adapter caches |
| 2026 | arXiv; under review | [TokenDance: Scaling Multi-Agent LLM Serving via Collective KV Cache Sharing](https://arxiv.org/abs/2604.03143) | Collective cache reuse and diff-aware storage |
| 2026 | arXiv | [RelayCaching: Accelerating LLM Collaboration via Decoding KV Cache Reuse](https://arxiv.org/abs/2603.13289) | Reuse upstream decoding caches during downstream prefill |
| 2026 | arXiv | [Agent Memory Below the Prompt: Persistent Q4 KV Cache for Multi-Agent LLM Inference on Edge Devices](https://arxiv.org/abs/2603.04428) | Persistent quantized agent memory |
| 2026 | arXiv | [QKVShare: Quantized KV-Cache Handoff for Multi-Agent On-Device LLMs](https://arxiv.org/abs/2605.03884) | Mixed-precision KV handoff |
| 2026 | arXiv | [AAFLOW+: Stateful Operator Abstraction with Zero-Copy Distributed KV Cache Orchestration for Multi-Agent Workflows](https://arxiv.org/abs/2607.10987) | Distributed zero-copy KV orchestration |
| 2026 | arXiv | [Learning Agent Execution for KV-Cache Management in Agentic Serving](https://arxiv.org/abs/2608.14624) | Agent-aware cache management; CacheScout |
| 2026 | arXiv | [Low-Latency Edge LLM Handover via Joint KV Cache Transfer and Token Prefill](https://arxiv.org/abs/2603.28018) | Edge handover and prefill scheduling |
| 2026 | arXiv | [ImpactHO: Importance-Aware KV Cache Transfer for Multi-User Edge LLM Handover](https://arxiv.org/abs/2608.10545) | Importance-aware partial-cache transfer |

### Safety, integrity, and causal audit

| Year | Status | Paper | Main question |
|---:|---|---|---|
| 2026 | arXiv | [When Latent Agents Lie: KV-Cache Integrity in Multi-Agent LLM Collaboration](https://arxiv.org/abs/2606.28958) | Can a plausible visible message conceal a tampered KV payload? |
| 2026 | EMNLP | [Out of Sight, Not Out of Mind: Unveiling Latent Attack in Latent-based Multi-Agent Systems](https://arxiv.org/abs/2605.28214) | Can attack-associated directions propagate through latent handoffs? |
| 2026 | arXiv | [Do Latent Channels Actually Communicate? A Causal Audit of Latent Multi-Agent LLM](https://arxiv.org/abs/2607.26773) | Does the receiver use example-specific sender content? |
| 2026 | arXiv | [When Does Latent Communication Pay? A Causal Audit of Relayed KV Caches in Multi-Agent LLMs](https://arxiv.org/abs/2608.04893) | When does correct cache pairing matter? |

### Surveys and adjacent work

| Year | Status | Paper | Relation to this repository |
|---:|---|---|---|
| 2026 | arXiv | [Beyond tokens: a unified framework for latent communication in LLM-based multi-agent systems](https://arxiv.org/abs/2606.05711) | Closest dedicated latent-communication survey |
| 2026 | TMLR | [The Five Ws of Multi-Agent Communication: Who Talks to Whom, When, What, and Why](https://arxiv.org/abs/2602.11583) | Broader survey spanning MARL, emergent language, and LLM agents |
| 2025 | arXiv | [A Survey on Latent Reasoning](https://arxiv.org/abs/2507.06203) | Survey of within-model latent reasoning |
| 2025 | COLM | [Training Large Language Models to Reason in a Continuous Latent Space](https://arxiv.org/abs/2412.06769) | Coconut; single-model continuous reasoning substrate |
| 2026 | ACL | [Latent Agents: A Post-Training Procedure for Internalized Multi-Agent Debate](https://aclanthology.org/2026.acl-long.709/) | Multi-agent debate internalized into one model |

## Contribution policy

Contributions are welcome. A paper submission should include:

- Exact title, complete author list, year, and primary-source URL
- Venue status: published, accepted, arXiv, or under review
- Code and model checkpoints, if available
- Proposed category and taxonomy annotations
- Context visible to the sender and receiver
- Reported communication payload and efficiency measurements
- Baselines and causal controls used in the paper
- A short note explaining why the work is semantic communication, systems reuse, safety/audit, or adjacent

Please do not infer acceptance from phrases such as “submitted to” or “under review at.” Prefer official proceedings pages when available; otherwise link to the primary arXiv or OpenReview record.

## Related resources

- [Awesome Latent Communication](https://github.com/enochliu98/Awesome-Latent-Communication) — a complementary curated paper collection and WHAT/WHICH/HOW taxonomy
- [Beyond tokens: a unified framework for latent communication in LLM-based multi-agent systems](https://arxiv.org/abs/2606.05711) — the closest dedicated survey
- [The Five Ws of Multi-Agent Communication](https://arxiv.org/abs/2602.11583) — a broader survey spanning MARL, emergent language, and LLM agents
- [A Survey on Latent Reasoning](https://arxiv.org/abs/2507.06203) — adjacent work on within-model continuous reasoning

## Roadmap

- [x] Verify the initial 42-paper bibliography
- [x] Draft the first survey manuscript
- [x] Separate semantic communication from systems-level reuse
- [x] Add a safety and causal-audit track
- [ ] Publish the machine-readable paper matrix
- [ ] Add paper-submission and correction issue templates
- [ ] Add automated metadata and duplicate checks
- [ ] Release benchmark task definitions and causal-control templates
- [ ] Track reproduction status and implementation compatibility
- [ ] Add visual maps of methods, model pairs, and evidence levels

## Citation

If this repository is useful in your research, please cite the accompanying survey. A finalized BibTeX entry will be added after the survey authors and public release information are confirmed.

## Contact

Questions, corrections, missing papers, and disagreement about classifications are welcome through GitHub Issues. Classification changes should be supported by the paper's experimental setup or primary-source metadata.
