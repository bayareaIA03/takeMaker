# TakeMeter — planning.md

> Written before any data collection or model training.
> Community: r/fantasyfootball
> Updated before any stretch features are started.

---

## Community

**Community chosen:** r/fantasyfootball (Reddit)

r/fantasyfootball is one of the largest and most active sports communities on Reddit, with over 2 million members. During the NFL season (September through January), hundreds of posts go up every day. The discourse is genuinely varied: some posts are careful data-driven breakdowns of player usage and matchups, some are emotional vents after a bad week, and some are direct requests for lineup help or trade advice. This makes it an ideal fit for a classification task because the differences between post types are real, consistent, and meaningful to the people who participate in the community.

A regular r/fantasyfootball member already knows instinctively whether a post is a rant, a question, or an argument — they just don't have labels for it. The goal of TakeMeter is to make those distinctions explicit and learnable.

**Why this community works for classification:**
- Posts are text-heavy and self-contained (most posts give enough context to classify without needing external knowledge)
- The community is large enough that all three label types appear frequently
- The distinctions between labels are ones that actually matter to participants — a rant and a genuine advice request call for very different responses from the community

---

## Label Taxonomy

### Label 1: `advice`

**Definition:** A post that asks for or provides concrete, actionable fantasy football guidance — start/sit decisions, waiver wire pickups, trade evaluations, or lineup help — where the primary goal is a practical recommendation for a specific decision.

**Example 1 (asking for advice):**
> "Should I start Jaylen Waddle or Stefon Diggs this week? Waddle has a great matchup against the Cardinals secondary but Diggs is my WR2 and more consistent. Half PPR league."

**Example 2 (giving advice):**
> "If you're looking for a Week 10 flex streamer, Brandon Aiyuk is available in 40% of leagues and has a top-5 matchup against the Falcons. His snap share has been climbing the last three weeks — worth the add before he gets picked up."

**Why these are `advice`:** Both posts are oriented around a specific decision. The first is asking "which one should I start?" The second is telling readers what to do on waivers. Even though the second post includes reasoning, the primary purpose is the actionable recommendation.

---

### Label 2: `rant`

**Definition:** A post expressing frustration, disbelief, or emotional venting about a fantasy outcome — a player's performance, a bad beat, an injury, a commissioner decision, or a bad luck situation — where no advice or analysis is being requested or offered. The post is purely expressive.

**Example 1:**
> "Of course Davante Adams decides to have a 3-catch, 28-yard game the ONE week I start him. I benched him last week when he had 150 yards and two TDs. I hate this game so much."

**Example 2:**
> "Lost by 0.4 points because my kicker missed a 28-yard field goal in garbage time. A 28-YARDER. That's an automatic make. Season is over. I'm done with fantasy football forever."

**Why these are `rant`:** Neither post asks a question or makes an argument. They are purely emotional reactions to a bad outcome. There is nothing actionable for a reader to respond to except sympathy.

---

### Label 3: `analysis`

**Definition:** A post that makes a reasoned argument or interpretation about a player, matchup, or fantasy trend, supported by statistics, usage data, historical comparison, or structured reasoning — going beyond a single opinion or assertion to explain the *why* behind a claim, without directing a specific immediate action.

**Example 1:**
> "People are way too low on Rhamondre Stevenson for the rest of the season. Over the last four weeks he's averaging 22 carries per game with a 68% snap share. The Patriots have been running the ball more since Mac Jones went down, and his remaining schedule has three bottom-10 run defenses. The usage is real and the opportunity is there."

**Example 2:**
> "The Travis Kelce hype is getting out of hand. Yes, he leads all TEs in targets, but his yards per route run is down 18% from last season and the Chiefs have been leaning heavily on the run in the second half of games. He's a solid TE1 but not worth a top-5 overall pick at his current ADP."

**Why these are `analysis`:** Both posts build an argument using multiple data points. They are trying to convince you of something about a player's value, not answer a specific "what should I do?" question.

---

## Hard Edge Cases

### Edge Case 1: `advice` vs `analysis`

This is the most important boundary in the taxonomy. Both label types can include statistics and reasoning, which makes them easy to confuse.

**Ambiguous post example:**
> "I think you should trade for Tyreek Hill right now — he's been getting 10+ targets a week and the Dolphins have four great matchups coming up. Pull the trigger."

This post could be `advice` (it's telling you to make a trade) or `analysis` (it cites target data and schedule as its reasoning).

**Decision rule:** Ask *what is the primary purpose of the post?* If the post is answering "what should I do?" — directing a specific action like a start/sit choice, a trade, or a waiver add — label it `advice`, even if it includes supporting stats. If the post is making a broader claim about a player's value or trajectory without pointing at a specific decision to make, label it `analysis`. The test: could you read this post without being in a situation where you need to make a decision right now, and still get something out of it? If yes, it's probably `analysis`. If the post only makes sense as a response to someone deciding what to do, it's `advice`.

**Applied to the ambiguous example above:** The post says "you should trade for him" and "pull the trigger" — it's directing an action. → `advice`.

---

### Edge Case 2: `rant` vs `advice`

Some posts start with frustration but end with a question.

**Ambiguous post example:**
> "Just lost my best player to injury. Absolutely brutal. Is there anyone on the waiver wire who can replace DeAndre Hopkins' production this week?"

This starts with venting but ends with a genuine waiver wire question.

**Decision rule:** If the post contains a specific, answerable question about what to do in fantasy football, label it `advice`. Emotional framing does not override a genuine request for help. Only label `rant` if the post is purely expressive with no actionable question attached. If the post both vents AND asks a question, the question determines the label.

**Applied to the ambiguous example above:** The post asks a specific waiver wire question. → `advice`.

---

## Data Collection Plan

**Source:** r/fantasyfootball on Reddit — public posts and comments only, no content behind authentication.

**Collection method:** Manual collection using the Reddit web interface. I will browse posts sorted by "Hot," "Top (This Week)," and "New" to get a mix of recency and engagement. For `rant` examples, I will check weekly threads like "Free Talk Friday" and post-game reaction threads. For `analysis` examples, I will look for posts with titles like "breakdown," "deep dive," "RoW value," or "sleeper alert." For `advice` examples, I will use the weekly "Start/Sit," "Waiver Wire," and "Trade Advice" megathreads.

**Target distribution:**

| Label      | Target Count | Where I'll find them                          |
|------------|--------------|-----------------------------------------------|
| `advice`   | 70–80        | Start/Sit threads, Waiver Wire megathreads    |
| `rant`     | 60–70        | Free Talk Friday, post-game threads           |
| `analysis` | 60–70        | Player breakdown posts, RoW discussion posts  |
| **Total**  | **200+**     |                                               |

**What I will NOT collect:**
- Posts under 15 words (not enough signal for the classifier)
- Posts that are just links with no body text
- Image posts or trade screenshots with no written explanation
- Posts that are purely a question with no context (e.g., just "start Kelce or Andrews?")

**If a label is underrepresented:** If `analysis` is hard to find, I will search specifically for "breakdown" or "deep dive" in the subreddit search bar. If `rant` is underrepresented, I will go to post-game threads from recent NFL weeks. I will not proceed to labeling until all three labels have at least 60 examples collected.

---

## Evaluation Metrics

**Metrics I will use:**
- **Overall accuracy** — what fraction of test examples the model labels correctly
- **Per-class F1 score** — the harmonic mean of precision and recall for each of the three labels individually
- **Confusion matrix** — a 3×3 table showing which label pairs the model confuses and in which direction

**Why F1 and not just accuracy:**

My label distribution will be roughly balanced (about 33% per label), so accuracy alone won't be misleading the way it would for an imbalanced dataset. However, per-class F1 is still essential because it tells me *which specific label* the model is failing on. A model with 72% overall accuracy might have an `analysis` F1 of 0.40 and `advice` F1 of 0.90 — accuracy hides that gap entirely. I need to know whether the model learned all three distinctions or just two of them.

**Why a confusion matrix:**

The confusion matrix tells me the direction of errors, not just the count. If the model is predicting `analysis` when the true label is `advice` more often than the reverse, that tells me something specific about which side of the boundary the model is landing on. That's actionable — I can look at those specific misclassified examples and understand why the model made that mistake.

**Why precision and recall separately (for stretch analysis):**

If `rant` has high precision but low recall, the model is conservative — it only predicts `rant` when very confident but misses many real rant posts. If `rant` has high recall but low precision, the model is over-predicting it. These two failure modes call for different fixes.

---

## Definition of Success

**Minimum acceptable performance (for this assignment):**
- Overall accuracy ≥ 70% on the test set for the fine-tuned model
- Per-class F1 ≥ 0.60 for all three labels — no label should be completely unlearned
- Fine-tuned model must outperform the zero-shot Groq baseline by at least 10 percentage points in overall accuracy

**What "good enough for real deployment" looks like:**

If this classifier were used in an actual tool — for example, to automatically flair posts on r/fantasyfootball or route them to the right weekly megathread — I would want overall accuracy ≥ 80% and no single label with F1 below 0.70. Below those thresholds, too many posts would be misfiled to make the tool trustworthy.

**What would make me suspicious of the results:**

If overall accuracy exceeds 95% on a subjective 3-class task with 200 examples, I would check for test set leakage into the training data. I would also check whether my labels accidentally correlated with easy surface features — for example, if all `rant` posts contain "I hate" or "I'm done" and the model just learned those keywords without understanding the broader pattern.

---

## AI Tool Plan

### Label Stress-Testing (before data collection)

Before annotating any examples, I will paste my three label definitions and both edge case descriptions into Claude and ask it to generate 8–10 posts that sit at the boundary between `advice` and `analysis`, and 5 posts that sit between `rant` and `advice`. If Claude produces posts that I genuinely cannot classify cleanly using my decision rules, I will revise the rules until I can. I will do this before collecting a single labeled example.

**What I'll ask Claude:** "Here are my three label definitions and two edge case decision rules for a fantasy football post classifier. Generate 8 posts that a human annotator would find genuinely difficult to assign to exactly one label. Do not tell me the labels — just give me the posts."

**How I'll verify:** I will try to label each generated post myself using my rules. If I cannot assign a label confidently, I will revise the definition or decision rule until I can.

### Annotation Assistance (during data collection)

I will use Claude to pre-label batches of 20–30 posts at a time by providing my label definitions and asking for one label per post. I will review and personally verify every pre-assigned label before accepting it — I will not skip reading any post. I will add a `pre_labeled` column (values: `yes` / `no`) to my CSV to track which examples were AI-suggested vs. independently labeled. I will disclose this in the AI usage section of my README.

**What I'll ask Claude:** "Here are my label definitions: [paste definitions]. For each of the following posts, assign exactly one label: advice, rant, or analysis. Output only the label name, one per line, in the same order as the posts."

**How I'll verify:** I will re-read every post and confirm or override Claude's label. If I override more than 20% of labels in a batch, I'll re-examine whether my prompt is conveying the definitions correctly.

### Failure Analysis (after fine-tuning)

After running the fine-tuned model on the test set, I will collect all misclassified examples and paste them into Claude with this prompt: "Here are posts that my fine-tuned text classifier got wrong, with the true label and the predicted label. Identify any patterns that might explain these errors — for example, similar post length, use of sarcasm, a specific pair of labels that keeps being confused, or posts that contain both a question and emotional language." I will then verify every pattern Claude identifies by re-reading the examples myself. I will include confirmed patterns and any patterns I had to discard in my evaluation report.

---

## A Complete Annotation Example (Step by Step)

This section walks through exactly what happens when I annotate a single post, to make the labeling process concrete and reproducible.

**Example post from r/fantasyfootball:**
> "CMC owners must be living their best lives right now. 28 carries, 2 TDs, 140 yards from scrimmage. He's literally a cheat code. Meanwhile I'm out here starting Ezekiel Elliott. Kill me."

**Step 1: Read the whole post.**
The post has two parts: praise for CMC's performance and frustration about the writer's own lineup decision.

**Step 2: Check against each label definition.**
- Is this `advice`? No — it does not ask for or give a specific recommendation. There is no actionable decision being addressed.
- Is this `analysis`? No — it does not make a structured argument supported by data. The CMC stats are mentioned as exclamations, not as evidence for a claim.
- Is this `rant`? Yes — it expresses frustration and envy about a fantasy outcome. The "kill me" ending is purely emotional, and the whole post is venting.

**Step 3: Check the edge case rules.**
Does this post contain a question or actionable request? No — "kill me" is not a genuine question. → No conflict with the `rant` vs `advice` rule.

**Step 4: Assign label.**
→ `rant`

**Step 5: Note it in the CSV.**
```
text: "CMC owners must be living their best lives right now..."
label: rant
notes: (none — clear case)
pre_labeled: no
```

---

## Hard Annotation Decisions (updated during data collection)

*This section will be filled in during Milestone 3 with at least 3 specific examples that were difficult to label and the decisions I made.*

| Post (abbreviated) | Labels considered | Decision | Reason |
|--------------------|-------------------|----------|--------|
| TBD after collection | | | |
| TBD after collection | | | |
| TBD after collection | | | |

---

## Stretch Features (updated before starting each one)

- [ ] Inter-annotator reliability — have someone else label 30+ examples and report Cohen's kappa
- [ ] Confidence calibration — check whether 90% confidence predictions are actually right more often than 60% confidence predictions
- [ ] Error pattern analysis — identify a systematic pattern across all wrong predictions, not just individual cases
- [ ] Deployed interface — build a Gradio app that accepts a post and returns a label + confidence score