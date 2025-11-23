Hiver – AI Intern Evaluation Assignment

This repository contains my solution to the Hiver AI Intern Assignment, covering:

• Part A: Email tagging mini-system
• Part B: Sentiment analysis prompt evaluation
• Part C: Mini-RAG for knowledge-base answering

All work is implemented end-to-end in a single notebook file:
hiver_assignment.ipynb

Project Structure

hiver-assignment/
│
├─ hiver_assignment.ipynb
├─ README.md
│
├─ data/
│ ├─ small_dataset.csv
│ └─ large_dataset.csv
│
└─ kb_articles/
├─ automation_setup.txt
├─ csat_missing.txt
├─ create_sla.txt
├─ billing_explained.txt
├─ mail_merge_troubleshooting.txt
├─ workflow_rules.txt
├─ notifications_delay.txt
├─ search_troubleshooting.txt
├─ performance_issues.txt
└─ tag_management.txt

Part A — Email Tagging Mini-System

Approach:

Combined email subject + body into a single text field.

Built one classifier per customer using TF-IDF vectorizer and logistic regression.

Each model only learns from emails belonging to its customer to avoid leakage.

Customer Isolation:

Models stored as a dictionary: customer_models[customer_id]

Allowed tag sets stored per customer: customer_tags[customer_id]

During prediction only the matching customer's model and tag space are used.

Accuracy Improvements using Patterns & Anti-Patterns:

Added a rule layer on top of ML model.

Pattern examples:
“billing”, “invoice” → billing_error
“dark mode”, “feature request” → feature_request
“csat”, “survey” → analytics/csat tags

Anti-pattern:
If a feature request email is classified as a bug → override label.

Error Analysis Summary:

Traditional train/test split gave poor accuracy due to very small dataset per customer.

Switched to evaluating performance on the full dataset with qualitative error observations.

Common failure patterns: semantically similar tags and short ambiguous emails.

3 Improvement Ideas for Production:

Replace TF-IDF with transformer-based embeddings + lightweight classifier.

Use feedback loops to retrain from agent-corrected predictions.

Maintain configurable customer-specific rules and hierarchical tag ontology.

Part B — Sentiment Analysis Prompt Evaluation

Prompt V1:

Returned sentiment (positive/negative/neutral), confidence score, and reasoning.

Reasoning helps debugging but stays internal.

Result: sometimes misclassified neutral bug reports as negative and confidence was not calibrated.

Evaluation:

Tested on 10 real support-style messages.

Logged human label vs model output and correctness.

Prompt V2 Improvements:

Added tone-based scoring (strong vs mild sentiment words).

Added rule: feature requests default to neutral unless expressing gratitude or frustration.

Confidence scaled based on strength of signal.

Evaluation Approach Summary:

Create labeled dataset

Run prompt V1

Compare results and identify failure patterns

Improve prompt

Run V2 and measure improvements

Part C — Mini-RAG for Knowledge Base Answering

Retrieval Approach:

Loaded KB articles from /kb_articles/

Built TF-IDF embedding index

Retrieved top-k relevant documents using cosine similarity

Generated answer by summarizing most relevant article snippet

Benchmark Queries:

“How do I configure automations in Hiver?” → Retrieved automation_setup.txt

“Why is CSAT not appearing?” → Retrieved csat_missing.txt

Each query result includes:

Retrieved article list

Extractive generated answer

Confidence score

Failure Case:

Queries phrased differently than article wording returned low scores.

Debugging Plan:

Add semantic embeddings (Sentence Transformers)

Enable metadata-aware search

Split large articles into sections

Add cross-encoder re-ranking

Collect user feedback to improve ranking over time

How to Run

Option 1: Google Colab (Recommended)

Open notebook hiver_assignment.ipynb

Upload /data and /kb_articles folders

Run all cells

Option 2: Local Environment

Install dependencies:
pip install pandas scikit-learn notebook

Launch: jupyter notebook

Run notebook

Deliverables Coverage

✔ End-to-end runnable solution
✔ Customer-isolated tagging system
✔ Prompt experimentation and refinement
✔ Mini-RAG retrieval with confidence scoring
✔ Error analysis and improvement proposals

Submitted by:
AI Intern Candidate — Hiver Assignment