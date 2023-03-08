Zettelcasten Index: 20230227115732-a1
Sequence: [[Building the Root Node of a Decision Tree With Numerical Features]]
Status: #idea
Zettelcasten Tags: [[Data Science]], [[Machine Learning]], [[Artificial Intelligence]], [[Decision Trees]], [[Gini Impurity of Decision Tree Leaves]], [[Decision Tree Gini Impurity]]

---

The root node of a [[Decision Trees|decision tree]] is built via calculating the [[Decision Tree Gini Impurity|gini impurity]] of each root tree for each feature. The the feature that generates the lowest gini impurity is then chosen as the root.

```mermaid
flowchart TD
    A[Loves popcorn\nGini impurity: 0.32]
    A --> |True| B[Loves the song]
    A --> |False| C[Deos not love the song]
```

```mermaid
flowchart TD
    A[Loves pop\nGini impurity: 0.1]
    A --> |True| B[Loves the song]
    A --> |False| C[Deos not love the song]
```

```mermaid
flowchart TD
    A[Loves cats\nGini impurity: 0.48]
    A --> |True| B[Does not loves the song]
    A --> |False| C[Loves the song]
```

## References
- [[StatQuest with Josh Starmer#Decision Trees]]