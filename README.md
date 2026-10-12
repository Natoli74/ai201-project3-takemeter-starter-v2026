# TakeMeter — Unit 5 Submission

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.**

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

TakeMeter is a text classifier for posts from the r/csMajors community. It
sorts each post into one of three categories: `analysis`, `hot_take`, or
`reaction`.

The dataset is stored in `labels.csv`. It contains 191 posts: 20 were labeled
by hand first, and 171 were pre-labeled with AI and then audited manually.

---

## Label Taxonomy

### `analysis`

**Definition:** Makes a structured, data-backed argument regarding career
strategy, tech stacks, internships, or coursework. It must cite a specific
metric, salary figure, course code, GPA threshold, or other verifiable
statistic.

### `hot_take`

**Definition:** Makes a bold, broad generalization or opinion about the tech
industry, recruiting, or degrees with zero supporting evidence.

### `reaction`

**Definition:** Expresses an immediate emotional outburst, rant, celebration, or
short personal anecdote, such as passing a class or venting about burnout.

### The hardest boundary

**Which two labels:** `analysis` and `reaction`

**The decision rule I used every time:** If a post includes concrete, checkable
data points like salary numbers, GPA thresholds, or specific course codes, it
is classified as `analysis`, even if the tone is emotional, heated, or phrased
like a rant. A personal emotional vent remains a `reaction` when those details
only provide context and the post does not develop an argument or career
analysis.



---

## The Dataset

**Where the posts came from:** r/csMajors.

**How I labelled them:** I labeled the first 20 posts by hand. The remaining
171 posts were pre-labeled with AI and then manually audited and corrected.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 68 | 35.6% |
| `hot_take` | 71 | 37.2% |
| `reaction` | 52 | 27.2% |
| **Total** | **191** | **100%** |

**Three hard cases**

**1.**

> *The post:* “I made $120k at my internship but I feel like I learned
> absolutely nothing and wasted my summer.”
>
> *Could have been:* `reaction` because of its emotional tone.
>
> *I chose, because:* `analysis` because it cites the concrete salary figure
> `$120k`, following the data-point rule.

**2.**

> *The post:* “SWE is dead, everyone should switch to Trades or Quant
> immediately.”
>
> *Could have been:* `analysis` if the claim were supported by evidence.
>
> *I chose, because:* `hot_take` because it is a sweeping industry claim with
> no supporting facts or data points.

**3.**

> *The post:* “Just failed CS2110 midterm after studying 40 hours. I hate this
> major so much.”
>
> *Could have been:* `analysis` because it contains a course code and study
> hours.
>
> *I chose, because:* `reaction` because it is a personal emotional vent with
> no structured argument or career analysis.

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate `2e-5`, batch size `16`, seed `42`, and
maximum token length `128`.

**Anything I changed from the defaults, and why:** I used the notebook's
provided training configuration and kept the seed fixed at 42 for the Unit 5
run so the result is reproducible.

**Split sizes:** 133 training posts, 29 validation posts, and 29 test posts.
The test split contained 10 `analysis`, 11 `hot_take`, and 8 `reaction` posts.

---

## How I Used AI

**Moment 1**

- *What I asked for:* I asked Copilot to generate edge-case post scenarios to
  stress-test the boundary decision rule.
- *What came back:* It produced examples designed to test the boundaries
  between analysis, hot takes, and emotional reactions.
- *What I changed:* I reviewed the examples and refined the decision rule
  before labeling the dataset.

**Moment 2**

- *What I asked for:* I asked Copilot to generate and pre-label the rows after
  the first 20 in `labels.csv`.
- *What came back:* It generated candidate posts and assigned one of the three
  taxonomy labels to each row.
- *What I changed:* I manually audited the pre-labeled rows against the
  taxonomy and decision rule, correcting labeling mistakes.

**Pre-labelling disclosure:** The first 20 posts were labeled by hand. The
AI-assisted rows were not accepted without human review.

---

## Execution Environment

Fine-tuning was executed locally with PyTorch inside the dedicated `.venv`
virtual environment. The recorded device was Apple Silicon GPU through
PyTorch MPS, with PyTorch version `2.14.1`.

The Unit 5 run achieved:

| Measure | Result |
|---|---:|
| Overall accuracy | 0.724 |
| Macro F1 | 0.745 |
| F1 — `analysis` | 0.636 |
| F1 — `hot_take` | 0.600 |
| F1 — `reaction` | 1.000 |

The run generated `results.json` and `test_split.csv` in the project folder.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline |
|---|---|
| Overall accuracy | 0.700 |
| Macro F1 | 0.602 |
| F1 — `analysis` | 0.714 |
| F1 — `hot_take` | 0.222 |
| F1 — `reaction` | 0.870 |

**What I predicted before I looked:**
prediciton: analysis

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
