# Practitioner Review

## 1. Review Scope

This review evaluates the fraud detection agent from a practical fraud-detection and decision-making perspective.

The review focuses on:

* Whether the agent's decisions are practically meaningful
* How uncertainty is handled
* The use of customer interaction as additional evidence
* Cost-sensitive decision making
* Human escalation
* Assumptions that would need to be addressed before production use

---

## 2. Strengths

### 2.1 The agent does more than classify transactions

The agent does not simply label every transaction as legitimate or fraudulent.

It can:

* Approve
* Question
* Examine
* Decline
* Escalate to Human Investigation

This provides more flexibility than a simple binary classification system.

### 2.2 Uncertain transactions can trigger information gathering

The Question action allows the agent to request additional information when the available evidence is not sufficient for a confident decision.

This is practically useful because some transactions may look unusual without necessarily being fraudulent.

### 2.3 Customer responses can change the decision

The agent can use a customer response as new evidence rather than treating the initial decision as final.

For example, transaction `FREQ_C0386` initially had a fraud belief of 54.21%.

The agent selected **Question** because it was the lowest-cost action.

After the customer response, the fraud belief increased to 95.52%, and the agent changed the decision to **Decline**.

This demonstrates adaptive decision-making based on new information.

### 2.4 Human Investigation provides a safety mechanism

Transactions that remain uncertain after additional evidence can be sent for Human Investigation.

This is a practical design choice because automated systems should not be expected to resolve every ambiguous case.

### 2.5 The experiment is reproducible

The project uses a fixed random seed and documents the assumptions used to generate the synthetic data and customer responses.

This makes the experiment easier to reproduce and evaluate.

---

## 3. Practitioner Concerns

### 3.1 Customer responses are simulated

The current experiment does not use real customer interaction data.

Customer responses are simulated using the actual fraud label so that the probabilistic update process can be demonstrated.

The fraud label itself is not provided to the agent when making the decision.

**Status: Accepted as a limitation.**

For a production system, customer-response behavior should be learned from historical interaction data.

---

### 3.2 Customer-response probabilities are assumed

The current model assumes response probabilities rather than learning them from historical data.

For example:

* P(Yes | Fraudulent) = 0.10
* P(Yes | Legitimate) = 0.95

These values are useful for demonstrating Bayesian updating but should not be treated as production estimates.

**Status: Accepted as a limitation.**

---

### 3.3 Action costs are assumed

The action costs used by the agent are manually defined.

For example, approving a fraudulent transaction has a high assumed cost, while questioning a transaction has a smaller cost.

These costs demonstrate cost-sensitive decision making but are not based on actual business loss data.

**Status: Accepted as a limitation.**

In a production environment, costs should reflect actual fraud losses, customer friction, investigation effort, and other operational consequences.

---

### 3.4 Concept drift is not currently monitored

Fraud patterns can change over time.

The current experiment does not include a mechanism for detecting changes in transaction behavior or model performance.

**Status: Deferred.**

Concept drift monitoring should be considered in a future production-oriented version.

---

### 3.5 The current state model is simplified

The implemented probability model uses two hidden states:

* Legitimate
* Fraudulent

Additional states such as "legitimate but unusual" and "unknown" were considered conceptually, but are not separate probability states in the current implementation.

**Status: Deferred.**

A richer state model could be explored in future work if it provides measurable practical value.

---

## 4. Review Findings

| Area                   | Finding                                                         | Status              |
| ---------------------- | --------------------------------------------------------------- | ------------------- |
| Multi-action decisions | More flexible than simple approve/decline classification        | Accepted            |
| Information gathering  | Agent can ask for additional evidence                           | Accepted            |
| Adaptive decisions     | Decisions can change after new evidence                         | Accepted            |
| Human escalation       | Provides a mechanism for unresolved cases                       | Accepted            |
| Reproducibility        | Synthetic experiment uses documented assumptions and fixed seed | Accepted            |
| Customer responses     | Currently simulated                                             | Limitation accepted |
| Response probabilities | Currently assumed                                               | Limitation accepted |
| Action costs           | Currently assumed                                               | Limitation accepted |
| Concept drift          | Not currently monitored                                         | Deferred            |
| Richer hidden states   | Not implemented in current model                                | Deferred            |

---

## 5. Overall Practitioner Assessment

The agent demonstrates a useful approach to fraud decision-making by combining probabilistic beliefs, information gathering, cost-sensitive decisions, and human escalation.

The design is particularly useful for situations where the available transaction evidence is insufficient to make an immediate decision.

However, the current system should be considered a **research prototype rather than a production-ready fraud detection system**.

The main practical limitations are the use of synthetic customer responses, assumed response probabilities, and assumed action costs. These would need to be replaced with real operational data and validated before deployment.

Overall, the agent provides a strong foundation for demonstrating how probabilistic reasoning can improve fraud decision-making while explicitly identifying the assumptions that need to be addressed in future work.
