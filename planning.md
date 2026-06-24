# planning.md — r/television Discourse Classifier

## 1. Community

**Chosen community:** r/television (with r/TrueFilm as a secondary source for more analysis-heavy posts)

I chose r/television as my main community because it is one of Reddit’s largest spaces for discussing television. Millions of users take part in conversations about shows from every genre and time period.

What makes this community especially useful is the variety of discussion styles. A single thread about a show like Breaking Bad might include a long discussion about storytelling, a short emotional reaction to a character death, a controversial opinion about the series, and someone simply asking for recommendations. Since all of these post types appear in the same space, the community works very well for a classification task.

The four labels in this project—analysis, hot_take, reaction, and recommendation—also match the way people in the community already talk about posts. Regular users often describe something as “just a reaction” or “a well-argued take.” Because of that, these labels reflect the community’s own norms rather than categories created from outside the community.

---

## 2. Labels

### analysis

**Definition:**
The post makes a structured argument about writing, characters, themes, acting, or storytelling. It includes evidence such as scene references, comparisons, or narrative observations. The reasoning would still make sense even if the opinion language were removed.

**Example 1:**

> "The reason the final season of Game of Thrones felt so rushed isn't just pacing — it's that the show had always used travel time as a way to show consequence. Ned's journey to King's Landing took 3 episodes and showed us Westeros. Dany flying from Dragonstone to the North in one cut destroyed the geography that the whole political map depended on. The show ate its own world-building."

**Example 2:**

> "Succession works as a tragedy precisely because Logan never has a real moment of self-awareness. Compare this to King Lear — the tragedy there depends on Lear knowing what he did wrong. In Succession, Logan dies mid-sentence, mid-deal. That's the point. The show denies him the tragic hero's recognition scene, and denies the audience the catharsis of watching him reckon with it."

---

### hot_take

**Definition:**
A strong or controversial opinion that is stated confidently but does not provide much supporting evidence. The author makes a claim but does not fully argue it. Any evidence that appears is usually limited or selective.

**Example 1:**

> "Breaking Bad is overrated and I will die on this hill. Walter White is just an unlikeable man doing unlikeable things for 5 seasons. The writing treats cruelty as depth and the fanbase mistakes grimness for sophistication."

**Example 2:**

> "Prestige TV peaked in 2012 and everything since has been streaming services throwing budgets at mediocre writers. The Wire and Mad Men couldn't get made today."

---

### reaction

**Definition:**
An immediate emotional response to an episode, scene, or plot development. The main purpose of the post is to express feelings such as shock, excitement, sadness, or anger rather than make an argument.

**Example 1:**

> "I literally had to pause after the Red Wedding and just sit there. I knew it was coming because my roommate warned me and I STILL wasn't ready. Absolutely devastating."

**Example 2:**

> "THEY ACTUALLY DID IT. I can't believe they went there. That finale is going to break the internet and I am HERE for it. Cannot sleep."

---

### recommendation

**Definition:**
The post mainly asks for viewing suggestions or recommends a show to someone else. The goal is to influence what someone watches rather than discuss something they have already seen.

**Example 1:**

> "If you liked The Bear and want something with the same controlled chaos energy, watch Fleabag. Same single-location intensity, same unreliable protagonist. Don't sleep on it."

**Example 2:**

> "Looking for something to watch this weekend — I've burned through all of HBO and I'm open to anything. Really liked The Leftovers and Severance. Bonus points if it has a satisfying ending."

---

## 3. Hard Edge Cases

### Hardest case: hot_take vs. analysis

The most difficult distinction is between a hot take that includes some evidence and an analysis that takes a strong position. Many posts in r/television contain both.

**Example:**

> "People who say The Wire is the greatest TV drama ever are confusing 'morally serious' with 'technically great.' The show's dialogue is often stagey and unnatural — characters explain the drug trade to each other in ways that only make sense if you're explaining it to the audience. That's a structural weakness, not a virtue."

This post makes a specific claim and gives a reason, but the evidence is mostly based on personal impressions rather than clear examples.

**Decision rule:**
If the evidence would still support the claim after removing the opinion language, the post is labeled analysis. If the evidence is weak, selective, or mostly there to support an opinion, it is labeled hot_take.

For this example, the claim is stated but not demonstrated with specific scenes, so it would be labeled hot_take.

---

### Secondary case: reaction vs. hot_take

A post can sound emotional while also making a judgment.

For example:

> "This finale was an embarrassment and the showrunners should be ashamed."

Although emotional, this statement is judging the quality of the show rather than describing feelings about the story.

**Decision rule:**
If the post mainly expresses emotions such as shock, sadness, or excitement, it is labeled reaction. If it evaluates the quality of a show, writer, or director, it is labeled hot_take, even if the language is emotional.

---

### Third case: recommendation vs. analysis

Some recommendations contain detailed reasoning and may look like analysis.

**Decision rule:**
If the purpose of the post is to encourage someone to watch a show or ask for recommendations, it is labeled recommendation, regardless of how much analysis it contains. The author's intention matters more than the amount of evidence.

---

## 4. Data Collection Plan

### Sources

* r/television as the primary source.
* r/TrueFilm as a secondary source for more analytical discussions.

### Target distribution

| Label          | Target Count | Percentage |
| -------------- | ------------ | ---------- |
| analysis       | 45           | 22.5%      |
| hot_take       | 55           | 27.5%      |
| reaction       | 60           | 30%        |
| recommendation | 40           | 20%        |

Reaction posts are the most common, so they have the largest target count. Analysis posts are less common and may require more focused searching.

If a category becomes underrepresented:

* Analysis examples will be collected from longer discussions or r/TrueFilm.
* Recommendation posts will be collected from recommendation threads.
* Only real community posts will be used.

No label should fall below 15% or exceed 35% of the dataset.

---

## 5. Evaluation Metrics

The main evaluation metrics are:

* Per-class F1 score
* Macro F1 score
* Confusion matrix

Per-class F1 is the most important because it shows how well the model performs on each category individually.

Accuracy alone is not enough. For example, if the model predicts reaction for every example, it could still achieve reasonable accuracy because reaction posts are common. Macro F1 prevents this by giving equal importance to all classes.

Additional metrics include:

* Precision and recall for each class
* Comparison against a zero-shot LLM baseline

---

## 6. Definition of Success

The classifier is considered useful if it can reliably separate higher-signal categories such as analysis and hot_take from lower-signal categories like reaction and recommendation.

### Target metrics

| Metric                    | Minimum | Target |
| ------------------------- | ------- | ------ |
| Accuracy                  | 65%     | 75%    |
| Macro F1                  | 0.60    | 0.70   |
| Per-class F1              | 0.55    | 0.65   |
| Improvement over baseline | +10%    | +15%   |

If any class receives an F1 score below 0.40, that label would not be considered reliable enough for real use.

These thresholds were chosen because strong performance across all four labels is more important than high overall accuracy.

---

## 7. AI Tool Plan

### Label stress testing

Before beginning annotation, I plan to use Claude to generate examples that fall between hot_take and analysis. If these examples are difficult to classify, I can adjust the definitions before labeling real data.

Prompt:

> "Here are four label definitions for a TV discourse classifier. Generate 10 posts that are genuinely ambiguous between hot_take and analysis. Do not provide the labels."

---

### Annotation assistance

After labeling approximately 50 examples myself, I plan to use Claude to pre-label another batch of around 50 examples.
Every label will still be reviewed manually. The source column in the CSV will indicate whether the example was labeled by a human or pre-labeled by an LLM.
If the correction rate exceeds approximately 15%, I will stop using pre-labeling and continue annotating manually.

---

### Failure analysis

After training the model, I will provide the misclassified examples to Claude and ask it to identify possible patterns in the errors.
Any patterns it identifies will be verified manually before being included in the final report.

Possible issues include:
* Short posts containing limited information
* Prestige television discussions that naturally sound analytical
* Sarcastic posts being confused with reactions

Both useful and discarded observations will be documented in the evaluation report.
