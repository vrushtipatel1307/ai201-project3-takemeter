# ai201-project3-takemeter
r/television Discourse Classifier

This project uses a fine-tuned DistilBERT model to classify posts and comments from r/television and r/TrueFilm into four different discussion styles: analysis, hot_take, reaction, and recommendation.

Community Choice

I selected r/television as the primary community, with r/TrueFilm used as a secondary source for more analytical examples.

r/television is one of Reddit’s largest communities dedicated to television. Discussions range from detailed breakdowns of storytelling and character development to quick emotional reactions, controversial opinions, and viewing recommendations. A single thread about a popular show can contain all of these discussion styles at once.

Because community members often describe posts as “just a reaction,” “a good analysis,” or “a bad take,” these labels reflect the way people in the community already talk about discussions rather than categories imposed from outside.

Labels
Analysis

Posts in this category provide structured reasoning about writing, characters, themes, acting, or storytelling. These posts usually include examples, scene references, comparisons, or supporting evidence.

Example:

"The final season of Game of Thrones felt rushed because the earlier seasons used travel time to show consequences and build the world. Removing that element weakened the story."

Hot Take

A hot take is a strong or controversial opinion stated confidently but with little supporting evidence.

Example:

"Breaking Bad is overrated. Walter White is just an unlikeable person doing terrible things for five seasons."

Reaction

Reaction posts focus mainly on emotional responses to episodes, scenes, or plot developments.

Example:

"I had to pause after the Red Wedding because I couldn't believe what happened. Even though I knew it was coming, it still shocked me."

Recommendation

These posts either suggest shows to other people or ask for viewing recommendations.

Example:

"If you enjoyed The Bear, you should watch Fleabag. Both shows have chaotic energy and flawed protagonists."

Dataset
Total examples: 200
Sources: r/television, r/TrueFilm, r/movies, and r/MovieRecommendations
Data split:
70% training
15% validation
15% testing
Collection method:
Manual collection
LLM-assisted pre-labeling
Human review and correction
Label Distribution
Label	Count	Percentage
Reaction	66	33%
Hot Take	48	24%
Analysis	44	22%
Recommendation	42	21%

The classes remain relatively balanced, with no category dominating the dataset.

Difficult Cases

The most difficult distinction was between analysis and hot_take.

Many posts make strong claims while also including some supporting details. To keep the labels consistent, I used the following rule:

If the evidence and reasoning still make sense after removing the author's opinions, the post is labeled analysis.
If the post mainly presents an opinion with limited support, it is labeled hot_take.

Another challenging boundary was between reaction and hot_take.

For example:

"This finale was an embarrassment."

Although emotional, this statement is making a judgment about quality rather than expressing feelings about the story itself, so it was labeled hot_take.

Fine-Tuning Setup
Base model: DistilBERT (distilbert-base-uncased)
Training platform: Google Colab (T4 GPU)
Epochs: 3
Learning rate: 2e-5
Batch size: 16

I kept the training at three epochs because the dataset was relatively small. Increasing the number of epochs could have caused the model to memorize the training data rather than learn general patterns.

The low confidence scores on many incorrect predictions suggested that the model was uncertain, indicating that additional training data would likely help more than additional epochs.

Difficult Annotation Examples
1. Obsession Genre Discussion

Decision: Analysis

The post discussed the idea of "semi-elevated horror" and explained the author's reasoning using genre comparisons and ideas about artistic intent. Although it begins with a strong opinion, the argument contains enough evidence and reasoning to qualify as analysis.

2. Emotional Quality Judgment

Decision: Hot Take

"This finale was an embarrassment and the showrunners should be ashamed."

The statement is emotional, but its main purpose is judging the quality of the work rather than expressing feelings about the story.

3. Recommendation with Analysis

Decision: Recommendation

"If you liked The Bear, watch Fleabag."

Although the post compares the two shows, its primary goal is recommending what someone should watch next.

Evaluation Results
Overall Accuracy
Model	Accuracy
Zero-shot LLaMA-3.3-70B	63.3%
Fine-tuned DistilBERT	43.3%

The fine-tuned model performed significantly worse than the zero-shot baseline.

Baseline Performance
Label	Precision	Recall	F1
Analysis	0.62	0.71	0.67
Hot Take	0.40	0.29	0.33
Reaction	0.69	0.90	0.78
Recommendation	0.75	0.50	0.60
DistilBERT Performance
Label	Precision	Recall	F1
Analysis	0.00	0.00	0.00
Hot Take	0.36	0.71	0.48
Reaction	0.50	0.80	0.62
Recommendation	0.00	0.00	0.00

The model never predicted analysis or recommendation on the test set. Instead, it classified nearly everything as either hot_take or reaction.

Failure Analysis

The main issue is that the model learned to recognize tone rather than reasoning structure.

Reaction posts often contain emotional language, while hot takes frequently use confident or opinionated wording. These patterns are easy for the model to identify.

Analysis posts, however, vary widely in style. Some are emotional, some are calm, some are long, and others are short. Their defining characteristic is the presence of reasoning and evidence, which is more difficult for the model to learn from a small dataset.

Recommendation posts face a similar problem because they can appear enthusiastic, analytical, or neutral.

Because reaction posts represented the largest class in the dataset, the model also developed a tendency to default to that category whenever it was uncertain.

Example Misclassification

Text:
"We have entered an interesting phase of horror where we now see semi-elevated horror films..."

True label: Analysis
Predicted label: Hot Take
Confidence: 0.26

The model focused on the confident opening statement and failed to recognize the supporting reasoning that followed.

Text:
"Any other actor would have chosen a regular villain voice. The fact that Bane's voice is so upbeat makes him more intimidating."

True label: Analysis
Predicted label: Reaction
Confidence: 0.27

The model interpreted the enthusiastic language as emotional reaction rather than noticing the comparison and reasoning.

Text:
"You need a 22-episode season to tell a good story."

True label: Analysis
Predicted label: Hot Take
Confidence: 0.27

This example sits directly on the boundary between analysis and hot take. The claim is structural, but the evidence is implied rather than explicitly stated.

Sample Predictions
Post	True Label	Predicted	Confidence
Red Wedding reaction	Reaction	Reaction	0.81
Breaking Bad is overrated	Hot Take	Hot Take	0.74
The Bear → Fleabag recommendation	Recommendation	Reaction	0.41
Nolan visual style discussion	Analysis	Hot Take	0.29
Recommend Evil	Recommendation	Hot Take	0.38

The model performed best on reaction posts because those examples contain clear emotional signals.

Reflection

The goal of this project was to classify posts based on how people communicate: whether they are arguing, reacting, recommending, or expressing opinions.

However, the model learned something slightly different. Instead of recognizing reasoning structures, it mainly learned surface-level language patterns:

Emotional language → Reaction
Strong opinions → Hot Take

As a result, it struggled with analysis and recommendation posts, which often share language patterns with other categories.

The project shows that identifying discourse structure is more difficult than identifying tone, especially with a relatively small dataset.

Spec Reflection

One aspect of the project specification that helped significantly was requiring decision rules before annotation. Defining the boundaries between labels early made the annotation process much more consistent.

One area where my implementation differed from the original plan was class balance. Reaction posts naturally appeared more often in r/television, and I chose to keep that distribution rather than artificially balancing the dataset. Looking back, a more balanced dataset may have helped the model learn the less common categories.

AI Usage
1. Pre-labeling

After manually labeling the first 50 examples, I used Claude to pre-label the remaining posts using my label definitions and decision rules.

I reviewed every suggested label and corrected approximately 15–20% of them, especially cases involving the analysis versus hot_take boundary.

2. Failure Analysis

After training, I provided the misclassified examples to Claude to identify possible patterns.

The most useful observation was that many incorrect predictions involved analysis posts. Another suggestion was that shorter posts caused more mistakes, but after reviewing the data myself, I did not find enough evidence to support that conclusion.

3. Label Stress Testing

Before starting annotation, I asked Claude to generate borderline examples between hot_take and analysis.

Several examples exposed weaknesses in my original definitions, which led me to refine the final decision rule:

A post should only be labeled as analysis if its supporting evidence remains meaningful even after removing the author's opinion language.

This rule became the foundation for consistent annotation throughout the project.
