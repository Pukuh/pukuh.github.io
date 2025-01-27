---
share: true
title: "The Learning Problem"
date: 2025-01-24 06:00:00 -0400
tags: [obsidian, blog, dissemination, learning_theory, probability/applications]
filename: "docs/_posts/2025-01-24-the-leraning-problem"
---

In science, math, and engineering, it is essential to specify the problem one is trying to solve clearly. This not only helps compare the strengths and weaknesses of different solutions but also helps establish the critical aspects of the theory under development.

This paper explains the problem one is trying to solve with machine learning. A clear understanding of the learning problem is the basis for understanding the methods and techniques used when applying machine learning to solve practical problems, from data preprocessing/cleaning to model deployment.

## First Attempt: An ill-posed problem

In machine learning, we aim to learn about a complex phenomenon by analyzing data generated from it. To simplify, we can assume that the phenomenon is defined by an unknown function $f(𝒙)$ and that we only have access to input-output examples $\\{(𝒙_n,y_n)\\}_{n=1}^N$ from said function, where $y_n=f(𝒙_n)$.  This scenario is known as **supervised learning** and is one of the most widely studied frameworks in machine learning.

For instance, the function $f$ could represent the probability of a heart attack occurring in the next year based on a person’s age, sex, weight, height, and laboratory measurements such as cholesterol and sugar levels. Given the complexity of the human body and our limited knowledge of it, it is challenging to develop a model that accurately predicts heart attack risk based on biological and chemical principles alone. Instead, machine learning relies on historical patient data from hospitals to create a model $h(𝒙)$ that can approximate the function $f(𝒙)$ for future inputs (new patients). If the approximation is good for new inputs, we say the model $h(𝒙)$ **generalizes** well. <!--- This might be a good place to introduce the loss function and the first attempt to defining of the problem--->

Now, the question is how to choose an $h$ with good generalization, given a training set $\\{(𝒙_n,y_n)\\}_{n=1}^N$. A natural requirement may be to **interpolate** the data, that is, find a function $h$ that agrees as much as possible with $f$ on the training set. However, doing well on the training set does not automatically imply doing well on new data points. In fact, from a mathematical point of view, a function $h$ can take arbitrary values for different inputs. For example, consider the training set that consists of the three points in $ℝ^2$ shown in Figure 1. Note that the yellow line and the green curve are functions that agree with the training set but differ outside the training set. Similarly, there are an *infinite* number of functions in $ℝ^2$ that also interpolate the data but are different outside the training data. If $f$ is among those infinite functions, then we do not have any information that helps us identify it, given the training data alone. Thus, asking for a function that generalizes the data is an *ill-posed* problem that can not be solved without extra information.

## Inductive Bias
The conclusion is that to have generalization, it is necessary to make more assumptions. We may require two types of regularities:

> 1. The functions we are trying to learn have regularities that hold across different data points.

The approach here is to use all the information available about the problem and try to incorporate it into the models.  This bias toward a specific type of function, given prior knowledge of the problem domain, is called **_inductive bias_**. For example, when classifying images, the proposed functions should be invariant to translation, meaning that the assigned class should remain the same even if the image is translated. This property was introduced in *Neural Networks* through *Convolutional Neural Networks*, resulting in a significant increase in performance and marking the beginning of *Deep Learning*'s many successes in subsequent years.

Similarly, since the data comes from phenomena that occur in the real world, researchers have proposed that functions behave far from random, requiring that the functions do not change much between points (small derivatives). This and other properties can be introduced into the learning problem in the form of a function $C(f)$ that measures how much the function $f$ deviates from the desired property $C$. With this notation, we could propose the following definition of the learning problem:

> Given a training set $D=\\{(𝒙_n,y_n)\\}_{n=1}^N$ find a function $f$ that minimizes:$e_{in}(f)+C(f)$ where the function $C(f)$ measures how much the function deviates from some predefined inductive bias.
{: .prompt-info}

The hope is that the function obtained from the previous minimization generalizes well. However, that will depend on how the choice of $C(\cdot)$ matches the phenomena under study. This is the same approach used in science, and to guarantee generalization, we still have to test the model on *new data* to **validate** all the assumptions used to generate the model.


> 2. Data points where the learning algorithm trains share some properties with the data points where the learned model will be tested.

Which functions generalize well among all those that interpolate the data? Is it possible that a function that does not interpolate the data generalize better? 

The conclusion is that we need a way to relate the behavior of $h$ on $𝒙$'s inside and outside the training set. <!--- Talk about the assumption that properties of the training set should be present outside the training set --->

## Probability and Statistics to the Rescue
To effectively learn from a training set, it is crucial to acknowledge that the inputs used for testing the function should possess similar characteristics to those in the training set. Therefore, explicitly stating the underlying assumptions when formulating the learning problem is essential. By incorporating probability into our approach, we can establish a connection between the errors within the training data and those outside the training data. Furthermore, utilizing the language of probability enables us to consider and address the uncertainties associated with the inputs observed during both the training and post-training phases. 

We employ a probability distribution $𝐏$ to represent the likelihood of encountering a specific input. To illustrate this, consider our heart attack prediction example, where the probability of observing an individual with a height of 2.3 meters would be exceedingly low. Moreover, it's important to note that the distribution can vary based on the country or region where the data is collected. In machine learning, the underlying assumption that allows us to establish a relationship between the model's performance within and outside the training set is that the data points in both scenarios follow the same distribution.

This sheds light on why a model trained using data from Mexico may not yield accurate results when tested in Finland, as the distributions in these regions might differ.

Importantly, it's worth noting that we do not assume prior knowledge of the distribution. In fact, learning the properties of the input distribution is a fundamental aspect of all machine learning frameworks.

Probability distributions over data can be highly intricate mathematical entities, especially when dealing with high-dimensional data such as attributes of individuals, images, texts, etc. In probability and statistics, various quantities summarize specific characteristics of these distributions. One of the most significant quantities is the expected value of a function, which generates a real value (such as loss) and relies on uncertain elements (such as input data). The expected value of a function can be understood as the value one would anticipate if the function were evaluated using an input generated from the specified distribution. It's important to note that since the input is random, the output of the function will differ each time the experiment is conducted. Nonetheless, most outcomes should closely approximate the expected value of the function. Armed with these newfound concepts, we can formulate the learning problem, albeit still incomplete.

> **Learning Problem**: Given a training set $\\{(𝒙_n,y_n)\\}_{n=1}^N$ where each point follows a distribution 𝐏, find a function $h$ that minimizes the expected value of the loss $𝐄[l(h(𝒙),f(𝒙))]$ where $𝒙$ follows also the same distribution $𝐏$.

Although this problem statement relates the performance inside and outside the training set, it has not allowed any solution so far...