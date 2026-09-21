# From Syscall Traces to Threat Intelligence — supplementary material

This repository holds the per-sample results and the prompt templates behind the
evaluation in the paper *From Syscall Traces to Threat Intelligence: An LLM
Pipeline for Linux Malware Analysis*. It contains no malware samples, no raw
system-call traces, and no source code of the platform.

## Contents

| Path | What it holds | Backs up in the paper |
|---|---|---|
| `results_on_150_samples.xlsx` | Verdicts of Gemini, Mistral and DeepSeek on the 150-sample evaluation set | Table II and the metric bar charts |
| `results_blind_mode_comparison_gemini.xlsx` | Gemini verdicts on 50 samples with and without blind mode, paired per run | Table III |
| `prompts/` | The prompt templates sent to the models, with their own README | Section III-B |

## Verdicts and scoring policies

The platform returns one of three verdicts: `benign`, `suspicious` or
`malicious`. The paper scores them under two policies:

- **Suspect→Malicious**: a `suspicious` verdict counts as a detection.
- **Suspect→Benign**: a `suspicious` verdict counts as a pass.

A malicious sample is a true positive if it is flagged under the chosen policy;
a benign sample is a false positive if it is flagged. Every count in the paper
can be recomputed from the raw verdicts in these files with these two rules.

## `results_on_150_samples.xlsx`

**Sheet `Tests`** — one row per sample, 150 rows.

| Column | Meaning |
|---|---|
| `ID` | `M00`–`M74` for malicious samples, `B00`–`B74` for benign ones |
| `NAME` | Malicious: the label assigned by MalwareBazaar (a family name, a generic detection name, or only a file-type tag). Benign: the program and the command line it was run with |
| `SHA256 / RUN ID` | SHA-256 of the malicious sample. For `M00` and all benign samples this column holds an internal run identifier instead, since these files were not downloaded |
| `TYPE` | Ground-truth label: `MALICIOUS` for every MalwareBazaar sample and for `M00`, `BENIGN` for every locally selected program |
| `PIPELINE RESULT Gemini` / `Mistral` / `Deepseek` | Raw three-way verdict of each model |

**Sheet `Metrics`** — true/false positive and negative counts per model under
both scoring policies, derived from the `Tests` sheet.

**Sheet `Others`** — the same counts broken down by true class, with percentages.

The benign runs in this file were produced in blind mode and the malicious runs
were not; the paper explains this choice and measures its effect (Section IV-E).

## `results_blind_mode_comparison_gemini.xlsx`

Fifty samples — 35 benign, 15 malicious — rerun through Gemini once with blind
mode and once without. Blind mode replaces process names and executable and
library paths with neutral labels; other file paths, addresses and domains are
not redacted. All runs used a limit of 100,000 system calls per trace and
disabled the response cache.

| Sheet | Content |
|---|---|
| `Summary` | Verdict counts, average severity and false-positive rate per condition |
| `Benign` | The 35 benign runs, one row each, with verdict and severity in both conditions and whether the verdict changed |
| `Malicious` | The same for the 15 malicious runs |
| `Raw_Data` | One row per individual run, with mode, verdict and severity |

## `prompts/`

The exact prompt text sent to the models, extracted from the source. The same
prompts are used for every backend; see `prompts/README.md` for when each one is
used and which placeholders it takes.

## Not included

- **Malware samples.** Identified by SHA-256 and obtainable from
  [MalwareBazaar](https://bazaar.abuse.ch/).
- **Raw system-call traces.** Too large to publish.
- **Platform source code.**

## Note on sample `M00`

`M00` is an educational ransomware written by the authors rather than downloaded.
It is the worked example in the paper and has no public hash.