---
num: lab07
ready: true
desc: "Application of Graphs to Machine Learning"
assigned: 2024-11-18 9:00:00.00-8
due: 2024-11-27 23:59:59.00-8
---

{% include mathjax.html %}

## Collaboration policy

You may work on this assignment with a partner. Make sure you add both your names at the top of `main.cpp` along with your perm numbers. Partnering is highly encouraged: Working in groups is a highly beneficial skill to have moving forward as well as in the industry.

## Academic integrity

All work submitted for this programming assignment must be your own, or from your partner.

## Goals for this lab

This lab is designed to offer a real-life application of graphs for a highly relevant field of computer science, machine learning, by implementing graph algorithms in the computation of a neural network.

### What this lab IS NOT about

- You will **NOT** be expected to learn and understand the theoretical inner workings of a neural network.
- You will **NOT** need to implement everything in the neural network - the heavy lifting is done for you.
- You will **NOT** need to worry about the architecture and design of the neural network.

### What this lab IS about

- Applying graph algorithms to a graph data structure.
- Learning and understanding the underlying graph structure of a neural network.
- Implementing basic class methods.

## Tips

- Don’t be intimidated by the nature of the lab: Focus on what you know about graphs and class structure and apply your skills as necessary. The neural network is meant to expose you to upper-division concepts earlier on and hopefully excite you for future classes.
- Start early and ask questions! It may take some time to understand what we are requiring you to do. This project is larger than normal. There is a lot of code involved, but try to focus on the subset of code you are required to implement.
- **The purpose of this lab is to write algorithms to traverse graphs (breadth-first and depth-first) in an applied way. Please read the specifications closely to see which functions require which algorithm. We have done the heavy lifting for you in terms of the neural network implementation and abstracted the math away behind visit functions. So, your plan should be to write a BFS and DFS algorithm and decide where to place these visit functions within them. First and foremost, this is a graph-centered lab in the context of neural networks, not a neural-network-centered lab.**

## Project Structure

The files you will work in are `NeuralNetwork.cpp` and `Graph.cpp`. Read the [tutorial](../lab07-EXTRA) to understand the basic terminology and the connection between graphs and neural networks.

### Graph

`Graph.cpp/hpp` contain the following classes:

- `NodeInfo`: Contains information of a node.
- `Connection`: Contains information about a connection between two nodes.
- `Graph`: Contains the base graph structure you learned about in class, using an adjacency list.

### NeuralNetwork

`NeuralNetwork.cpp/hpp` contains the following class:

- `NeuralNetwork`: Inherits the graph structure and exposes other neural network methods.

This class inherits from the `Graph` class. It means you can access graph members within the `NeuralNetwork` class. This is because a `NeuralNetwork` is a specific type of graph.

### Utilities

Other classes/files that are part of the project are:

- `DataLoader` contains an implementation for an object that contains a dataset and is used to send data into the neural network.
- `utility` contains miscellaneous functions to aid the neural network.

The other remaining files are related to the test code.

## Step by Step Instructions

### Step 0: Create a Git repo and get the starter code

Refer to lab01 for instructions on how to set up a GitHub repository and pull the starter code for this lab. Here is the link for this lab’s starter code:  <https://github.com/{{site.class_org.name}}/STARTER-{{page.num}}>

### Step 1: Run the executable

Review the Makefile provided and look at the top of each file to understand how the code is organized. If you are having trouble, take a look at this Makefile tutorial guide: <https://zackglazewski.github.io/UCSBCS24-MakefileIntroduction/>

We provided a simple test suite in `test_neuralnet.cpp`. For now, running `./test_neuralnet` will fail because some important functions have not been initialized yet. Once you implement steps 2 and 3, try playing around with `main.cpp`, which will load and test the accuracy of a pretrained neural network model.

### Step 2: Implement methods of the `Graph` class

Your first step is implementing some of the member functions for the `Graph` class. You need to implement the following functions:

- `void Graph::updateNode(int id, NodeInfo n)`
    - Allocates a `NodeInfo` object on the heap and updates it in `nodes`.
- `NodeInfo* Graph::getNode(int id) const`
    - This method should return a pointer to the `NodeInfo` object at the index `id`.
- `void Graph::updateConnection(int v, int u, double w)`
    - This method takes in a source id `v`, a destination id `u`, and a weight `w`. The input represents a weighted and directed edge in the graph (from `v` to `u`). Update the adjacency list to reflect this update. Connections do not need to be allocated on the heap. If the connection already exists, just update the weight.
- `void Graph::clear()`
    - This method should deallocate any memory allocated on the heap.

When you implement `updateNode` or `updateConnection`, you should check whether `id` is in the valid range based on the size of the graph.

### Step 3: Implement getter/setter methods of `NeuralNetwork` class

The next step is implementing the getters and setters for the ```Graph``` and ```NeuralNetwork``` classes. You need to implement the following functions:

- `void NeuralNetwork::setInputNodeIds(std::vector<int> inputNodeIds)`
- `void NeuralNetwork::setOutputNodeIds(std::vector<int> outputNodeIds)`
- `std::vector<int> NeuralNetwork::getInputNodeIds() const`
- `std::vector<int> NeuralNetwork::getOutputNodeIds() const`

Once you have correctly implemented the functions listed so far, the `test_structure` test case `./test_neuralnet 4` should pass.

### Step 4: Implement `predict` with BFS

The next two steps will be the most challenging part of the programming assignment. We recommend taking the self-test assignment on Gradescope before implementing these functions.

As described in the **Making predictions** part of the tutorial, you need to compute the values of a node after computing all preceding nodes’ values. To do so, we use a breadth first search (BFS), which you need to implement using a queue in the following function:

- `std::vector<double> predict(DataInstance instance)`
    - This function takes an input training example, `instance`, and returns the neural network's prediction (a vector, in case the neural network has more than one output).

We have provided you two important functions that take care of the math for you:

- `NeuralNetwork::visitPredictNode(int vId)`
    - This function takes care of the neural network math for visiting a node during the prediction phase. Computation will be performed on the `NodeInfo` whose `id` is `vId`.
- `NeuralNetwork::visitPredictNeighbor(Connection c)`
    - This function takes care of the neural network math for visiting a connection during the prediction phase. Computation will be performed on the nodes that make up the connection `c`.

Here is the flow of the algorithm. You initialize a queue with input nodes using the given input values and start the traversal. When you pop a node, you can assume that all previous node values are computed and the weighted sum is accumulated, so you should call `visitPredictNode` to add a bias and apply an activation function to finalize the value of the current node before visiting other nodes. For each visit of a connection, you should call `visitPredictNeighbor` to accumulate the weighted sum. You should not use the `NeuralNetwork::layers` member variable.

### Step 5: Implement `contribute` with DFS

As described in the **Training the neural network** part of the tutorial, you can calculate the gradient (we will call a gradient as a contribution interchangeably) of each weight or bias after computing them in all succeeding layers, which is backwards compared to the forward direction in which compute the prediction. To do so, we use a depth first search (DFS), which you need to implement using recursion in the following functions:

- `bool NeuralNetwork::contribute(double y, double p)`
    - This is the main, public version of this function. `y` is the ground truth label and `p` is the neural network's prediction.
- `double NeuralNetwork::contribute(int nodeId, const double& y, const double& p)`
    - This is the recursive helper function for the main contribute function. `y` and `p` remain the same throughout each call. `nodeId` indicates the node that is currently being visited.

Your job is to write the DFS algorithm that visits nodes and connections in the right order. Why do we use DFS for back-propagation? There are a couple of reasons:

1. The error received in one layer depends on some new error that the next layer computes and passes down. The error a node receives from its out-neighbors is an “incoming contribution” and the error that a node passes down to its in-neighbors is its “outgoing contribution.”
2. The connections are only forward-directed; you cannot traverse backward in the network without the help of a recursive call.

We have provided you with two important functions that take care of the math for you:

- `void visitContributeNode(int vId, double& outgoingContribution)`
    - This function updates `outgoingContribution` and computes the contribution of the node’s `delta` term.
- `void visitContributeNeighbor(Connection& c, double& incomingContribution, double& outgoingContribution)`
    - Updates `outgoingContribution` and computes the contribution to the connection’s `delta` term.

Notice `incomingContribution` and `outgoingContribution` defined in the function. During your DFS, `incomingContribution` will represent what comes from the recursive result of calling `contribute` on a neighbor. `outgoingContribution` will be updated throughout the duration of the recursive call and will eventually be returned, becoming the new `incomingContribution` for the previous layer.

In place of a “found” set, there is a map called `contributions`. This acts as a way to keep track of nodes that we have already visited, as well as store their previously computed contributions.

![NeuralNetwork Example]({{site.baseurl}}/lab/lab07/assets/generic_neural_net.svg)

In the example neural network above, suppose we are currently at node 3:

1. Iterate through all your neighbors. For each neighbor:
    - Get the neighbor’s incoming contribution by recursively calling `contribute` on node 6.
    - Using that incoming contribution, visit the connection corresponding to that neighbor.
        - In this case, we visit the connection with weight $w_7$.
2. Visit node 3 and pass in your running outgoing contribution to be updated.

Now, an example using node 1:

1. Get contribution from node 3, and visit the connection with $w_1$.
2. Get contribution from node 4, and visit the connection with $w_2$.
3. Get contribution from node 5, and visit the connection with $w_3$.
4. Visit node 1 and pass in your running outgoing contribution to be updated.

### Step 6: Test and submit

`test_neuralnet.cpp` has been given as a brief way to check your neural network. Please test your code against this file and make sure you pass all the tests before trying to submit to Gradescope. If you are having issues, it is helpful to run a debugger like `gdb` to make sure your code is following the logic described above.

## External resources

### BFS and DFS

- GeeksForGeeks BFS: <https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/>
- GeeksForGeeks DFS: <https://www.geeksforgeeks.org/depth-first-search-or-dfs-for-a-graph/>

### Graphviz

The graph class overloads the `<<` operator and outputs the graph in dot format. Copy the portion that looks like: `digraph G {...}` and paste it [here](https://dreampuf.github.io/GraphvizOnline/#digraph%20G%20%7B%0A%0A%20%20subgraph%20cluster_0%20%7B%0A%20%20%20%20style%3Dfilled%3B%0A%20%20%20%20color%3Dlightgrey%3B%0A%20%20%20%20node%20%5Bstyle%3Dfilled%2Ccolor%3Dwhite%5D%3B%0A%20%20%20%20a0%20-%3E%20a1%20-%3E%20a2%20-%3E%20a3%3B%0A%20%20%20%20label%20%3D%20%22process%20%231%22%3B%0A%20%20%7D%0A%0A%20%20subgraph%20cluster_1%20%7B%0A%20%20%20%20node%20%5Bstyle%3Dfilled%5D%3B%0A%20%20%20%20b0%20-%3E%20b1%20-%3E%20b2%20-%3E%20b3%3B%0A%20%20%20%20label%20%3D%20%22process%20%232%22%3B%0A%20%20%20%20color%3Dblue%0A%20%20%7D%0A%20%20start%20-%3E%20a0%3B%0A%20%20start%20-%3E%20b0%3B%0A%20%20a1%20-%3E%20b3%3B%0A%20%20b2%20-%3E%20a3%3B%0A%20%20a3%20-%3E%20a0%3B%0A%20%20a3%20-%3E%20end%3B%0A%20%20b3%20-%3E%20end%3B%0A%0A%20%20start%20%5Bshape%3DMdiamond%5D%3B%0A%20%20end%20%5Bshape%3DMsquare%5D%3B%0A%7D) to visualize the graph.

## Credits

This assignment was conceived and created by a UCSB CS ULA, Zackary Glazewski, in consultation with Diba Mirza for CS 24. Thanks to the UCSB CS24 teaching staff for reviewing and providing feedback: Torin Schlunk, Mehak Dhaliwal, Nawel Alioua, Joseph Ng, Shinda Huang, Xinlei Feng, Yaoyi Bai, Ally Chu, and Sanjana Shankar.

Revised by TA Gyuwan Kim and reviewed by ULAs Siddhi Mundhra, Dennis Kim, and Wong Zhao for the Fall 2024 quarter.

[CC BY-NC-SA 2.0](https://creativecommons.org/licenses/by-nc-sa/2.0/), Zackary Glazewski and Diba Mirza, Feb 2024.
