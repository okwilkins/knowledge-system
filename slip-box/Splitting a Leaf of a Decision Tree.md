Zettelcasten Index: 20230227115732-c
Sequence: [[Preventing Decision Tree Overfitting]]
Status: #idea
Zettelcasten Tags: [[Data Science]], [[Machine Learning]], [[Artificial Intelligence]], [[Decision Trees]], [[Decision Trees Branches And Nodes]], [[Gini Impurity of Decision Tree Leaves]]

---

A leaf can be split by comparing features and if they reduce the gini impurity of a [[Decision Trees Branches And Nodes|branch]]. The feature generates the lowest gini impurity for that branch is chosen.

Example:

```mermaid
flowchart TD
    A[Loves popcorn]
    C[Does not love the song\nGini impurity: 0.3]
    D[Loves the song\nGini impurity: 0.48]

    A --> |True| C
    A --> |False| D
```

Checking if splitting the right node via `loves cats` would reduce the branch gini impurity:
```mermaid
flowchart TD
    A[Loves popcorn]
    C[Does not love the song\nGini impurity: 0.3]
    D[Loves cats\nGini impurity originally: 0.48\nGini impurity of branch: 0.42]
    E[Loves the song\nSamples: 50\nGini impurity: 0.4]
    F[Does not love the song\nSamples: 10\nGini impurity: 0.5]

    A --> |True| D
    A --> |False| C

    D --> |True| E
    D --> |False| F
```
The node would be split into a branch as the gini impurity of the branch is lower.

## References
- [[StatQuest with Josh Starmer#Decision Trees]]
