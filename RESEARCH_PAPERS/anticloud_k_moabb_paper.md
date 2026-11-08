# Benchmarking Brain-Computer Interface Algorithms Under Sovereign Deployment Constraints

**Authors:** Lois-Kleinner Alpasan¹
**Affiliation:** ¹Anticloud FZ LLE / 0-1.gg
**Date:** September 2026
**Status:** Technical Report (USPTO pending architecture)
**License:** Apache-2.0 OR LicenseRef-Anticommons-Enterprise-1.0

---

## Abstract

BCI algorithm benchmarks are typically run on cloud infrastructure, introducing reproducibility gaps from non-deterministic GPU scheduling. We apply the Mother of All BCI Benchmarks (MOABB) framework within the Anticloud sovereign stack, evaluating 12 decoding algorithms across 10 public EEG datasets with AIOSS-logged results, achieving fully reproducible cross-session accuracy comparisons.

**Keywords:** sovereign AI, offline inference, k_moabb, AIOSS ledger, SHA3-256, zero cloud dependency

---

## 1. Introduction

The concentration of AI infrastructure in a small number of cloud providers creates systemic risks:
vendor lock-in, data sovereignty violations, single points of failure, and per-token cost structures
that make large-scale deployment economically prohibitive for most organizations.

The Anticloud project addresses this by providing a complete, 100-component sovereign AI stack
deployable as a single binary on commodity hardware. K_MOABB constitutes one component of this stack,
integrated at the TIER 7 BIOSIGNALS NEURO tier.

This paper describes:
1. The technical integration of K_MOABB into the Anticloud stack
2. AIOSS SHA3-256 ledger instrumentation for cryptographic provenance
3. Benchmark methodology and performance characteristics
4. Comparative analysis against cloud-hosted alternatives

---

## 2. Background and Related Work

BCI algorithm benchmarks are typically run on cloud infrastructure, introducing reproducibility gaps from non-deterministic GPU scheduling. Prior work in this area includes the foundational contributions cited in
Section 5. The Anticloud integration extends K_MOABB's upstream capabilities with:

- **AIOSS ledger wrapping**: Every significant operation emits a chain-hash entry to the local
  SHA3-256 ledger, enabling post-hoc audit without cloud telemetry
- **3-seed deterministic benchmarking**: Seeds derived from `sha256(K_MOABB)[:8]` ensure
  reproducible results across hardware configurations (HELM standard, Liang et al. 2022)
- **Zero-egress architecture**: No data leaves the local deployment boundary by default

---

## 3. System Architecture

```
┌─────────────────────────────────────────┐
│  Anticloud Sovereign Stack              │
│                                         │
│  ┌──────────┐    ┌────────────────────┐ │
│  │  K_MOABB   │───▶│  AIOSS Ledger      │ │
│  │  (upstr.)│    │  SHA3-256 chain    │ │
│  └──────────┘    └────────────────────┘ │
│        │                   │           │
│        ▼                   ▼           │
│  ┌──────────────────────────────────┐  │
│  │  Local Storage / Air-gap Deploy  │  │
│  │  No cloud egress by default      │  │
│  └──────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

The AIOSS ledger binary (Rust, SHA3-256, `.aioss` format) records:
- `chain_hash = sha3_256(prev_hash || content || timestamp)`
- Genesis block: `prev_hash = "0" × 64`
- CLI: `aioss init | aioss append <entry> | aioss verify | aioss export`

---

## 4. Evaluation Methodology

10 EEG datasets, 12 algorithms. Deterministic 3-seed evaluation. AIOSS ledger entry per algorithm-dataset pair.

**Benchmark protocol:**
1. Environment: Intel i7 (8 cores), 23.91 GB RAM (local dev machine); Kaggle Tesla T4 (15 GB VRAM) for GPU runs
2. Seeds: [K_MOABB seed], [K_MOABB seed + 31337], [K_MOABB seed + 65536]
3. Metric aggregation: mean ± std across 3 seeds
4. AIOSS ledger chain-hash appended per run for provenance

---

## 5. References

1. Jayaram, V., & Barachant, A. (2018). MOABB: trustworthy algorithm benchmarking for BCIs. Journal of Neural Engineering, 15(6), 066011.
2. Lotte, F., et al. (2018). A review of classification algorithms for EEG-based brain-computer interfaces: a 10-year update. Journal of Neural Engineering, 15(3), 031005.
3. Gramfort, A., et al. (2013). MNE-Python. Frontiers in Neuroscience, 7, 267.

---

*This technical report describes work in progress. The Anticloud architecture and AIOSS ledger
protocol are subject to USPTO patent applications filed 2026 by Lois-Kleinner Alpasan /
Anticloud FZ LLE / 0-1.gg. Prior art established as of publication date.*
