# Zero-Day — Research Results

**Drift-Aware Explainable Anomaly Detection for Behavioral Threat Hunting.**

This repository publishes the **results, methodology and experimental record** for my track in an
undergraduate final-year research project that I lead. The detector source code, trained weights
and data pipeline are **kept private** (team IP and coursework under review).

Every number on this CV-adjacent page is reproducible from the files in this repo.

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

| | |
|---|---|
| Best fusion strategy, mean ROC-AUC across 7 held-out attack families | **0.9634** |
| Held-out Botnet family, after tuning | **0.7200 → 0.9645** (paired t = 18.8) |
| Replication | Second dataset (CTU-13, all 12 capture directions) + 4/4 benign training days |
| Transfer to NF-UNSW-NB15-v2 | zero-shot 0.5816 → **0.8362** by refitting the feature scaler alone |

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