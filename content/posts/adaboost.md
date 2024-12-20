---
title: "Adaboost"
date: 2024-12-04T19:40:07Z
tags: ["machine-learning"]
---
Adaboost is a boosting algorithm for binary classification, although it can be generalised to multi-class classification. A boosted classifier is just a aggregation of weaker learners (e.g. decision trees) where each one is trained on top of the previous one. Each subsequent learner is trained with a set of updated weights for the training samples, where the misclassified sampled are weighted more, or equivalently suppress the weights of the correctly classified samples, to allow the current learner to correct the mistakes made by the previous one. The final prediction is the sum of the outputs of all trees, with its sign indicating which class a sample belongs to, and the value the confidence of the prediction.

<div class="math">
I did not understand why assigning more weights to the misclassified samples would improve the learner since I thought this would misclassify the correct samples in the last round. What I didn't realise the weights for the correct samples were small such that the classifier would give an output close to 0. The aggregated sum becomes something like \(\hat y + 0.01 + -0.05 + ... \simeq \hat y\).
</div>

## Derivation

The following formulation follows the one in [wikipedia](https://en.wikipedia.org/wiki/AdaBoost), nonetheless is useful for myself to write it out.
The aggregated classifier after \(T\) iteration, \(C_T(\bar x)\)[^1], is the sum of the weak learners trained at each iteration \(t\) where

$$ C_{T}(\bar x) = \sum_{t=1}^T c_{t}(\bar x) $$

The final select weak learner \(f_t(\bar x)\) is actually a weighted weak learner at each iteration, such that

$$ c_t(t) = \alpha_t k(\bar x), $$

where \(k(\bar x)\) is the selected weak learner and \(\alpha_t\) the coefficient associated to the weak learner that minimizes the loss function:

$$ E_t = \sum_i E[C_{t-1}(\bar x_i) + \alpha_t k(\bar x_i)] $$

Suppose we have a dataset \(\left\{(\bar x_1, y_1), ..., (\bar x_N, y_N)\right\}\), where \(y \in \left\{-1, 1\right\}\) and a set of weak classifiers \(\left\{k_1, ..., k_L\right\},\) and \(k_j(\bar x_i) \in \left\{1, 1\right\}\). The boosted classifier after \(m-1\) iteration is then:

$$ C_{(m-1)}(\bar x_{i})=\alpha_{1}k_{1}(\bar x_{i})+\cdot\cdot\cdot+\alpha_{m-1}k_{m-1}(x_{i}) $$

It follows that

$$ C_m(x_i) = C_{m-1}(x_i) + \alpha_m k_m (x_i) $$

All we need to do now is to get the best \(k_m\) by minimising the loss function. In Adaboost, we use an exponential loss on each data point

$$ E=\sum_{i=1}^{N}e^{-y_{i}C_{m}(\bar x_{i})}=\sum_{i=1}^{N}e^{-y_{i}C_{(m-1)}(\bar x_{i})}e^{-y_{i}\alpha_{m}k_{m}(\bar x_{i})} $$

If \(C_m(\bar x_i) = y_i\) then the loss will be \(e^{-1}\), otherwise \(e^1\). We can factorise the right and wrong predictions:

$$
\begin{align}
E &= \sum_{y_i=k_m(\bar x_i)} w_{i}^{(m)} e^{-\alpha_m} + \sum_{y_i \neq k_m(\bar x_i)} w_{i}^{(m)} e^{\alpha_m} \nonumber \\
  &= \sum_{i=1}^{N} w_{i}^{(m)} e^{-\alpha_m} + \sum_{y_i \neq k_m(\bar x_i)} w_i^{(m)} (e^{\alpha_m} - e^{-\alpha_m})\nonumber
\end{align}
$$

where \(w_i^{(1)} = 1\) and \(w_{i}^{(m)} = e^{-y_i C_{m-1}(\bar x_i)}\) for \(m > 1\). We also use the fact \(y_i k_m(\bar x_i) = 1\) for the correct predictions.

Differentiating \(E\) with respect to \(\alpha_m\) and set it to \(0\) gives

$$ \alpha_m = \frac{1}{2} \ln\left(\frac{\sum_{y_i=k_m(\bar x_i)} w_i^{(m)}}{\sum_{y_i \neq k_m(\bar x_i)} w_i^{(m)}}\right) $$

Since the weighted error rate of the weak classifier is \(w_i^{(m)}\) for \(m > 1\), the error rate is

$$ \epsilon_{m}\,=\,\frac{\sum_{y_{i}\neq k_{m}(x_{i})}\,w_{i}^{(m)}}{\sum_{i=1}^{N}\,w_{i}^{(m)}} $$

We can now conclude \(\alpha\) to be

$$ \alpha_m = \frac{1}{2} \ln \left(\frac{1-\epsilon_m}{\epsilon_m}\right) $$


## Adaboost Algorithm

1. Initialise the observation weights \(w_i = \frac{1}{n}, i = 1,2,...,n\).
2. For m = 1 to M:
    1. Fit a classifier \(k^{(m)}(\bar x)\) to the traning data using weight \(w_i\).
    2. Compute the weighted error rate \(\epsilon_m\)
    3. Compute \(\alpha_m\)
    4. Set the new weight \(w_{i} \leftarrow w_{i} e^{-y_i \alpha_m h_m(\bar x)}\)
    5. Renormalise \(w_i\) such that \(\sum_i w_i = 1\)
3. Output \(C(\bar x) = \sum_{m=1}^{M} \alpha_m h_m(\bar x)\)


## Multi-class Adaboost Algorithm
[This paper](https://hastie.su.domains/Papers/samme.pdf) suggests a simple twist to allow Adaboost to be used for multi-class classification. The algorithm is exactly the same except now \(\alpha_m\) is changed to

$$ \alpha_m = \log \frac{1 - \epsilon_m}{\epsilon_m} + \log (K - 1)$$

where \(K > 1\) is the number of classes. This algorithm is implemented in [scikit-learn](https://scikit-learn.org/stable/auto_examples/ensemble/plot_adaboost_multiclass.html).

[^1]: I use a bar on top of a variable to denote it is a vector.
