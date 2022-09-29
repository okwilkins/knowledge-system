# Bias
The inability for an ML model to capture the true rel is called bias

# Variance
The difference in how well a model fits differing datasets is called variance

# Decicsion Trees
## Basic Concepts
- milti data type ok
- a question on a feature can be asked multiple times
- numerica thresholds can be diff for the same feature
- final classificaitons can be repeated
- if statement is true, you go left
- very top of tree is called root node/the root
- internal nodes/branches are inbetween top and bottom
	- branches have arrows pointing to them
	-  AND arrows pointing away from them
- leaves have arrows pointing to them but no pointing away

## Building a Tree with Gini Impurity
- First need to work out which feature makes the root
	- do this based off which best predicts target var
		- do this by making tree based off only "loves popcorn" to predict "Loves Song"
	- If a **leaf** contains a mixture of correct **AND** incorrect predictions, then they are **IMPURE**
	- To quantify the impurity of leaves you can use: **Gini Impurity**, **Entropy**, and **Information Gain**
	- Gini Impurity for a leaf = 1 - (prob("yes") ^ 2 - prob("no") ^ 2)
		- to work out toal gini impurity fpr the simple tree:
			- = weigthed avg of gini impurities
			- weigthed because a leaf may not represent the same number of people
				- eg "loves popcorn? Yes? No?"
	- For numeric data:
		- sort by low to high
		- calc avg value based of row below and current row
		- then calc gini for each avg value
			- this can be done e.g. avg val = 9.5
			- root: "age < 9.5, loves song?"
		- You then pick the root that represents num feature by the avg that = lowest gini
	- You then pick the root by selecting the feature that has lowest gini!
- So split based off: "loves soda?"
	- Can we reduce gini of branch by splitting people that loved soda or Age < ??? ?
	- Calc gini for each of the two questions
	- Select lowest gini of the two: "age < 12.5?"
- Can stop splitting based off if leaves are completly pure
## How to Prevent Overfitting
- Let say that leaf only has one person, can be hard to be sure that it does a good job of making preds
	- could overfit the data
- Can deal with this by:
	- pruning
	- can put limits on hwo trees grow by requiring 3 or more people per leaf

# Regression Trees
- use when you cant fit straight line to data
- to work out output of leaf, just get avg of the values that sit in the leaf
- building root:
	- dosage < 3 -- 3 is avg of two values
	- get avg of vals of < 3 and >= 3 (0 & 38.8)
		- so tree will say: "Dosage < 3? If so, then 0, else 38.8."
	- use sum of square residuals of all points (left and right) in the leaves to get qual of preds
	- THEN, go to next two points e.g. "dosage < 5"
	- Select smallest sum of squared residuals
		- root = "dosage < 14.5?"
	- You then for the left and right branch can do the same again on the samller subset of values
	- you then need to do this for the other features in data
	- compare SSR for each feature that makes the root node
	- to the branch down, you just do the same as before but it's filtered on e.g. "Age > 50"

# How to Prune Regression Trees
- there are several methods of prunin
	- this is on **Cost Complexity Pruning** or **Weakest Link Pruning**

use this for overfitting and variance
let say we have test/train data and the model is overfit to test data

- one way to reduce overfitting is by reducing the number of leaves
	- replace the split with a leaf that is avg of a larger num of observations
		- e.g. remove two leaves of branch and replace branch by leaf
- now the sub-tree does better at predicting test data but worse at train

how do we decide which sub-tree to use???

- first tep of cost complex pruning is to calc SSR of each pruned sub-tree for trian data
	- calc SSR for each leaf
	- then add up all SSR for all leaves
- each time a branch/leaf is removed, the SSR for the sub-tree will get bigger
	- not a suprise tho ofc
- how comapre trees?
	- CCP based on:
		- tree score = SSR + alpha (tree complexity pen, find using cross validation) * T (num of leaves or terminal nodes)
		- do this for all sub-trees
		- pick lowest tree score sub-tree

- how to build pruned regression tree?
	- first use ALL data build full sized reg tree
	- now increase alpha till the sub-tree with one branch removed, gives lower tree score compared to full sized tree
	- keep doing this with smaller and smaller sub-trees
		- recording alpha values for each
	- in the end, different vals for alpha give use a sequence of tree from full sized to just a leaf
		- e.g. 0, 10k, 15k, 22k
	- now divide into train and test
		- using just train data, use the alpha values we found on full tree and a sequaence of subtrees that min the tree score
		- now calc SSR of each new tree for test data and pick alpha based off lowest SSR
	- repeat using cross-validation
		- pick alpha that on avg gave lowest SSR with the testing data in each cross fold

# Random Forests

Step 1: Create a "**bootstrapped**" dataset
- Randomly sample dataset so that we have same size as orginal dataset
	- can pick same sample more than once

Step 2: Create decision tree using boostrapped dataset but only use a random subset of features at each step
- e.g. only consider 2 cols at each step
- there can be an optimal num of cols

- once root has been determined out of 2 cols, remove that col from data to focus on ALL of the remaining cols
- for next branch, randomly select 2 cols of the remaining cols
	- in the next levels of branches, you can select the col that was removed for the parent node

Now go back to step 1 and repeat bootstrap and build tree again

How to use forest?
- all trees vote and pick answer based off all the trees, 5 Trues and 1 False = True classification

**B**oostrapping the data plus the **agg**regate to make a decision is called **Bagging**

We can have **Out-Of-Bag-Dataset**, which are all the samples that did not make it into the bootstrapped data
- typically 1/3 of og data doesn't make it
- run data through tree and check if it estimate correct
- do this out-of-bag sample through all of the other trees that were built without it
- do the same thing for all of the other out of bag samples for all of the trees

The proportion of out-of-bag samples that were incorrectly calssifies is the **Out-Of-Bag Error**

To tune the number of cols selected for each step, use **Out-of-bag error**

# Adaboost

Trees are usually just a root and two leaves
- This is called a stump
- They are not great at making accurate classifications
- "*weak learners*"

In a forest of stumps, some stumps get more say in final classification than others
- this is in contrast with RF

Order is important
- The error that the first stump makes, influences how the second stump is made, etc etc
- In RF the trees are independently generated

First give each row in dataset a sample weight
- To begin with, this will be 1 / N

Then create root node in the same way as a decision tree

The the **total error** will determine the **amount of say** a stump has:
- amount of say = 0.5 * log((1 - total error) / total error)
- if total error = 0.5, say = 0
- if total error apporaches -1 (you would just do opposite of what tree says) or 1, say increases
- if total error = 1, the equation freaks out (log(0)), so a small error term is added to correct for this

To update the rows that the stump incorrectly guesses:
- new sample weight = sample weight * e ^ (amount of say)
- So if stump has a lot of say and gets guess wrong, sample weight will drastically increase

If the stump gets the guess correct, update the sample weight:
- new sample weight = sample weight * e ^ -(amount of say)
- if amount of say is large, then the row weight will go down a lot

After all weights have been updated, you need to nomalise the weights so that they add up to 1
- do this by dividing each weight by the sum of the weights

In theory, the new sample weights could be used to calc a weighted gini index for a new tree

In practice, you generate rng nums between 0 and 1, selecting the row that it falls on base don the cum sample weights

Do this as many times as you want!

How do the trees make predicition?
- Add up the amount of say of each True and False preds
- The one with more total say will be the pred

# Least Squares Linear Regression

1. Draw line through data
2. Sum pred - obvs for all points
	1. Square each term in sum to negatate large obvs vs pred
	2. This is **sum of squared residuals**

$y = ax + b$ 
We want to find the optimal vaues for a and b so that the sum of squared residuals is minimised
Since we want the line that will give us the smallest sum of squares, this is called ***least squares*** 

Plot graph of roration of line vs SSR and take derivative of the function
Best point is where the derivative is 0

# Linear Regression
Main ideas:
1. Use least-squares to fit a line to the data
2. Calculate $R^2$
3. Calculate a p-value for $R^2$

Look above for notes on least-squares

$R^2$:
Example:
- First shift all data to y-axis
	- We are only interested in y-axis feature
- Then calculate avg. y data
	- Sum the squared resdiduals
	- We call this SS(mean) -> "sum of squares around the mean"
		- SS(mean) = (data - mean)^2
	- Variation around the mean = (data - mean) ^ 2 / n
		- Var(mean) = SS(mean) / n
		- We can think of variance as the average sum of squares per e.g. "mouse"

Now apply this to the line of best fit:
- SS(fit) = (data - line) ^ 2
- Var(fit) = SS(fit) / n
- Can think of Var(fit) as the avg SS(fit) for each mouse

In general: Variance(something) = sum of squares / the number of those things = average sum of squares

There is less variation around the line that we fit by least-squares
- We can say that some for the variation in mouse size is "explained" by taking mouse weight into account

***So $R^2$ tells up how much of the variation in mouse size can be explained by taking mouse weight into account.***

$R^2 = \frac{Var(mean) - Var(fit)}{Var(mean)}$

Example: *$R^2$ = 0.6 = 60%: "There is a 60% reduction in variance when we take the mouse weight into account"* or *"mouse weight explains 60% of the variation in mouse size"*.

You can also use the sums of squares to make the same calc:
$R^2 = \frac{SS(mean) - SS(fit)}{SS(mean)}$

Adding terms will never reduce $R^2$!



# Gradient Descent
Can be used to optimise least squares like in notes above!
For: $y = ax + b$
start with least squares estimate for the slope a
we use gradient descent to find optimatal value for b

set b = 0, any number will do
calc SSR
- NOTE: the SSR is a type of **loss function**

Keep changing the intercept and plot a graph of intercept vs SSR
- Inefficent way of doing this would be to calc lots and lots of points
- Gradient descent makes this way more efficent
	- Does a few calcs when far from the optimal solution
	- increases the number of calcs, close to the optimal value
- You can derive an equation for a line that describes the SSR
- Determine derivative of the function
- Then take steps towards min value approaches derivative = 0
	- This is v useful because get around times where it's impossible for derivative = 0
	- The size of the step should be dependant on the size of the derivative
	- This is determined by a **step size** called the
		- Mutiply the derivative by a small number called a **learning rate**, the combination of these two is the step size
	- So in this case, the new intercept = old intercept - step size
	- This process repeats itself
	- It will stop when the step size is very close to 0

How to do grad descent for both a and b?
- Take derivative with respect to the intercept also do the same with respect to the slope
	- When you have 2 or more derivatives of the same function, they are called a **gradient**
	- This is why the algorithm is called **gradient descent**!
- Pick rando number for intecept and slope
	- Gives 2 SSRs
- Calc step size for both
- Adjust intercept and slope by step size

In general:
1. Take the derivative of the **Loss Function** for each parameter in it. Meaning take the **Gradient** of the **Loss Function**.
2. Pick rando values for all parameters.
3. Plug param values into the gradient
4. Calc step size
5. calculate new params
6. Go back to step 3 and repeat till step size small or max num of steps reached

For a lot of data points, this can take long time
So, **stochastic gradient descent** that uses a rando selected  subset of the data at every step, rather than the full dataset


# Gradient Boosting: Regression Main Ideas

Grad boost very similar to AdaBoost

When starting, grad boost creates a single leaf that is the avg of the target var
Then grad bost builds a tree, that is based on the errors of the previous tree.
- This tree is larger than stump but still restricts the size of the tree
- In practice, people ofen set the num of leaves to be between 8 and 32

Grad boost will scale each tree by a fixed amount, unlike AdaBoost (amount of say)
It will then build tree based of the errors of the previous tree.
It will then build trees in this fashon till made num of trees asked for or aditional trees fail to improve fit.

1. Calc avg of the target feature as first 'tree'/leaf
	1. Better way: $F_{0}(x) = \arg \max_{\gamma}\sum_{i=1}^{n}L(y_{i}, \gamma)$, $L$ is loss func
	2. Either find best value via grad descent or if ez derivative to solve for 0, do that
2. Build tree on errors of first tree: error = obvs - pred, this is called a **pseudo residual**. The pseudo part is a reminder that we are doing grad boost, not linear reg.
3. Now use input vars to predict the residuals to build tree. A leaf of this tree may pointwards two residuals. If this happens, get avg of residuals on leaf.
4. Now combine original leaf with the new tree for pred. Effectively this new tree 'corrects' for the error of the og leaf. After doing this once, the bias will be low but varience high (overfit), so there is a learning rate to correct for this. Multiple learning rate by new tree's residual pred. Between 0 and 1. This results in a small step in the right direction. Taking lots of small steps in the right direction results in lower varience.
5. Calc psuedo residuals again based of new preds and do the same.
6. Keep doing this until num trees reached or the sum of residuals do not significantly decrease.

Gradient boost is called gradient boost because the residual is the **Gradient** that comes from **Gradient descent**.

# Gradient Boost: Classification Main Ideas










