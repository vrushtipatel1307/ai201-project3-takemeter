# planning.md — r/television Discourse Classifier

## 1. Community

**Chosen community:** r/television (and r/TrueFilm as a secondary source for analysis-heavy posts)

r/television is one of the largest TV-focused communities on Reddit, with millions of members posting daily across every genre and era of television. The discourse is remarkably varied in quality: the same thread about a Breaking Bad episode might contain a 400-word argument about narrative structure, a one-line gut reaction to a death scene, a confident hot take about overrated shows, and a "should I watch this?" recommendation request. That range — all in one place, all about the same domain — makes it an ideal fit for a classification task.
The distinctions I'm capturing (analysis vs. hot take vs. reaction vs. recommendation) reflect how people in the community *already* talk about discourse quality. Regulars in r/television will describe a post as "just a reaction" or "actually a well-argued take" without prompting. The labels aren't imposed from outside — they reflect native community norms.

---

## 2. Labels

### `analysis`
**Definition:** The post makes a structured argument about craft, writing, character, themes, or performance — backed by specific evidence such as scene references, comparisons to other shows, or narrative observations. The argument could stand on its own even if you stripped out any opinion language.

**Example 1 (clear):**
> "The reason the final season of Game of Thrones felt so rushed isn't just pacing — it's that the show had always used travel time as a way to show consequence. Ned's journey to King's Landing took 3 episodes and showed us Westeros. Dany flying from Dragonstone to the North in one cut destroyed the geography that the whole political map depended on. The show ate its own world-building."

**Example 2 (clear):**
> "Succession works as a tragedy precisely because Logan never has a real moment of self-awareness. Compare this to King Lear — the tragedy there depends on Lear knowing what he did wrong. In Succession, Logan dies mid-sentence, mid-deal. That's the point. The show denies him the tragic hero's recognition scene, and denies the audience the catharsis of watching him reckon with it."

---

### `hot_take`
**Definition:** A bold or contrarian opinion stated with confidence but without supporting argument. The post makes a claim — often one the author expects to be controversial — but asserts rather than argues. Evidence, if present, is decorative or cherry-picked rather than genuinely reasoned.

**Example 1 (clear):**
> "Breaking Bad is overrated and I will die on this hill. Walter White is just an unlikeable man doing unlikeable things for 5 seasons. The writing treats cruelty as depth and the fanbase mistakes grimness for sophistication."

**Example 2 (clear):**
> "Prestige TV peaked in 2012 and everything since has been streaming services throwing budgets at mediocre writers. The Wire and Mad Men couldn't get made today."

---

### `reaction`
**Definition:** An immediate emotional response to a specific episode, scene, or plot development. The post expresses a feeling — shock, grief, hype, outrage, delight — in the moment. Little or no argument is made; the post is primarily communicating an emotional state rather than a position.

**Example 1 (clear):**
> "I literally had to pause after the Red Wedding and just sit there. I knew it was coming because my roommate warned me and I STILL wasn't ready. Absolutely devastating."

**Example 2 (clear):**
> "THEY ACTUALLY DID IT. I can't believe they went there. That finale is going to break the internet and I am HERE for it. Cannot sleep."

---

### `recommendation`
**Definition:** The post primarily asks for a show to watch, or recommends one to others, with or without supporting reasoning. The defining feature is that the post's purpose is to direct viewing behavior, not to argue a position or express an emotion about content already watched.

**Example 1 (clear):**
> "If you liked The Bear and want something with the same controlled chaos energy, watch Fleabag. Same single-location intensity, same unreliable protagonist. Don't sleep on it."

**Example 2 (clear):**
> "Looking for something to watch this weekend — I've burned through all of HBO and I'm open to anything. Really liked The Leftovers and Severance. Bonus points if it has a satisfying ending."

---

## 3. Hard Edge Cases

### The hardest anticipated case: `hot_take` vs. `analysis`

The boundary that will cause the most annotation pain is between a hot take that *cites something* and an analysis that *takes a position*. Many posts in r/television do both.

**Example borderline post:**
> "People who say The Wire is the greatest TV drama ever are confusing 'morally serious' with 'technically great.' The show's dialogue is often stagey and unnatural — characters explain the drug trade to each other in ways that only make sense if you're explaining it to the audience. That's a structural weakness, not a virtue."

This post makes a specific, arguable claim (the dialogue is stagey) with a reason (exposition disguised as character speech). But it's framed as a rebuttal to a cultural consensus, and the "evidence" is an impression rather than a scene-level demonstration.

**Decision rule:** If the post provides specific, verifiable evidence that would support the claim even if you removed all opinion framing, label it `analysis`. If the evidence is impressionistic, cherry-picked, or just enough to sound credible without actually reasoning through the claim, label it `hot_take`. For the post above: the claim about dialogue is asserted, not demonstrated with examples — → `hot_take`.

### Secondary edge case: `reaction` vs. `hot_take`

A post can be emotionally charged AND make a bold claim. "This finale was an embarrassment and the showrunners should be ashamed" reads like a reaction but is actually a `hot_take` — it's making a claim about quality, not expressing a feeling about plot.

**Decision rule:** If the post's primary content is a feeling (I was devastated, I'm so hyped, I can't believe that happened), label it `reaction`. If it's a judgment about quality, a director, a show's legacy — even if written in an emotional register — label it `hot_take`.

### Third edge case: `recommendation` vs. `analysis`

A long, well-reasoned recommendation ("here's why you should watch X, and here's what makes it work") can look like analysis. 

**Decision rule:** If the post's *purpose* is to direct viewing behavior — asking for recs or giving them — label it `recommendation` regardless of how much supporting reasoning it contains. The intent, not the length or evidence level, defines the label.

---

## 4. Data Collection Plan

**Sources:**
- r/television: main source. Collect from weekly discussion threads, episode threads, and the "Best of" sidebar.
- r/TrueFilm (crossover TV posts): supplement for analysis-heavy examples, which may be underrepresented in r/television's more casual tone.

**Target distribution (200 examples minimum):**

| Label | Target count | % of dataset |
|---|---|---|
| `analysis` | 45 | 22.5% |
| `hot_take` | 55 | 27.5% |
| `reaction` | 60 | 30% |
| `recommendation` | 40 | 20% |

Reaction posts are the most abundant in the wild, so they're given the highest target — but capped at 30% to avoid imbalance. Analysis posts are rarest and may require active searching in episode discussion threads.

**If a label is underrepresented after 150 examples:**
- For `analysis`: search r/TrueFilm or look specifically in long-form episode discussion threads where users write multi-paragraph responses.
- For `recommendation`: search "what should I watch" or "looking for shows like X" posts.
- Do not manufacture or paraphrase examples — only collect organic community posts.

**Imbalance threshold:** If any label exceeds 70% of the dataset after collection, re-collect before annotating. Target: no label below 15% or above 35%.

---

## 5. Evaluation Metrics

**Primary metrics:**
- **Per-class F1** (most important): because class imbalance is likely and some labels matter more than others for downstream use. Accuracy alone would let the model look good by over-predicting `reaction`.
- **Macro F1**: average F1 across all classes, unweighted by support. This penalizes the model equally for failing on `recommendation` (rare) and `reaction` (common).
- **Confusion matrix**: to identify *which* label pairs are being confused — especially the `hot_take` / `analysis` boundary, which is the hardest by design.

**Why accuracy alone is insufficient:** If `reaction` makes up 30% of the dataset and the model predicts `reaction` for everything, it achieves 30% accuracy — but is completely useless. Macro F1 breaks this by requiring the model to perform across all classes.

**Secondary metrics:**
- Per-class precision and recall to diagnose whether the model is over- or under-predicting specific labels.
- Baseline comparison: zero-shot LLM performance on the same test set. The fine-tuned model must meaningfully exceed this.

---

## 6. Definition of Success

A classifier is **genuinely useful** for a community moderation or recommendation tool if it can reliably distinguish the high-signal labels (analysis, hot_take) from the low-signal ones (reaction, recommendation).

**Specific thresholds:**

| Metric | Minimum acceptable | Target |
|---|---|---|
| Overall accuracy | ≥ 65% | ≥ 75% |
| Macro F1 | ≥ 0.60 | ≥ 0.70 |
| Per-class F1 (each label) | ≥ 0.55 | ≥ 0.65 |
| Improvement over baseline | +10 pp accuracy | +15 pp accuracy |

**Failure condition:** If any single class achieves F1 < 0.40, the classifier is not deployable for that label — the boundary is either too ambiguous or the training data is too noisy for that distinction.

**Why these thresholds:** A 0.65+ per-class F1 on all four labels means the model has genuinely learned all four distinctions, not just the easy ones. In a real community tool (e.g., tagging posts for quality filters or search), misclassifying analysis as hot_take has real cost — so 0.55 is the floor, not the target.

---

## 7. AI Tool Plan

### Label stress-testing
Before annotating 200 examples, I will give Claude my four label definitions and the edge case descriptions above and ask it to generate 10 posts that sit at the boundary between `hot_take` and `analysis`. If it produces posts I can't cleanly classify with my decision rules, I'll tighten the definitions before any annotation begins. This is the highest-leverage use of AI in this project — catching definition ambiguity before it contaminates 200 labels.

**Prompt I'll use:**
> "Here are four label definitions for a TV discourse classifier: [paste labels]. Generate 10 posts that are genuinely ambiguous between hot_take and analysis — posts where a careful annotator would have to think hard. Do not tell me the label."

### Annotation assistance
I plan to use Claude to pre-label one batch of ~50 examples mid-way through annotation, after I have labeled the first 50 myself to calibrate. I'll provide my full label definitions and the decision rules from Section 3. I will review and correct every pre-assigned label — not skim. I'll track which examples were pre-labeled in the CSV's `source` column (`human` vs. `llm_prelabeled`).

If I find the pre-labeled batch has more than ~15% corrections, I'll stop using pre-labeling and annotate the rest manually — that error rate signals the definitions aren't clear enough for LLM annotation.

### Failure analysis
After fine-tuning, I'll paste all misclassified test examples into Claude and ask:
> "Here are posts my classifier got wrong, with their true and predicted labels. What patterns do you see — in length, framing, topic, or writing style — that might explain the errors?"

I'll then verify every pattern it identifies by re-reading the examples myself. I expect it to find: short posts being misclassified (low signal), posts about prestige shows (where community tone is more analytical regardless of content), and sarcastic hot takes being read as reactions. Whatever it finds — including patterns I discard — will appear in my evaluation report.
