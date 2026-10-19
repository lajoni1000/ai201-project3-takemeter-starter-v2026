# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall Accuracy

The model must achieve at least 80% overall accuracy on the held-out test set.

**Why this target:** I see this classifier as a customer service center, where questions are directed to specialized teams depending on the user's needs. I chose 80% because I consider it a reasonable starting point for correctly routing most questions, while accepting that approximately 2 out of 10 may need human review or correction. A wrong classification could send a question to the wrong team.

<!-- Your criterion. It must name a number. -->

---

## 2. Per-Label Performance

Each of the three labels must achieve an F1-score of at least 0.70 on the held-out test set.

**Why this target:** I want to evaluate the model's performance for each label, not just its overall accuracy, because a good overall result could hide poor performance in one category. The F1-score combines precision and recall, helping me evaluate how well the model identifies each type of question. I chose 0.70 because I consider it a reasonable starting point for the first version of my classifier, while recognizing that there is still room for improvement.


---

## 3. Risk Assessment Recall

The `risk_assessment` label must achieve at least 80% recall on the held-out test set.

**Why this target:** I consider risk assessment questions more critical than the other types of questions, so I want to give them priority. Going back to my customer service center example, I want at least 4 out of 5 risk assessment questions to be directed to the appropriate team. I chose 80% because I do not want more than 20% of these questions to be incorrectly routed to another team, where users might receive a conceptual explanation instead of a security risk assessment.


---

## 4. Maximum Label Share

No single label should represent more than 60% of the 200-post dataset.

**Why this target:** During my initial review of posts from Information Security Stack Exchange, I noticed that many questions seemed to be asking for implementation guidance. My initial impression is that this category could represent more than 45% of the questions. I chose a maximum of 60% because I want to allow some differences between categories while preventing one label from dominating the dataset. This would leave at least 40% of the examples for the other two categories.


---

## 5. Minimum Label Representation

Each of the three labels must have at least 40 examples in the 200-post dataset.

**Why this target:** Ideally, I would like each category to represent around 30% of the dataset. However, for this first version of TakeMeter, I feel comfortable starting with a minimum of 20% per category. This will ensure that the model has examples from all three categories to learn from, even if the dataset is not perfectly balanced.


---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
