# Machine Learning: The High-Interest Credit Card of Technical Debt

## [[First Pass]]
### Category
This paper is commentary of [[Tech Debt| tech debt]] associated with [[Data Sciece| data science]] and [[Machine Learning| machine learning]] systems.

### Context
***NA***

### Correctness
The assumptions seem to be valid, as this is a commentary, not a scientific paper.

### Contributions
A well though out commentary of tech debt in [[Machine Learning| machine learning]].

### Clarity
The paper does seem to be well written.

## [[Second Pass]]
### Machine Learning and Complex Systems
- In the paper, [[Tech Debt| technical debt]] is described as:
> Real world software engineers are often faced with the challenge of moving quickly to ship new products or services
- This can lead to the dilemma of speed of execution and quality of engineering.
- 'Traditional' methods of paying off debt include:
	- Refactoring
	- Increasing coverage of unit tests
	- Deleting dead code
	- Reducing dependencies
	- Tightening APIs
	- Improving documentation
- Debt is not necessarily bad, like with fiscal debt, but it tends to compound.

One of the basic premises of the paper is that machine learning packages have the same basic code complexity issues as 'normal' code. Such packages can also create larger system-level complexity, leading to the creation of hidden debt. Because of this, the traditional methods of paying off debt is time well spent but does not necessarily address debt at the systems level.

This paper focuses on the system-level interactions between [[Machine Learning| machine learning]] code and larger systems, where this hidden tech debt may rapidly accumulate. Furthermore, at a system-level, [[Machine Learning| ML]] may:
- 'Subtly' erode [[Abstraction Boundries| abstraction boundries]].
- Create unintended tight coupling of otherwise disjoint systems by re-using input signals.
- Result in large masses of "glue code" or calibration layers that lock in assumptions as [[Machine Learning| ML]] packages may often be treated as [[Machine Learning Black Boxes| black boxes]].

Changes in the external world may make models or input signals change behavior in unintended ways. This will ratchet up maintenance cost and the burden of any debt.

The intro finished off with:
> Paying down technical debt may initially appear less glamorous than research results usually reported in academic ML conferences. But it is critical for long-term system health and enables algorithmic advances and other cutting-edge improvements.

### Complex Models Erode Boundaries
Strong [[Abstraction Boundries| abstraction boundries]] helps to create maintainable code which is easy to make isolated changes and improvements. It does this though encapsulation and modular design. Furthermore, these boundaries help to express the invariant and logical consistency of the information inputs and outputs form a given components. [[Machine Learning| ML]] erodes such boundries the very reason for a [[Machine Learning| machine learning]] system is precisely that:
> desired behavior cannot be effectively implemented in software logic without dependency on external data

#### Entanglement
Machine learning libraries are tools for mixing data sources together. This makes isolation of improvements practically impossible from entanglement. If we have a system with features $x_1, ..., x_n$:
- If the input distribution of $x_1$ were to change, the remaining $n - 1$ features may all change.
- Adding a new feature $x_{n + 1}$ can cause similar changes.
- Removing a feature $x_j$ will also do the same.
- The same principle applies to [[Hyper-parameters| hyper paramters]].

No features are ever really independent. The paper calls this the **CACE** principle: **C**hanging **A**nything **C**hanges **E**verything.
 
 The paper suggests three strategies to mitigate such entanglement:
 - Isolate models and serve ensembles.
	 - This is useful when sub-problems decompose naturally or where the cost of maintaining separate models is outweighed by the benefits of enforced modularity.
	 - The downside is that this can be unscalable in many large-scale settings and within a given model, the issues of entanglement may still be present.
 - Develop methods of gaining deep insights into the behaviour of the model predictions.
- Attempt to use more sophisticated regularization methods to enforce that any changes in prediction performance carry a cost in the objecting function used in training.
	- A downside here is that this may add more debt via increased system complexity than is reduced via decreased entanglement.

> The above mitigation strategies may help, but this issue of entanglement is in some sense innate to machine learning, regardless of the particular learning algorithm being used. In practice, this all too often means that shipping the first version of a machine learning system is easy, but that making subsequent improvements is unexpectedly difficult. This consideration should be weighed carefully against deadline pressures for version 1.0 of any ML system.

#### Hidden Feedback Loops
A system predicting (CTR) of headlines on a website likely relies on user clicks as training labels. This will in turn depend on previous predictions from the model. This leads to issues in analyzing system performance. As an example, if we have $x_{week}$ as a feature, reporting on the number of headlines a user has clicked on in the past week. If the CTR model has improved, it is likely a user will have clicked on more headlines. Furthermore, if the model is updated on new data at a later time, it may change its opinion of $x_{week}$. In this environment, the system will slowly change over time in time scales much longer than a week. These may not be visible in quick experiments. This will lead to added cost to even simple improvements as analysing the effect of a proposed change can be extremely difficult.

Type:  
#paper
Tags:  
#learning
Author(s):  
[[D. Sculley]], [[Gary Holt]], [[Daniel Golovin]], [[Eugene Davydov]], [[Todd Phillips]], [[Dietmar Ebner]], [[Vinay Chaudhary]], [[Michael Young]]
Related:  
[[Data Sciece]], [[Tech Debt]],  [[Data]], [[Google]], [[Machine Learning]]
References:  
[https://research.google/pubs/pub43146](https://research.google/pubs/pub43146 "https://research.google/pubs/pub43146")
Date Accessed:  
2021-09-20