# TakeMeter

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
> **The rest of this file is your submission.** Fill it in as you go.

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

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->
For this project, I selected Information Security Stack Exchange, a public community where people ask questions about cybersecurity. My goal is to build a classifier that identifies what type of help a person is looking for when asking a security-related question. Instead of classifying posts by cybersecurity topic, I decided to focus on the user's main need: understanding a concept, getting practical guidance, or evaluating a security risk. 


---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `conceptual_explanation`

**Definition:** The user wants to understand a cybersecurity concept, technology, or principle.

**Example 1:** What key does an HTTPS server use to encrypt data sent to a client?

**Example 2:** Public vs private network vulnerability difference

### `implementation_guidance`

**Definition:** The user needs practical guidance to configure, implement, troubleshoot, or improve a security solution.

**Example 1:** Securing Mobile/Desktop App's Access to Database

**Example 2:** How to chain multiple VPNs?

### `risk_assessment`

**Definition:** The user wants to evaluate whether a situation, behavior, or technology presents a security risk.

**Example 1:** Can open-source/weight LLMs like DeepSeek leak private code or data when run locally?

**Example 2:** Can an attacker use HTTP header injection or response splitting to trigger XSS despite HTML escaping?

### The hardest boundary

**Which two labels:** `conceptual_explanation` and `implementation_guidance`

**The decision rule I used every time:**

When a post asks for specific steps, configurations, or practical criteria to solve a cybersecurity problem, I classify it as `implementation_guidance`, even if the person also wants to understand the concept. If the main purpose is to understand how something works, without asking for a practical solution, I classify it as `conceptual_explanation`.


<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**
I collected 200 public cybersecurity questions from Information Security Stack Exchange. I used a Python script to download the questions, including their titles and full bodies, and organized them into a CSV file.


**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->
I manually labeled the first 20 posts without AI assistance to practice applying my label definitions. For the remaining 180 posts, I used ChatGPT to suggest labels. I read and reviewed every post, accepted the suggestions I agreed with, and corrected the labels when I considered another category more appropriate. I also documented some of my decisions in the note column.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
|implementation_guidance  | 82 | 41% |
|risk_assessment  | 66 | 33% |
| conceptual_explanation | 52 | 26% |
| **Total** | 200 | 100% |
The dataset contains 200 unique posts, and no label represents more than 60% of the dataset.


**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.** Post #40 — iCloud E2EE encryption keys

This post could fit either risk_assessment or conceptual_explanation. AI initially suggested risk_assessment because the question raised concerns about possible exposure of encryption keys. However, I changed it to conceptual_explanation because the main question was about how the keys are generated and handled. I decided that understanding the technical process was the primary intent.


**2.** Post #48 — Access tokens and refresh tokens

This post could fit either conceptual_explanation or implementation_guidance. The user wanted to understand why access tokens and refresh tokens are both needed, but was also implementing authentication for a web application. AI initially suggested conceptual_explanation, but I changed it to implementation_guidance because the main purpose was to decide how to implement authentication securely. I applied my decision rule that practical implementation needs take priority over conceptual explanations.


**3.** Post #80 — Security risks of proprietary software

This post could fit either conceptual_explanation or risk_assessment. The user asked whether running proprietary software, such as JetBrains IDEs, could expose encryption keys or other sensitive information on a laptop. AI initially suggested conceptual_explanation, but I changed it to risk_assessment because the user was mainly trying to understand the security risks of using this software in their specific situation, rather than just learning how the technology works.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

I used `distilbert-base-uncased`, a pretrained language model that I fine-tuned to classify cybersecurity questions into my three categories.

**Settings:**
- Maximum sequence length: 128 tokens per post
- Epochs: 3
- Learning rate: 2e-5
- Batch size: 16
- Random seed: 42
- Device: CPU
- PyTorch version: 2.14.1+cpu

**Anything I changed from the defaults, and why:**

I kept the default training settings provided in the CodePath notebook. I only replaced the example labels with my three cybersecurity categories: `conceptual_explanation`, `implementation_guidance`, and `risk_assessment`.

I wanted to complete the first training run using the original settings before experimenting with any changes.

**Split sizes:**

The notebook automatically divided my 200 labeled posts into training, validation, and test sets using an approximately 70/15/15 split.

| Label | Training | Validation | Test |
|---|---:|---:|---:|
| conceptual_explanation | 36 | 8 | 8 |
| implementation_guidance | 57 | 13 | 12 |
| risk_assessment | 46 | 10 | 10 |
| **Total** | **139** | **31** | **30** |

All three labels have at least eight examples in the test set, although the relatively small number of test examples may contribute to variation in the evaluation results.

<!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- **What I asked for:** I asked ChatGPT to help me create a Python script to collect public cybersecurity questions from Information Security Stack Exchange, including their titles and full question bodies.
- **What came back:** ChatGPT provided Python code to download the questions and organize them into a CSV file.
- **What I changed:** I tested the script, first collecting a smaller sample of questions, and then used it to collect 200 posts for my dataset.

**Moment 2**

- **What I asked for:** I asked ChatGPT to suggest labels for cybersecurity questions using my three label definitions and the decision rule I had established.
- **What came back:** ChatGPT provided suggested labels for the posts, including some cases where the classification was ambiguous.
- **What I changed:** I reviewed the full text of every post, corrected labels when I disagreed, and documented some of my decisions in the `note` column. For example, I changed post #40 from `risk_assessment` to `conceptual_explanation` because the main question was about understanding how E2EE keys work.

**Pre-labelling disclosure:**

I manually labeled the first 20 posts without AI assistance. I then used ChatGPT to pre-label the remaining 180 posts. I read and reviewed all 200 posts, accepted the suggestions I agreed with, and corrected the others. The AI-assisted labeling process and human review are documented in the `note` column of `labels.csv`. I made the final labeling decisions myself.




<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.500 | 0.400 | -0.100 |
| Macro F1 | 0.422 | 0.190 | -0.232  |
| F1 — conceptual_explanation | 0.667 | 0.000  | -0.667  |
| F1 — implementation_guidance | 0.000 | 0.571 | +0.571  |	
| F1 — risk_assessment | 0.600 | 0.000 | -0.600 |	

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
Before running the comparison, I expected my fine-tuned model to perform better than the baseline in overall accuracy and in the conceptual_explanation category.


**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

The results did not support my expectations. Overall accuracy decreased from 0.500 to 0.400, and the F1 score for conceptual_explanation dropped from 0.667 to 0.000.
Interestingly, the fine-tuned model performed better on implementation_guidance, improving its F1 score from 0.000 to 0.571.
These results suggest that fine-tuning helped the model with one category but did not improve its overall performance. I need to investigate why the model struggled with the other two categories.

---
### Impact of Label Definitions

I also ran the baseline without label_definitions.txt to see whether providing definitions would make a difference.

Measure | Without Definitions | With Definitions |
Overall accuracy | 0.433 | 0.500 |
Macro F1 | 0.352 | 0.422 |
F1 — conceptual_explanation | 0.500 | 0.667 |
F1 — implementation_guidance | 0.000 |0.000
F1 — risk_assessment | 0.556 | 0.600

Without definitions, the baseline correctly classified 13 out of 30 posts. With definitions, it correctly classified 15.
The definitions improved accuracy by 6.7 percentage points. The largest improvement was in conceptual_explanation, while implementation_guidance remained at an F1 score of 0.000.
This experiment helped me understand that providing clear label definitions can improve classification performance even without fine-tuning the model.


## Run Log — Before


<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall Accuracy | >= 0.80 | 0.40 | 0.40 | 0.40 | MISSED |
| 2. Per-Label F1 | Each label >= 0.70 | 0.00 | 0.00 | 0.00 | MISSED |
| 3. Risk Assessment Recall | >= 0.80 | 0.00 | 0.00 | 0.00 | MISSED |
| 4. Maximum Label Share | <= 60% | 41% | 41% | 41% | MET |
| 5. Minimum Label Representation | >= 40 examples | 52 | 52 | 52 | MET |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | conceptual_explanation | implementation_guidance | risk_assessment |
|---|---|---|---|
| **conceptual_explanation** | 0 | 8 | 0 |
| **implementation_guidance** | 0 | 12 | 0 |
| **risk_assessment** | 0 | 10 | 0 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->
The biggest confusion was between risk_assessment and implementation_guidance. The model classified 10 risk assessment posts as implementation guidance, but none in the opposite direction.
I also noticed that the model predicted implementation_guidance for all 30 test posts. This tells me that the model is not distinguishing the three categories correctly. I still need to investigate whether the problem comes from my labels, the dataset, or the training process.


---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->


| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall Accuracy | >= 0.80 | MISSED | Accuracy was 0.40 across all three seeds, below my 0.80 target. |
| 2 | Per-Label F1 | Each label >= 0.70 | MISSED | None of the three labels reached 0.70. Two labels had an F1 of 0.00. |
| 3 | Risk Assessment Recall | >= 0.80 | MISSED | Recall was 0.00 in all three seeds. None of the 10 risk assessment test posts were correctly identified. |
| 4 | Maximum Label Share | <= 60% | MET | The largest category was implementation_guidance, with 82 of 200 posts (41%). |
| 5 | Minimum Label Representation | >= 40 examples | MET | The smallest category was conceptual_explanation, with 52 examples. |


**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

Criterion 1 — Overall Accuracy: MISSED

My target was 80% accuracy, but the model achieved only 40% across all three seeds.

The confusion matrix shows that the model classified all 30 test questions as implementation_guidance, including questions labeled conceptual_explanation and risk_assessment.

I think the model did not learn to distinguish the three categories well enough from the available training data. One possible reason is that cybersecurity questions often use similar technical language, even when the user's main intention is different. However, I cannot confirm that this was the main cause without further testing.

Criterion 2 — Per-Label F1: MISSED

My target was an F1-score of at least 0.70 for each label, but none of the three categories reached this target.

What surprised me was that the model predicted only implementation_guidance, even though my dataset included 82 implementation, 66 risk assessment, and 52 conceptual explanation examples.

This suggests that having examples from all three categories was not enough for the model to learn how to distinguish them. The limited training data may have contributed to this problem, but I would need further testing to confirm it.

Criterion 3 — Risk Assessment Recall: MISSED

My target was at least 80% recall for risk_assessment, but the model achieved 0% across all three seeds. All 10 risk assessment questions in the test set were classified as implementation_guidance.

During labeling, I noticed that some questions could appear to request implementation guidance when the main purpose was actually to evaluate a security risk. For example, Post #197 discusses changing a Microsoft Defender Safe Links configuration, but the user's concern is whether this change introduces a security risk.

This is a possible explanation for why the model struggled with risk_assessment, although I cannot confirm that this specific post was misclassified.

What the agreement report tells me
My agreement exercise resulted in 30 out of 30 matches with the staff labels. However, I reviewed some initial labeling decisions before the final comparison, and the exercise used a different taxonomy.
Therefore, the agreement result does not prove that my cybersecurity labels were consistent. It also does not provide direct evidence that inconsistent labeling caused the model's errors. I would need a separate agreement check using my TakeMeter taxonomy to investigate that possibility.


---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 30 / 30 = 100%

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

There were no disagreements between my final labels and the staff labels. All 30 posts matched, so there were no cases to adjudicate or assign to piles (a), (b), or (c).

**What the pattern in my disagreements tells me:**
Although I had no disagreements in the final comparison, I initially had some difficulty applying the taxonomy rules.
For example, I initially classified sp_005 and sp_007 as reaction because they described personal running experiences. After reviewing the rules, I understood that these posts were better classified as analysis because the authors used measurable information, such as running times, mileage, and temperature, to support their conclusions.
Another interesting example was sp_014, which I classified as hot_take. Although the author mentioned a 3% change in running times, that number alone did not establish that shoes were the only cause. 
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
