# Credit Card Transaction Decision Agent

## 1. Problem Statement

This project explores how an AI agent can make credit-card transaction decisions when the true transaction state—legitimate or fraudulent—is unknown at decision time.

The agent compares a transaction with the customer's historical behaviour, estimates fraud probability, considers the expected cost of available actions, and requests additional information when appropriate.

The project focuses on decision-making under uncertainty rather than fraud classification alone. It is an experimental research prototype, not a production-ready fraud detection system.

## 2. Project Objectives

The agent is designed to:

1. Observe a new transaction and compare it with customer history.
2. Generate evidence about unusual transaction behaviour.
3. Estimate the probability of fraud.
4. Select an action using expected action costs.
5. Ask for additional information when appropriate.
6. Update its fraud belief after receiving a simulated customer response.
7. make a final decision or escalate unresolved cases to Human Investigation.
8. Evaluate its decisions using held-out test data.
9. Measure uncertainty using entropy and realized entropy reduction.

## 3. Agent Design

### Observable information

The agent uses transaction information and customer history, including:

* Transaction amount
* Merchant
* Location
* Timestamp
* Previous transaction behaviour
* Customer home location, where available

### Hidden state

The implemented probability model uses two hidden states:

* Legitimate
* Fraudulent

The true fraud label is not used to calculate the agent's initial belief. It is retained for evaluation and is also used by the synthetic customer-response simulator, as explained in the limitations.

### Available actions

| Action                  | Purpose                                              |
| ----------------------- | ---------------------------------------------------- |
| **Approve**             | Allow a transaction when its expected cost is lowest |
| **Question**            | Request additional information from the customer     |
| **Examine**             | Escalate a transaction for further examination       |
| **Decline**             | Stop a transaction when its expected cost is lowest  |
| **Human Investigation** | Escalate an unresolved case for human review         |

## 4. Evidence Signals

The agent evaluates five evidence signals against customer history:

* **Amount:** Checks whether the transaction amount is more than four times the customer's historical average.
* **Location:** Checks whether the transaction location has appeared in the customer's previous transaction history.
* **Merchant:** Checks whether the merchant has appeared in the customer's previous transaction history.
* **Frequency:** Compares the time gap between transactions with the customer's historical transaction gaps.
* **Velocity:** Checks whether at least three previous transactions occurred within the preceding 30 minutes.

The evidence is represented primarily as Boolean unusual/not-unusual signals. Missing information is handled separately where applicable.

## 5. Probability and Decision Process

The agent uses a Bayesian-style probability model with a design prior of 5% fraud. Evidence likelihoods are used to update the fraud belief.

The evidence likelihoods are combined under a conditional-independence assumption. This simplifies the model and may not reflect real-world dependencies between transaction signals.

The agent calculates expected costs for Approve, Question, Examine, and Decline, then selects the action with the lowest expected cost. If the initial action is Question, the agent can request additional information and update its belief.

The Week 2 flow is:

```text
Transaction
    ↓
Customer History
    ↓
Generate Evidence
    ↓
Estimate Fraud Belief
    ↓
Calculate Action Costs
    ↓
Select Initial Action
    ↓
If Question: Request Information
    ↓
Simulated Customer Response
    ↓
Update Fraud Belief and Recalculate Costs
    ↓
Make Final Decision
    ↓
Human Investigation if Unresolved
    ↓
Evaluate and Measure Uncertainty
```

The cost-based policy is the main decision approach. A threshold-based policy was also explored as an alternative in the earlier experiments.

## 6. Dataset and Experimental Setup

The project uses a synthetic dataset generated for experimentation.

The original dataset-generation configuration specified:

* 1,000 customers
* 10,000 normal transactions
* 2,000 fraudulent transactions
* 12,000 transactions in total

The Week 2 notebook uses an 80/20 train-test split with `random_state=42`. The split is not stratified by the fraud label. Evaluation is performed on the held-out test set, which contains 1,991 legitimate and 409 fraudulent transactions (2,400 transactions total).

The agent's fraud decisions are evaluated against the true labels only after the decisions are made. The synthetic customer-response simulator is an exception: it uses the hidden label to generate a response for the experiment. This limitation is documented below.

## 7. Week 2 Results

### Performance metrics

| Metric    | Result |
| --------- | -----: |
| Accuracy  | 98.92% |
| Precision | 98.98% |
| Recall    | 94.62% |
| F1 score  | 96.75% |

### Final action counts

| Final action        | Transactions |
| ------------------- | -----------: |
| Approve             |        1,931 |
| Decline             |          391 |
| Examine             |            9 |
| Human Investigation |           69 |
| **Total**           |    **2,400** |

### Fraud labels by final action

| Final action        | Legitimate | Fraudulent |
| ------------------- | ---------: | ---------: |
| Approve             |      1,931 |          0 |
| Decline             |          4 |        387 |
| Examine             |          2 |          7 |
| Human Investigation |         54 |         15 |
| **Total**           |  **1,991** |    **409** |

These results describe this synthetic experiment and should not be interpreted as expected performance on real payment transactions.

## 8. Uncertainty and Information Gathering

The Week 2 agent measures uncertainty using binary entropy.

For transactions that receive a simulated customer response, the notebook reports:

| Measure                             | Result |
| ----------------------------------- | -----: |
| Transactions with customer response |    223 |
| Average initial entropy             | 0.9833 |
| Average updated entropy             | 0.3651 |
| Average realized entropy reduction  | 0.6182 |

Realized entropy reduction is the difference between entropy before and after the observed response. It is a retrospective measure, not a formal estimate of expected information gain before asking a question.

## 9. Probability Decision Record

The detailed Probability Decision Record uses transaction `FREQ_C0386`.

Initially, the agent estimated a fraud belief of **54.21%** and selected **Question**. After a simulated customer response of **False**, the estimated fraud belief increased to **95.52%**, and the agent selected **Decline**.

The actual fraud label is used for audit and evaluation, not as an input to the agent's belief update.

The full record is maintained in:

`docs/probability_decision_record.md`

## 10. Limitations and Responsible Use

### Synthetic data

The generated dataset cannot represent all the patterns, behaviours, and complexities of real payment transactions.

### Simplified evidence and probability model

Most evidence signals are Boolean, and the probability model assumes conditional independence. Both choices limit how much detail the model can capture.

### Design prior and action costs

The 5% fraud prior and action-cost values are experimental assumptions. They have not been established as appropriate costs for a real financial institution.

### Simulated customer responses

Customer responses are simulated using the hidden fraud label. This creates an oracle-based limitation: the simulator has access to information that a real customer-response process would not directly provide. The results therefore do not establish how the agent would perform with actual customer feedback.

### Delayed labels and concept drift

Real fraud labels may arrive much later through disputes or investigations. Transaction patterns can also change over time. The current prototype does not fully model delayed feedback or monitor concept drift.

### Not production-ready

The agent is intended for experimentation and learning. It requires stronger validation, realistic response data, calibrated probabilities, operational safeguards, and human oversight before any real-world use.

## 11. Project Structure

The current repository includes the following project materials:

```text
credit-card-transaction-agent/
├── discussion/
│   └── discussion-record.md
├── research/
│   └── research-file.md
├── docs/
│   ├── ai_reviews/
│   │   ├── practitioner_review.md
│   │   ├── probability_review.md
│   │   └── preprint_review.md
│   └── probability_decision_record.md
├── paper/
│   └── main.md
├── README.md
└── review-record.md
```

The project also includes the Week 1 and Week 2 notebooks and the CSV datasets in the surrounding `Code/` area of the current workspace.

## 12. How to Run

Requirements include Python and common data-science libraries such as pandas, NumPy, and scikit-learn.

1. Open `Week2_Probabilistic_Fraud_Agen.ipynb` in Jupyter.
2. Confirm that the customer-profile and transaction CSV paths point to the correct files.
3. Run the notebook cells in order.
4. Review the generated evidence, initial beliefs, and initial actions.
5. Run the customer-response simulation and belief-update steps.
6. Review final actions, performance metrics, and the confusion table.
7. Review the entropy analysis and Probability Decision Record.

File paths may need adjustment for your local environment.

## 13. AI and Human Contributions

AI tools supported concept explanations, debugging, exploration of design alternatives, and reviews of the agent and research paper. The project author remains responsible for evaluating suggestions and deciding what to incorporate.

Human discussion and research records are maintained separately in the `discussion/` and `research/` directories.

## 14. Future Work

Potential improvements include:

1. Replace Boolean evidence with richer numerical features.
2. Model dependencies between evidence signals.
3. Replace the oracle-based simulator with realistic, noisy customer feedback.
4. Learn response likelihoods from historical data.
5. Evaluate probability calibration.
6. Model delayed fraud labels and concept drift.
7. Compare against simple baseline models.
8. Test alternative action costs and decision policies.
9. Evaluate the value of information before asking questions.
10. Study the trade-off between fraud prevention, customer friction, and human investigation capacity.

## 15. Conclusion

This project demonstrates an experimental transaction decision agent that combines customer history, evidence signals, probabilistic beliefs, cost-sensitive actions, additional information gathering, and human escalation.

The Week 2 experiment illustrates how new evidence can change a fraud belief and lead to a different decision. It also shows why evaluating an agent involves more than measuring classification performance: uncertainty, consequences, and the handling of unresolved cases matter too.

The results are limited to the current synthetic dataset and simulation assumptions. The project is a research prototype for studying decision-making under uncertainty, not a production fraud detection system.
