# Zero-Day — Research Results

**Drift-Aware Explainable Anomaly Detection for Behavioral Threat Hunting.**

This repository publishes the **results, methodology and experimental record** for my track in an
undergraduate final-year research project that I lead. The detector source code, trained weights
and data pipeline are **kept private** (team IP and coursework under review).

Every headline number on this page is reproducible from the files here, and where a number is
not yet covered by a card it links to the specific experiment page that backs it.

**Scope note.** The numbers in the table below come from two places, and the distinction matters:
the per-family ablation figures are from the `main` branch, while the **Botnet k=30 result and its
CTU-13 replication come from an experiment branch not yet merged**. Those are published here in
[`k30/`](k30/) so the claims can be checked rather than taken on trust.

---

## What the project does

A network intrusion detector for **zero-day** attacks — attacks that have no known signature
and no label at training time.

The core design decision: **a graph autoencoder over network-flow graphs, not a classifier.**
A supervised classifier can only ever recognise attack families present in its training labels.
A zero-day has no label by definition, so a classifier is structurally incapable of flagging one.
An autoencoder learns only "normal" and fires on deviation, so it can alert on a family that did
not exist when it was trained. Choosing the autoencoder also keeps the ablation controlled —
M5a scores by reconstruction error and M5b scores by reconstruction error, so the comparison
varies exactly one thing: per-flow versus relational.

## Headline result

| | | Source |
|---|---|---|
| Best fusion strategy, mean ROC-AUC across 7 held-out attack families (seed 0) | **0.9634** | [`ablation_table.md`](ablation_table.md) |
| Held-out Botnet family at k=30 | **0.7200 → 0.9645** (paired t = 18.8) | [`k30/E92.html`](k30/E92.html) |
| Botnet replication, second dataset (CTU-13) | all 12 capture directions | [`k30/E94.html`](k30/E94.html), [`k30/E95.html`](k30/E95.html) |
| Botnet replication across benign training days | 4/4 days | [`k30/E93.html`](k30/E93.html) |
| Transfer to NF-UNSW-NB15-v2 | zero-shot 0.5816 → **0.8362** by refitting the feature scaler alone | [`report_cards.md`](report_cards.md) |

**On seed variance.** The 0.9634 above is a single-seed figure (seed 0) and is labelled as such.
The multi-seed figures are higher and carry bands: 0.9558 ± 0.0044 (RC-21) and 0.9764 ± 0.0041
([`papers_faceoff.md`](papers_faceoff.md)). The Botnet k=30 result is 3 seeds with paired t = 18.8.
Single-draw and banded numbers are not interchangeable, and the ablation table says which is which.

See [`ablation_table.md`](ablation_table.md) for the per-family breakdown and
[`papers_faceoff.md`](papers_faceoff.md) for the comparison against published unsupervised IDS work.

## Benchmarked against published work

Compared against PIKACHU, AutoGraphAD, Anomal-E, EULER and VGRNN
([`papers_faceoff.md`](papers_faceoff.md)).

**Three published bars were cleared. Two were not.** The two that were not cleared are recorded
as documented negative results rather than quietly dropped — including one where the approach
lost to AutoGraphAD by 0.669 at connection granularity.

## Methodology notes

- **Strictly held-out attack families.** Trained on benign-only traffic; every attack family
  evaluated was unseen during training.
- **Multi-seed retrain bands.** Reported numbers carry a seed-variance band, not a single draw.
- **Documented negatives.** Failed experiments are recorded with the reason they failed.
  See [`CHANGELOG.md`](CHANGELOG.md) — an append-only dated lab notebook.
- **Evaluation guards.** Six separate experiments produced invalid numbers through incorrect
  checkpoint/data/scaler pairing. Each was caught only by a number not matching a previously
  known number, which is a luck-based defence. The failure mode was then made explicit and
  enforced in unit-tested guards rather than left to intuition.

## Files

| File | Contents |
|---|---|
| [`ablation_table.md`](ablation_table.md) | Per-family ROC-AUC and P@100 across fusion strategies |
| [`papers_faceoff.md`](papers_faceoff.md) | Benchmark against 31 published papers — wins and losses |
| [`report_cards.md`](report_cards.md) | 27 experiment cards for this track, one per run |
| [`CHANGELOG.md`](CHANGELOG.md) | Append-only lab notebook for this track: what was tried, what worked, what didn't |
| [`attack_mapper.json`](attack_mapper.json) | Anomaly → MITRE ATT&CK technique mapping |
| [`SHAP_COMMANDS.md`](SHAP_COMMANDS.md) | Explainability workflow |
| [`k30/`](k30/) | Experiment pages for the Botnet k=30 work and its CTU-13 / cross-day replications (E90–E95) |
| `*.schema.json` | Feature-vector and scored-alert schemas |

## Team and scope

A 4-person final-year project that I lead. The team splits into four tracks:

| Track | Owner | In this repo |
|---|---|---|
| **Detection modelling** (GNN detector, evaluation harness, experimental record) | **Me** | Yes |
| Data & capture (flow extraction, schemas, eBPF syscall capture) | Saharsh | No |
| Trust / UEBA (synthetic identity events, host features) | Aditya Tiwari | No |
| Adversarial evasion harness (red-team techniques) | Avinash | No |

**This repository publishes my track only.** Everything here is the detection-modelling work:
the graph autoencoder, the evaluation protocol, the ablation, the benchmark against published
IDS work, and the dated experiment log for that track. Teammate-authored work has been removed
rather than re-labelled, and stays in the private project repo.

The detector source, trained weights and data pipeline also remain private.

## Contact

Avradeep Majumder — [GitHub](https://github.com/DeepxD-code) · [LinkedIn](https://linkedin.com/in/avradeep-majumder-4664b532a)