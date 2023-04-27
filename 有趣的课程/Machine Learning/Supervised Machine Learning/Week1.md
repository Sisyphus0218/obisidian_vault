# Week 1
## Supervised vs Unsupervised Machine learning
### Supervised learning
Learns from data labeled with the <font color="#d99694">"right answers"</font>

Regression: House Price Prediction

![](assets/week1-1.png)

Classification: Breast Canser Detection

![](assets/week1-2.png)

| Regression                       | Classification                     |
| -------------------------------- | ---------------------------------- |
| predict a number                 | predict categories                 |
| infinitely many possible outputs | small number   of possible outputs |

### Unsupervised learning
Find something interesting in <font color="#d99694">unlabeled</font> data

Clusting

![](assets/week1-3.png)

## Regression Model

![](assets/week1-4.png)

Training set : Data used to train the model

Notation:  
x : "input" variable (feature)  
y : "output" variable (target)  
(x, y) : single training example  
$(x^{(i)},y^{(i)})$ : $i^{th}$ training example  
m : number of training examples

![](assets/week1-5.png)

How to represent f ?  
linear regression

## Cost Function
$J(w,b) = \frac{1}{2m} \sum^m_{i=1} (f_{w,b} (x^{(i)}) - y^{(i)})^2$  
goal : minimize J (w, b)

why 2m? why not just m?  
之后的梯度下降求导时，后面的平方求导会得一个 2，正好可以约掉，更好看

![](assets/week1-6.png)

![](assets/week1-7.png)

## Train the model with gradient descent

![](assets/week1-8.png)

![](assets/week1-9.png)

![](assets/week1-10.png)

if $\alpha$ is too small, gradient descent may be slow  
if $\alpha$ is too large, gradient descent may overshoot, never reach minimum

Gradient Descent for <font color="#d99694">Linear</font> Regression

![](assets/week1-11.png)

If J is a <font color="#d99694">convex function</font> (bowl shape), it has only one <font color="#d99694">local minimum</font>  
Some functions have <font color="#d99694">more than one local minimum</font>

![](assets/week1-12.png)

"Batch" gradient descent  
"Batch" : Each step of gradient descent uses <font color="#d99694">all</font> the training examples.

There are other versions of gradient descent that do not look at the entire training set, but instead looks at <font color="#d99694">smaller subsets</font> of the training data at each update step.