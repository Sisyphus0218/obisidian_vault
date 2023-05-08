# Week3
## Classification with logistic regression

![](assets/week3/week3-1.png)

![](assets/week3/week3-2.png)

![](assets/week3/week3-3.png)

## Cost function for logistic regression

![](assets/week3/week3-4.png)

![](assets/week3/week3-5.png)

## Gradient descent for logistic regression

![](assets/week3/week3-6.png)

## The problem of overfitting

![](assets/week3/week3-7.png)

![](assets/week3/week3-8.png)

Addressing Overfitting
1. Collect more data
2. Select features（相当于让某些 w 为 0）
	- Feature selection
3. Reduce size of parameters（让某些 w 很小）
	- Regularization

![](assets/week3/week3-9.png)

$\lambda$ 较大时，为了最小化 $J$，$w_j$ 会减小

Regularized Linear Regression

![](assets/week3/week3-10.png)

![](assets/week3/week3-11.png)

Regularized Logistic Regression

![](assets/week3/week3-12.png)