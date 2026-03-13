# Credit Card Fraud Detection

By Paul Sentongo

---

## What this project does

Every day, billions of credit card transactions happen around the world. Most are completely legitimate. A small fraction are not. The challenge is that fraudulent transactions look almost identical to real ones on the surface, and by the time a human reviews a suspicious charge, the money is often already gone.

This project builds a machine learning system that reads the details of a credit card transaction and decides, in milliseconds, whether it looks fraudulent. The system was trained on 8,000 labelled transactions and tested using five different machine learning algorithms to find out which one catches the most fraud with the fewest mistakes.

---

## The dataset

The dataset contains 8,000 credit card transactions, each with 20 pieces of information attached. Things like the amount charged, what type of card was used, what device the transaction came from, what time of day it happened, and whether the bank actually approved or declined it at the point of sale. Each transaction is also labelled as either fraudulent or legitimate, which is what the models learn from.

The dataset is nearly evenly split between fraud and legitimate transactions, which is unusual in the real world but makes it straightforward to train and evaluate the models fairly.

---

## How the models work

Rather than relying on a single approach, this project trains five different models and compares them against each other. Each one looks at the same data but uses a different strategy to find patterns.

**Logistic Regression** is the simplest model here. It draws a straight line through the data and puts transactions on one side or the other. It is fast and easy to understand, and it sets the benchmark that every other model has to beat.

**Random Forest** builds hundreds of decision trees, each trained on a slightly different slice of the data. A transaction passes through all those trees and the final answer is a vote. Because the trees were trained differently and therefore disagree with each other in different ways, their individual mistakes tend to cancel out.

**Gradient Boosting** also builds many trees, but differently. Each new tree focuses specifically on the transactions the previous trees got wrong. It learns from its own mistakes iteratively. This usually makes it more accurate than Random Forest, though slower to train.

**XGBoost** is a highly optimised version of gradient boosting that dominated data science competitions for several years. It trains much faster than the sklearn version, includes built-in safeguards against overfitting, and handles missing data on its own without needing any special treatment.

**LightGBM** is Microsoft's take on gradient boosting and it is the fastest of the five. While most gradient boosting tools build trees one full level at a time, LightGBM always picks the single branch that will reduce the error the most and expands only that one. This sounds like a small difference, but it produces more precise trees with fewer total splits and generally achieves the highest accuracy on tabular datasets like this one.

---

## What the analysis revealed

The single most important piece of information turned out to be something the original version of this notebook completely ignored: the **Transaction Response Code**.

When a credit card terminal processes a payment, the bank sends back a response code. A code of 0 means the transaction was approved normally. A code of 5 means the bank declined it. A code of 12 means it was flagged as invalid. Fraudsters attempting to drain a stolen card often trigger multiple declines before a successful charge, and those response codes leave a very clear trail in the data.

Once that feature was included alongside about ten other signals, including time of day, whether the transaction happened at night or on a weekend, how many previous transactions the card had on record, and whether the card is close to its expiry date, accuracy improved dramatically across all five models.

The second most useful signal was the hour of the transaction. Fraud concentrates significantly in the early hours of the morning, roughly between midnight and six in the morning, when bank monitoring is at its lightest and fraudsters know it.

---

## How to run this project

You need Python 3.8 or newer. Open a terminal in the project folder and run the following:

```bash
python -m venv venv
venv\Scripts\activate
pip install pandas numpy matplotlib seaborn scikit-learn imbalanced-learn xgboost lightgbm jupyter
jupyter notebook credit_card_fraud_detection.ipynb
```

Then run all the cells from top to bottom. The whole pipeline from loading the data to producing the final comparison charts takes a few minutes on a standard laptop, mostly because of the tuning step where each model is tested across many settings to find the best configuration.

The dataset file `credit_card_fraud.csv` needs to be in the same folder as the notebook.

---

## What you will see when you run it

The notebook walks through every step in plain language with charts at each stage. You will see how fraud and legitimate transactions distribute across different card types, devices, and hours of the day. You will see all five models trained and tuned. Then you will see them compared against each other using ROC curves, Precision-Recall curves, confusion matrices, and a side-by-side bar chart that makes it easy to see at a glance which model performs best.

The feature importance section shows, for each of the five models, which pieces of information mattered most when making their decisions. A heatmap at the end pulls this together across all the tree-based models to show a consensus view of what actually drives fraud in this dataset.

---

## Results

LightGBM and XGBoost come out on top. Logistic Regression is the weakest but still a useful baseline because it is transparent and fast. The gap between the worst and the best model is real and consistent across all the metrics used.

The most important takeaway is not which algorithm won. It is that better features drove more of the improvement than switching algorithms did. Adding the Transaction Response Code alone accounted for more accuracy gain than the switch from Random Forest to Gradient Boosting. In any fraud detection project, finding the right signals in the data matters more than choosing between machine learning libraries.

---

## Files in this project

`credit_card_fraud_detection.ipynb` is the main notebook with all the analysis, visualisations, and model training.

`credit_card_fraud.csv` is the dataset and needs to stay in the same folder as the notebook.

`venv/` is the local Python environment folder created during setup.

---

## Author

Paul Sentongo
