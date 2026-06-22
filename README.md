# TakeMeter — AI201 Project 3

A fine-tuned text classifier that evaluates discourse quality in r/fantasyfootball. Given a post or comment from the subreddit, TakeMeter predicts whether it is an **advice** post, a **rant**, or an **analysis** — distinctions that matter to anyone who participates in the community.

---

## Community Choice

**Community:** r/fantasyfootball (Reddit)

r/fantasyfootball has over 2 million members and generates hundreds of posts per day during the NFL season. The discourse is genuinely varied: some posts are careful data-driven arguments about player value, some are emotional vents after a bad week, and some are direct requests for lineup help or trade advice. These distinctions are meaningful to the community — a rant and an advice request call for completely different responses, and a careful analysis is treated differently from a hot take.

The community is text-heavy, public, and active enough to collect 200+ labeled examples without scraping tools. It was a strong fit for a classification task because the label boundaries reflect how community members already think about posts — they just don't have explicit labels for it.

---

## Label Taxonomy

### `advice`
A post that asks for or provides concrete, actionable fantasy football guidance — start/sit decisions, waiver wire pickups, trade evaluations, or lineup help — where the primary goal is a practical recommendation for a specific decision.

**Example 1:**
> "Should I start Jaylen Waddle or Stefon Diggs this week? Waddle has a great matchup against the Cardinals secondary but Diggs is my WR2 and more consistent. Half PPR league."

**Example 2:**
> "If you're looking for a Week 10 flex streamer, Brandon Aiyuk is available in 40% of leagues and has a top-5 matchup against the Falcons. His snap share has been climbing the last three weeks — worth the add."

---

### `rant`
A post expressing frustration, disbelief, or emotional venting about a fantasy outcome — a player's performance, a bad beat, an injury, or a commissioner decision — where no advice or analysis is being requested or offered.

**Example 1:**
> "Of course Davante Adams decides to have a 3-catch, 28-yard game the ONE week I start him. I benched him last week when he had 150 yards and two TDs. I hate this game."

**Example 2:**
> "Lost by 0.4 points because my kicker missed a 28-yard field goal in garbage time. A 28-YARDER. Season over. I'm done with fantasy football forever."

---

### `analysis`
A post that makes a reasoned argument or interpretation about a player, matchup, or fantasy trend, supported by statistics, usage data, historical comparison, or structured reasoning — going beyond a single opinion to explain the *why* behind a claim, without directing a specific immediate action.

**Example 1:**
> "People are sleeping on Rhamondre Stevenson RoW. Over the last four weeks he's averaging 22 carries per game with a 68% snap share. The Patriots are running the ball more since Mac Jones went down, and his remaining schedule has three bottom-10 run defenses."

**Example 2:**
> "The Travis Kelce hype is getting out of hand. Yes he leads all TEs in targets, but his yards per route run is down 18% from last season and the Chiefs have been leaning heavily on the run game. He's a solid TE1 but not worth a top-5 overall pick at his current ADP."

---

## Data Collection

**Source:** r/fantasyfootball on Reddit — public posts and top-level comments only.

**Collection method:** Manual collection using the Reddit web interface. Posts were gathered from "Hot," "Top (This Week)," and "New" sorts, as well as weekly megathreads (Start/Sit, Waiver Wire, Free Talk Friday) to ensure variety across all three label types.

**Labeling process:** Each post was read individually and assigned one label using the definitions above. Ambiguous cases were resolved using two decision rules documented in planning.md: (1) if a post contains a specific actionable question, it is `advice` regardless of emotional framing; (2) if a post includes statistics as part of a broader argument without directing a specific decision, it is `analysis` even if it reads confidently.

**Label distribution:**

| Label      | Count |
|------------|-------|
| `advice`   | [FILL IN AFTER COLLECTION] |
| `rant`     | [FILL IN AFTER COLLECTION] |
| `analysis` | [FILL IN AFTER COLLECTION] |
| **Total**  | **[FILL IN]** |

**Three difficult-to-label examples:**

**1.**
> "[PASTE POST HERE]"

Labels considered: `advice` vs `analysis`
Decision: [YOUR DECISION]
Reason: [YOUR REASON]

**2.**
> "[PASTE POST HERE]"

Labels considered: `rant` vs `advice`
Decision: [YOUR DECISION]
Reason: [YOUR REASON]

**3.**
> "[PASTE POST HERE]"

Labels considered: [LABELS]
Decision: [YOUR DECISION]
Reason: [YOUR REASON]

---

## Fine-Tuning Approach

**Base model:** `distilbert-base-uncased` from HuggingFace

**Training setup:**
- Framework: HuggingFace `transformers` + `datasets`
- Platform: Google Colab (T4 GPU)
- Train / Validation / Test split: 70% / 15% / 15% (handled automatically by the starter notebook)

**Hyperparameter decisions:**

| Parameter     | Value | Reason |
|---------------|-------|--------|
| Epochs        | [FILL IN] | [e.g., "Used 3 epochs — more caused overfitting on validation loss"] |
| Learning rate | [FILL IN] | [e.g., "Kept default 2e-5 — validation F1 did not improve at 5e-5"] |
| Batch size    | [FILL IN] | [e.g., "Used 16 — standard for DistilBERT on T4 GPU"] |

---

## Baseline

**Model used:** `llama-3.3-70b-versatile` via Groq API (zero-shot, no fine-tuning)

**Prompt used:**

```
You are a classifier for posts from the r/fantasyfootball subreddit.

Classify the following post into exactly one of these three labels:

- advice: A post that asks for or gives concrete, actionable fantasy football guidance — start/sit decisions, waiver pickups, trade evaluations, or lineup help. The primary goal is a practical recommendation.
- rant: A post expressing frustration, disbelief, or emotional venting about a fantasy outcome. No advice or analysis is being requested or offered.
- analysis: A post that makes a reasoned argument about a player, matchup, or trend, supported by stats, usage data, or structured reasoning — without directing a specific immediate action.

Respond with only one word: advice, rant, or analysis.

Post: {post_text}
```

**How results were collected:** The prompt was run on every example in the locked test set using the baseline section of the starter Colab notebook. The notebook parsed each response and flagged any that returned more than one word or an unrecognized label.

---

## Evaluation Report

### Overall Accuracy

| Model            | Accuracy |
|------------------|----------|
| Zero-shot baseline (Groq) | [FILL IN]% |
| Fine-tuned DistilBERT     | [FILL IN]% |

---

### Per-Class Metrics — Zero-Shot Baseline

| Label      | Precision | Recall | F1   |
|------------|-----------|--------|------|
| `advice`   | [FILL]    | [FILL] | [FILL] |
| `rant`     | [FILL]    | [FILL] | [FILL] |
| `analysis` | [FILL]    | [FILL] | [FILL] |

---

### Per-Class Metrics — Fine-Tuned Model

| Label      | Precision | Recall | F1   |
|------------|-----------|--------|------|
| `advice`   | [FILL]    | [FILL] | [FILL] |
| `rant`     | [FILL]    | [FILL] | [FILL] |
| `analysis` | [FILL]    | [FILL] | [FILL] |

---

### Confusion Matrix — Fine-Tuned Model

*Rows = true label. Columns = predicted label.*

|                | Predicted: advice | Predicted: rant | Predicted: analysis |
|----------------|-------------------|-----------------|----------------------|
| **True: advice**   | [FILL] | [FILL] | [FILL] |
| **True: rant**     | [FILL] | [FILL] | [FILL] |
| **True: analysis** | [FILL] | [FILL] | [FILL] |

---

### Wrong Predictions — Analysis

**Wrong prediction 1:**

Post:
> "[PASTE THE MISCLASSIFIED POST]"

True label: [LABEL] | Predicted label: [LABEL]

Analysis: [Explain why the model got this wrong. Which labels are being confused? Is this a labeling inconsistency, a surface feature problem, a short post, or a genuinely ambiguous case? What would need to change to fix it?]

---

**Wrong prediction 2:**

Post:
> "[PASTE THE MISCLASSIFIED POST]"

True label: [LABEL] | Predicted label: [LABEL]

Analysis: [Same format — go deeper than "the model got it wrong."]

---

**Wrong prediction 3:**

Post:
> "[PASTE THE MISCLASSIFIED POST]"

True label: [LABEL] | Predicted label: [LABEL]

Analysis: [Same format.]

---

### Sample Classifications

*Posts run through the fine-tuned model with predicted label and confidence score.*

| Post (abbreviated) | True Label | Predicted Label | Confidence |
|--------------------|------------|-----------------|------------|
| [FILL IN] | [LABEL] | [LABEL] | [e.g. 0.94] |
| [FILL IN] | [LABEL] | [LABEL] | [e.g. 0.81] |
| [FILL IN] | [LABEL] | [LABEL] | [e.g. 0.67] |
| [FILL IN] | [LABEL] | [LABEL] | [e.g. 0.73] |
| [FILL IN] | [LABEL] | [LABEL] | [e.g. 0.88] |

**Correct prediction explained:**

[Pick one correctly predicted post from the table above and write 1–2 sentences explaining why the prediction makes sense. Example: "The model correctly identified this post as a rant with 0.94 confidence. The post contains no question, no statistics, and no actionable suggestion — it is entirely an emotional reaction to a bad beat, which is the clearest possible rant signal."]

---

## Reflection: What the Model Learned vs. What I Intended

[Write 2–3 paragraphs here after you have your results. This is NOT about listing wrong predictions — it's a higher-level observation about the gap between your label definitions and what the model's decision boundary actually captures.

Prompts to guide your writing:
- What surface features might the model have learned instead of the actual distinctions you defined? (e.g., did it learn that "rant" = posts with exclamation marks rather than posts with no actionable content?)
- Which boundary did the model learn well and which did it miss?
- If you retrained with more data or different labels, what would you change?]

---

## Spec Reflection

**One way the spec helped:**

[Example: "The spec's requirement to write decision rules for edge cases before annotating saved significant time. Having an explicit rule for `advice` vs `analysis` — 'if the post is answering what should I do, it's advice' — meant I almost never had to pause during annotation. Without that rule written down in advance, I would have labeled similar posts inconsistently."]

**One way implementation diverged from the spec:**

[Example: "The spec suggested aiming for 70–80 advice examples, but in practice advice posts were so common on r/fantasyfootball that I had to actively stop collecting them at 75 and go looking for analysis posts specifically. The actual collection process was less balanced by default than the plan assumed."]

---

## AI Usage

**Instance 1 — Label stress-testing:**

What I directed the AI to do: Pasted my three label definitions and two edge case descriptions into Claude and asked it to generate 8 posts that sit at the boundary between `advice` and `analysis`.

What it produced: [Describe what Claude generated — were the posts genuinely hard? Did any of them expose a gap in your definitions?]

What I changed or overrode: [e.g., "Two of the generated posts exposed a gap in my `analysis` definition — posts that cited a single stat to support a trade recommendation. I revised the decision rule to specify that a post directing a specific action is always `advice` regardless of whether it includes a supporting stat."]

---

**Instance 2 — Annotation assistance:**

What I directed the AI to do: Provided Claude with my label definitions and batches of 20–30 unlabeled posts, asking it to assign one label per post.

What it produced: Pre-labeled batches with one label per line in the same order as the input posts.

What I changed or overrode: [e.g., "I overrode approximately 15% of Claude's labels. The most common correction was posts Claude labeled `analysis` that I labeled `advice` — Claude tended to label any post that included statistics as analysis, even when the primary purpose was a trade or start/sit recommendation."]

---

**Instance 3 — Failure analysis:**

What I directed the AI to do: After fine-tuning, I pasted all misclassified examples into Claude with their true and predicted labels and asked it to identify common patterns across the errors.

What it produced: [Describe the patterns Claude identified.]

What I kept: [e.g., "Claude correctly identified that most errors involved short posts under 20 words — the model appeared to default to `rant` for short posts regardless of content. I verified this by checking post length across all misclassified examples."]

What I discarded: [e.g., "Claude suggested the model struggled with posts mentioning injuries, but when I re-read those examples I found the labels themselves were inconsistent — I had labeled similar injury posts differently during annotation."]

---

## Setup

**1. Clone the repo:**

```bash
git clone https://github.com/[YOUR_USERNAME]/ai201-project3-takemeter
cd ai201-project3-takemeter
```

**2. Open the Colab notebook:**

Go to the [TakeMeter starter notebook](https://colab.research.google.com/) and save a copy to your Drive. Set the runtime to **T4 GPU** before running any cells.

**3. Add your Groq API key:**

In Colab, click the 🔑 icon in the left sidebar, add a secret named `GROQ_API_KEY`, and enable notebook access.

**4. Upload your dataset:**

When Section 1 prompts you to upload a file, upload `data/labeled_posts.csv` from this repo. The notebook handles the train/validation/test split automatically.

**5. Run sections in order:**

- Section 1: Define label map and upload CSV
- Section 2: Split and tokenize dataset
- Section 5: Run zero-shot baseline (do this before fine-tuning)
- Section 3: Fine-tune DistilBERT
- Section 4: Evaluate fine-tuned model
- Section 6: Side-by-side comparison and export

---

## Repository Structure

```
ai201-project3-takemeter/
├── planning.md                  ← Design decisions written before data collection
├── README.md                    ← This file — final report and documentation
├── data/
│   └── labeled_posts.csv        ← 200+ labeled examples (text, label, notes, pre_labeled)
├── outputs/
│   ├── evaluation_results.json  ← Exported from Colab Section 6
│   └── confusion_matrix.png     ← Exported from Colab Section 4
└── notebook/
    └── takemeter_colab.ipynb    ← Your completed Colab notebook (optional export)
```