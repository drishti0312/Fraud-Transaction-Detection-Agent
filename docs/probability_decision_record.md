# Probability Decision Record

## Case: FREQ_C0386

### 1. Evidence

The agent observed the following evidence for the transaction:

* Transaction amount: ₹721.74
* Location: Delhi
* Merchant: Myntra
* Amount unusual: No
* Location unusual: No
* Merchant unusual: No
* Frequency unusual: **Yes**
* Velocity unusual: No

The only unusual signal was the transaction frequency.

### 2. Hidden States

The agent considers two possible hidden states:

* Legitimate
* Fraudulent

The actual fraud label is not used by the agent when making the decision. It is used only for evaluation.

### 3. Initial Beliefs

Based on the available evidence, the agent estimated:

* Legitimate: **45.79%**
* Fraudulent: **54.21%**

The probabilities sum to 100%.

### 4. Available Actions and Initial Costs

The agent considered four possible actions:

| Action   | Expected Cost |
| -------- | ------------: |
| Approve  |       54.2129 |
| Question |    **6.3370** |
| Examine  |        6.3736 |
| Decline  |        9.1574 |

The agent follows a cost-sensitive decision rule and selects the action with the lowest expected cost.

### 5. Initial Decision

The lowest-cost action was **Question**.

Although the estimated probability of fraud was slightly higher than the probability of legitimacy, immediately declining the transaction was not the lowest-cost option.

The agent therefore asked the customer for additional information.

### 6. New Evidence

The customer did **not** confirm the transaction.

The response was:

**False**

This response was treated as new evidence and used to update the fraud belief.

### 7. Updated Beliefs

After incorporating the customer response, the agent updated its beliefs to:

* Legitimate: **4.48%**
* Fraudulent: **95.52%**

This substantially reduced the uncertainty about the transaction.

### 8. Updated Action Costs

After updating the belief, the expected costs became:

| Action   | Expected Cost |
| -------- | ------------: |
| Approve  |       95.5182 |
| Question |        9.6415 |
| Examine  |        5.1345 |
| Decline  |    **0.8964** |

### 9. Final Decision

The lowest-cost action after receiving the new evidence was **Decline**.

Therefore, the agent changed its decision:

**Question → Decline**

### 10. Decision Rationale

This case demonstrates how the agent uses probabilistic reasoning and active information gathering.

Initially, the agent was uncertain because the transaction had only one unusual signal. Instead of making an immediate high-cost decision, it asked for additional information.

The customer's negative response substantially increased the estimated probability of fraud from **54.21% to 95.52%**. After updating the belief, declining the transaction became the lowest-cost action.

The case demonstrates the agent's decision loop:

**Evidence → Belief → Cost-sensitive Action → New Evidence → Updated Belief → Updated Action**

### 11. Audit Information

For evaluation purposes, the actual transaction label was **fraudulent**. This ground-truth label was not provided to the agent during its decision-making process.
