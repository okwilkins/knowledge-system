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