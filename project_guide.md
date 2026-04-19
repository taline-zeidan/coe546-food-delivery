COE 546/746 — Term Project

**User Behavior Prediction in Food Delivery Applications**

*Team: Taline · Farah · Lama*

Kaggle deadline: April 22, 2026  |  Report deadline: April 30, 2026

# **1\. What Is This Project?**

We are participating in a Kaggle machine learning competition. The goal is to build a model that predicts whether a user will place a food order during a session on a delivery app.

We are given data from thousands of real user sessions. Each row describes one session: what the user did, what was in their cart, what promotional offers they saw, and how they responded. We use this data to train a model, and then apply that model to new, unseen sessions to predict the outcome.

The final output is a **probability** between 0 and 1 for each session e.g. 0.87 means the model thinks there is an 87% chance the user placed an order.

Please join the competition on this link: [https://www.kaggle.com/competitions/user-behavior-prediction-in-food-delivery-applications/data](https://www.kaggle.com/competitions/user-behavior-prediction-in-food-delivery-applications/data)

# **2\. The Data We Have**

We have one training file (train.csv) that contains approximately 297,000 sessions. We also have a test file (test.csv) with \~100,000 sessions that has no labels. These are what we predict and submit to Kaggle.

## **What Each Row Looks Like**

Each row \= one user session on the food delivery app. Here is what each column means:

| Feature | What it means | Why it matters |
| :---- | :---- | :---- |
| f2 | Unique Session ID | Identifies each session — used as an ID, not a real feature |
| f3 | Session start time | When the user opened the app |
| f4 | Session end time | When the session ended |
| f5 | Last activity time | Last action taken — gap between f5 and f4 hints at disengagement |
| f6 | Time zone | Geographic context |
| f7 | Action type (browsing, interaction) | What the user was doing in the session |
| f8 | \# of promo offers declined | Higher \= less interested in promos |
| f9 | Customer type: new or returning | Returning users may behave differently |
| f10 | \# items added to cart | Strong signal — more items \= more likely to order |
| f11 | Cart total value | Higher cart value \= stronger intent |
| f12 | Promotion type (F / P / S / C) | F=fixed discount, P=item discount, S=spend-more, C=checkout |
| f13 | Discount value | How much the promo saves the user |
| f14 | Minimum spend to qualify for promo | If cart \< f14, promo doesn't apply |
| f15 | \# of promos shown | How many offers the user saw |
| f16 | Device screen size | Device type context |
| f17 | User response to promo (accepted/declined/ignored) | Very strong signal of purchase intent |
| **order\_placed** | **TARGET — did the user place an order? (1 \= yes, 0 \= no)** | **This is what we predict** |

## **Key Things to Notice About the Data**

* f3, f4, f5 are timestamps. We will extract useful things like session duration and inactivity time from them.

* f10 and f11 (cart items and cart value) are likely very strong predictors. A full cart suggests purchase intent.

* f17 (user response to promo) is probably the most informative single feature. Someone who accepted a promo is far more likely to order.

* f9 (new vs returning customer) and f12 (promotion type) **are categorical and need to be encoded before the model can use them.**

* The target column order\_placed only exists in the training data. In the test data, this is what we predict.

# **3\. How We Are Ranked: ROC AUC Explained**

## **The Simple Explanation**

Our model outputs a probability for each session. Kaggle takes all our predictions and asks a simple question:

*"If I randomly pick one session that DID result in an order, and one that DID NOT, how often does your model correctly give the order session a higher probability?"*

That percentage is the AUC score. An AUC of 0.97 means 97% of the time, the model correctly ranks an order session above a non-order session. That is excellent.

## **The Score Scale**

* 1.0 — Perfect model. Always ranks correctly.

* 0.97 — Excellent. This is roughly where the top teams on our leaderboard are.

* 0.5 — No better than random guessing.

* 0.0 — Perfectly wrong (worse than random).

Our target is to get as close to the top of the leaderboard as possible. The current top score is 0.97188.

## **Why Probabilities, Not 0/1 Labels?**

AUC works by sliding a threshold across all predicted probabilities and measuring performance at each threshold. If we submitted binary 0/1 labels, Kaggle would only have one point to evaluate — it cannot compute a curve. We must submit raw probabilities (e.g. 0.83, 0.12, 0.67).

## **Public vs. Private Leaderboard**

During the competition, Kaggle shows a Public score based on \~30% of the test data. After the deadline, the final Private score is revealed using the remaining 70%. The private score is what counts for our grade. This means we should not over-optimize just for the public leaderboard — we need a model that generalizes well.

# **4\. Our Modeling Approach \[NOT CONFIRMED YET\]**

## **Why Gradient Boosting?**

Looking at the leaderboard, the top scores (\~0.97) are achievable only with powerful models. Based on the type of data we have (tabular, mixed feature types, moderate size), the standard approach in competitive ML is gradient boosting, specifically LightGBM or XGBoost. These models:

* Handle a mix of numeric and categorical features naturally

* Do not require heavy preprocessing or normalization

* Are very fast to train even on 300,000 rows

* Consistently win tabular ML competitions

We will start with a simple baseline (Logistic Regression), then move to LightGBM as our primary model.

## **The Machine Learning Workflow**

Here is the full sequence of steps we will follow:

| \# | Phase | Owner | Description |
| :---- | :---- | :---- | :---- |
| 1 | Data Exploration (EDA) | **Taline** | Load all data, check for missing values, visualize distributions, understand target balance |
| 2 | Data Preprocessing | **Farah** | Handle missing values, encode text columns, parse timestamps into usable numbers |
| 3 | Feature Engineering | **Lama** | Create new features: session duration, cart-vs-threshold ratio, inactivity time, promo engagement |
| 4 | Modeling | **Taline** | Train baseline and advanced models (LightGBM/XGBoost), compare using AUC |
| 5 | Tuning & Submission | **Taline** | Tune best model, generate prediction CSV, submit to Kaggle |
| 6 | Report Writing | **Lama \+ Farah** | Write the 4-page report covering EDA, preprocessing, modeling, and results |
| 7 | Presentation | **All** | Build slides summarizing the approach, results, and key findings |

## **Feature Engineering Highlights**

Beyond the raw columns, we will create new features that capture richer signals:

* **f4 \- f3: total time spent in the app:** Session duration

* **f4 \- f5: how long since the last action. A long gap may suggest disengagement:** Inactivity gap

* **f11 \- f14: how far the cart value is from the promo threshold. Close to threshold may push user to order:** Promo gap

* **extracted from f3. Ordering behavior changes throughout the day and week:** Time of day / day of week

* **f11 / f14: a ratio that captures promo eligibility context:** Cart-to-threshold ratio

## **Validation Strategy**

We will use K-Fold cross-validation (typically 5 folds) to estimate our AUC score locally before submitting to Kaggle. This gives us a reliable estimate without wasting our 3 daily submission limit.

# **5\. What We Need to Deliver**

* Kaggle submission (CSV file): id \+ order\_placed probability for all \~100K test rows — due April 22, 2026

* Clean Jupyter notebook: all code from EDA to final model, well-commented and organized

* Written report (max 4 pages PDF): EDA findings, preprocessing, modeling approach, results — due April 30, 2026

* PowerPoint presentation: clear summary of our approach and results

## **Submission Format**

The CSV we submit to Kaggle must have exactly two columns: id (the session ID) and order\_placed (our predicted probability). Example:

id,order\_placed0,0.821,0.152,0.91...

# **6\. Important Rules**

* Do NOT share code or results with other teams. This lowers our Kaggle score and grade.

* AI tools (like ChatGPT/Claude) may only be used for grammar/formatting help, not to generate full sections of the report.

* We are limited to 3 Kaggle submissions per day as a team.

* Up to 2 submissions count toward our final leaderboard score. Kaggle auto-selects our best two if we do not choose.

