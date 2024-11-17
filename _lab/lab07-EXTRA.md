---
num: lab07-tutorial
ready: true
desc: "A Gentle Introduction to Neural Networks"
---

{% include mathjax.html %}

This section provides a very brief introductory explanation of neural networks.

## Recap on graphs

A graph is a structure consisting of nodes and edges (connections between nodes). A graph can represent many different situations with varying constraints, so graphs are powerful. Depending on the problem setting, we can choose an appropriate graph type and develop an algorithm on the chosen model.

For example, a binary tree is a type of graph with the following conditions:

- There is a single “root” node that has no incoming connections.
- Every node has at most two outgoing connections, collectively called “children.”
- Every node, except the root, has exactly one incoming connection, called the “parent.”
- A node may never have an outgoing connection to an ancestor or cousin.

Suppose we want to formulate a problem of finding the shortest path from one city to another. As illustrated in the following graph, we can represent each city as a node and a highway connecting two cities as a directed edge with a weight representing its length.

![NeuralNetwork Example]({{site.baseurl}}/lab/lab07/assets/generic_weighted_directed_graph.svg)

## Introduction to machine learning and neural networks

A machine learning model learns from training data to make a prediction about unseen test data. In other words, a machine learning model is a function that maps an input $x$ to an output $y$. Various types of machine learning models exist, and which machine learning model to use is usually chosen depending on a target task (or an availability of data).

In this lab, we focus on an artificial neural network, a specific class of machine learning models inspired by biological neural networks in animal brains, and its underlying graph structure. A neural network consists of nodes (similar to neurons in the brain) and edges (similar to synapses in the brain). Therefore, a neural network can also be represented as a graph. There are different types of neural networks. In this lab, we focus on a fully connected feedforward neural network, but we will just call it a neural network for brevity.

### Structure of a feed forward neural network

Here are the specifications of the graph structure in a neural network:

- A neural network consists of sequential “layers.” The first layer, the last layer, and the other intermediate layers are called “input” layer, “output” layer, and “hidden” layers, respectively.
- Each layer consists of a disjoint set of nodes.
- For every node pair between two consecutive layers, there is an edge with a direction from the predecessor layer to the successor layer and a “weight.” There are no other connections.
- Every node, except for nodes in the input layer, contains a “bias” term.
- Every node can be activated with an “activation function.” Typically, all nodes in a layer share the same activation function.

Following is an example diagram of a neural network which has three layers, two input nodes, and one output node:

![NeuralNetwork Example]({{site.baseurl}}/lab/lab07/assets/generic_neural_net.svg)

### Making predictions

Each node’s value is calculated by the weighted sum of the values of the previous layer nodes plus a bias followed by an activation. To calculate a node’s value, it requires the value of the preceding nodes. Therefore, we should perform the computation in the correct order defined by the graph structure of the neural network. We can compute in the order of layers, from the input layer to the output layer.

Let’s look at how we compute node values in the example neural network above. Assume the node 1 contains the input value ($x_1$​) and the node 2 contains the input value ($x_2$​). Using the input values, we can compute the node values in the hidden layer: $h_1 = \operatorname{ReLU}(x_1w_1 + x_2w_4 + b_1)$, $h_2 = \operatorname{ReLU}(x_1w_2 + x_2w_5 + b_2)$, and $h_3 = \operatorname{ReLU}(x_1w_3 + x_2w_6 + b_3)$. And then, with those values, we can compute the output node value: $y_1 = \operatorname{sigmoid}(h_1w_7 + h_2w_8 + h_3w_9 + b_4)$.

Note that weights and biases determine the prediction. Model parameters are the set of all weights and biases (in the example, $\{w_1, \ldots, w_9, b_1, \ldots, b_4\}$) that need to be learned. We can update model parameters using a training dataset, a set of input-output pairs $(x, y)$ (in the example, $x = (x_1, x_2)$ and $y = y_1$), from some initialization based on gradient descent method in a way that reduces the error between predictions and ground truths (will be described later). After that, we can predict the output $y$ for a new input $x$ using the trained model parameters.

### Training the neural network

When a neural network makes a prediction, we can measure “how bad” of a prediction by comparing it with a ground truth using a *loss function*. We train a model by optimizing it to minimize this loss over the training data. To do so, we calculate a gradient of the loss function with respect to the model parameters and update model parameters in the negative direction of each gradient. This is called *gradient descent*. Usually, since the size of the training dataset is large, we repeat this gradient descent on a small sample subset of the training dataset (called a mini-batch). In this case, it is called stochastic gradient descent.

Based on the chain rule (we will skip the exact formulation here), the gradient of weights and biases should be calculated in the backward order starting from the output layer. Therefore, we call the process of gradient computation as *backpropagation*. Here is a nifty little GIF that depicts these processes. You can see how the input flows through the graph, how the nodes are activated, and how the error gets propagated backward.

![Prediction and Backprop]({{site.baseurl}}/lab/lab07/assets/backprop.gif)

## External resources

### Gradient descent

You are not required to understand the math behind gradient descent, but if you are interested, this is a great resource:

- StatQuest - Gradient Descent: <https://www.youtube.com/watch?v=sDv4f4s2SB8>

### Neural networks

Here are some great resources to help you out. I would say that StatQuest has great videos to understand the implementation of neural network structures, whereas 3Blue1Brown is more conceptual based:

- StatQuest - Neural Network Basics (great for understanding the prediction algorithm): <https://youtu.be/CqOfi41LfDw?si=8waS2U01uMWcpH2i>
- StatQuest - Back Propagation (great for understanding the contribute algorithm): <https://youtu.be/IN2XmBhILt4?si=bnDft-3T4DQ2iO9X>
- 3Blue1Brown - <https://youtu.be/aircAruvnKk?si=KZt2AsbD7URc58-L>

## Credits

This assignment was conceived and created by a UCSB CS ULA, Zackary Glazewski, in consultation with Diba Mirza for CS 24. Thanks to the UCSB CS24 teaching staff for reviewing and providing feedback: Torin Schlunk, Mehak Dhaliwal, Nawel Alioua, Joseph Ng, Shinda Huang, Xinlei Feng, Yaoyi Bai, Ally Chu, and Sanjana Shankar.

Revised by TA Gyuwan Kim and reviewed by ULAs Siddhi Mundhra, Dennis Kim, and Wong Zhao for the Fall 2024 quarter.

[CC BY-NC-SA 2.0](https://creativecommons.org/licenses/by-nc-sa/2.0/), Zackary Glazewski and Diba Mirza, Feb 2024.
