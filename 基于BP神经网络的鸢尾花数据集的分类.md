## 基于BP神经网络的鸢尾花数据集的分类

## BP神经网络算法分析

### **单个神经元的结构**

![image-20221101223420615](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101223420615.png)

​																							图1 单个神经元结构

![image-20221101223954588](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101223954588.png)

### **激活函数**

激活函数是一种添加到人工神经网络中的函数，旨在帮助网络学习数据中的复杂模式。类似于人类大脑中基于神经元的模型，激活函数最终决定了要发射给下一个神经元的内容。在人工神经网络中，一个节点的激活函数定义了该节点在给定的输入或输入集合下的输出。激活函数往往将线性的关系转换为非线性的关系，帮助神经网络学习更加复杂的模式。

本次算法实现采用的是Sigmoid函数：

![image-20221101224025221](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224025221.png)

![image-20221101224036000](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224036000.png)

​																										图2 Sigmoid函数

Sigmoid函数的导函数：

![image-20221101224115123](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224115123.png)

![image-20221101224123569](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224123569.png)

​																									图3 Sigmoid导函数图

### **传播过程**

本次算法实现使用的是三层神经网络结构，第一层是输入层，第二层是隐层，第三层是输出层。

![image-20221101224153946](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224153946.png)

​																									图4 三层前馈网络结构

![image-20221101224238691](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224238691.png)

### **数学推导**

#### **正向传播**

![image-20221101224431016](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224431016.png)

![image-20221101224453141](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224453141.png)

#### **反向传播**

![image-20221101224521734](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224521734.png)

![image-20221101224547060](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224547060.png)

![image-20221101224647469](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224647469.png)

BP算法是一种有监督式的学习算法，其主要思想是：输入学习样本，使用反向传播算法对网络的权值和偏差进行反复的调整训练，使输出的向量与期望向量尽可能地接近，当网络输出层的误差平方和小于指定的误差时训练完成，保存网络的权值和偏差。算法具体流程如下。

![image-20221101224738680](C:\Users\Zgq\AppData\Roaming\Typora\typora-user-images\image-20221101224738680.png)

## 代码如下：

```python
# -*- coding: utf-8 -*-
# @Time    : 2022-10-17 9:22
# @Author  : Zgq
# @FileName: bp_neural_network.py
# @Software: PyCharm
# BP Neural Network

# Call Library Functions
import math
import random

import matplotlib.pyplot as plt


# Activation function
def sigmiod(x):
    return 1.0 / (1.0 + math.exp(-x))


# BP_neural_network
class bp_neural_network:
    def __init__(self, n_in, n_hidden, n_out, step):
        # Set learning step
        self.step = step
        # Initialize neurons
        self.n_in = n_in + 1  # Input layer neuron threshold, input layer number plus 1
        self.n_hidden = n_hidden + 1  # Neuron threshold of hidden layer, the number of hidden layers is increased by 1
        self.n_out = n_out
        # Neuronal storage vector
        self.vector_in = [0.0] * self.n_in
        self.vector_hidden = [0.0] * self.n_hidden
        self.vector_out = [0.0] * self.n_out
        # Neuron threshold assignment
        self.vector_in[self.n_in - 1] = 1.0
        self.vector_hidden[self.n_hidden - 1] = 1.0
        # Weight storage vector
        self.in_v = [[0.0] * (self.n_hidden - 1) for i in range(self.n_in)]
        self.hidden_w = [[0.0] * self.n_out for i in range(self.n_hidden)]
        # Initialize the weight value
        for i in range(self.n_in):
            for j in range(self.n_hidden - 1):
                self.in_v[i][j] = random.uniform(-0.2, 0.2)
        for i in range(self.n_hidden):
            for j in range(self.n_out):
                self.hidden_w[i][j] = random.uniform(-2.0, 2.0)

    # Forward propagation algorithm
    def Forward_propagating(self, input_data):
        if len(input_data) != (self.n_in - 1):
            print("Input Data error!")
        # Input neuron assignment
        for i in range(self.n_in - 1):
            self.vector_in[i] = input_data[i]
        # Calculate the hidden layer neurons
        for i in range(self.n_hidden - 1):
            '''Take the inner product of the vectors and then use the activation function sigmiod ()'''
            self.vector_hidden[i] = sigmiod(sum(self.in_v[j][i] * self.vector_in[j] for j in range(self.n_in)))
        # Calculate the output layer neurons
        for i in range(self.n_out):
            '''Take the inner product of the vectors and then use the activation function sigmiod ()'''
            self.vector_out[i] = sigmiod(
                sum(self.hidden_w[j][i] * self.vector_hidden[j] for j in range(self.n_hidden)))
        return self.vector_out  # Returns the output layer neuron

    # Back propagation algorithm
    def Back_propagation(self, output_data):
        '''Stochastic gradient descent'''
        if len(output_data) != self.n_out:
            print("Output Data error!")
        # Loss value
        Loss = 0.0
        # Calculate the output layer error
        g = [0.0] * self.n_out
        for i in range(self.n_out):
            Loss += 0.5 * (self.vector_out[i] - output_data[i]) ** 2  # The loss function uses the least square method
            '''The continued rule takes the gradient'''
            g[i] = (output_data[i] - self.vector_out[i]) * self.vector_out[i] * (
                    1 - self.vector_out[i])  # Calculate the gradient value
        # Calculate the hidden layer error
        e = [0.0] * (self.n_hidden - 1)
        for i in range(self.n_hidden - 1):
            for j in range(self.n_out):
                e[i] += g[j] * self.hidden_w[i][j]
            '''The continued rule takes the gradient'''
            e[i] = e[i] * self.vector_hidden[i] * (1 - self.vector_hidden[i])  # Calculate the gradient value
        # Update the hidden layer weights
        for i in range(self.n_hidden):
            for j in range(self.n_out):
                self.hidden_w[i][j] += self.step * g[j] * self.vector_hidden[i]
        # Update the input layer weights
        for i in range(self.n_in):
            for j in range(self.n_hidden - 1):
                self.in_v[i][j] += self.step * e[j] * self.vector_in[i]
        return Loss  # Return loss value

    # Neural network training
    def train(self, train_data, train_times, lossger):
        for i in range(train_times):
            all_loss = 0.0  # Loss value
            for v in train_data:
                self.Forward_propagating([v[0], v[1], v[2], v[3]])  # Positive communication
                all_loss += self.Back_propagation(v[4])  # Back propagation
            lossger.append(all_loss)  # Record all loss values
            loss = all_loss / len(train_data)  # Calculate the average loss value
            if (i + 1) % 100 == 0 or i == 0:
                print("第{}次训练后的损失值: {}".format((i + 1), loss))

    # Neural network test
    def test(self, test_data):
        exact_number = 0  # Record the correct number of tests
        for v in test_data:
            ''' Take the largest of the category list as the classification category'''
            i = v[4].index(max(v[4]))  # Take the real category
            tmp = self.Forward_propagating([v[0], v[1], v[2], v[3]])  # Positive communication
            j = tmp.index(max(tmp))  # Take the category of the prediction
            # Verify that the prediction is correct
            if i == j:
                exact_number += 1
        result = exact_number / len(test_data)  # Accuracy of calculation
        return result


if __name__ == '__main__':
    # Data extraction
    iris_data = []
    with open('iris.dat', 'r') as iris:
        for i in iris.readlines():
            # Preprocessing read data
            i = i.replace('\n', '')
            i = i.split()
            tmp = []
            for v in i:
                tmp.append(eval(v))  # String to number
            iris_data.append(tmp)
    # Build a list similar to the output neurons
    for v in iris_data:
        if v[4] == 1:
            v[4] = [1, 0, 0]
        if v[4] == 2:
            v[4] = [0, 1, 0]
        if v[4] == 3:
            v[4] = [0, 0, 1]

    # Split the training set and test set
    split_ratio = 0.7
    # Parameter initialization
    n_in = 4  # Number of input neurons
    n_hidden = 16  # Number of neurons in the hidden layer
    n_out = 3  # Number of neurons in the output layer
    step = 0.2  # learning rate
    train_times = 1000  # Number of training
    Accuracy = 0.0  # Average accuracy rate
    verify_times = 10  # Number of cross validation

    for i in range(verify_times):
        # Data set shuffling
        random.shuffle(iris_data)
        # The training set and test set were segmented with a segmentation ratio of 0.7
        train_iris = iris_data[:int(split_ratio * len(iris_data))]
        test_iris = iris_data[int(split_ratio * len(iris_data)):]

        # Calyx length
        a = [iris_data[i][0] for i in range(len(train_iris))]
        # Calyx width
        b = [iris_data[i][1] for i in range(len(train_iris))]
        # Drawing training set scatter plot
        plt.title("Training set scatter plot")
        plt.xlabel("Calyx length")
        plt.ylabel("Calyx width")
        plt.scatter(a, b, alpha=1, s=5)
        plt.show()

        print("第%d轮验证：" % (i + 1))
        # Building a neural network
        BP = bp_neural_network(n_in, n_hidden, n_out, step)

        Lossger = []  # Record the loss value of the dedar process
        # Model training
        BP.train(train_data=train_iris, train_times=train_times, lossger=Lossger)
        print("模型%d次训练完成！！" % (train_times))

        # Model test
        tmp = BP.test(test_data=test_iris)
        print("第{}次验证准确率：{}%".format(i + 1, tmp * 100))
        Accuracy += tmp

        # Drawing Loss
        plt.title("Change of loss values")
        plt.xlabel("Train times")
        plt.ylabel("Loss")
        plt.grid(True)
        plt.plot(Lossger)
        plt.show()

    # Calculate the average accuracy
    print("{}次交叉验证的平均准确率：{}%".format(verify_times, Accuracy / verify_times * 100))
```

## 数据集：

```
5.1 3.5 1.4 0.2 1
4.9 3.0 1.4 0.2 1
4.7 3.2 1.3 0.2 1
4.6 3.1 1.5 0.2 1
5.0 3.6 1.4 0.2 1
5.4 3.9 1.7 0.4 1
4.6 3.4 1.4 0.3 1
5.0 3.4 1.5 0.2 1
4.4 2.9 1.4 0.2 1
4.9 3.1 1.5 0.1 1
5.4 3.7 1.5 0.2 1
4.8 3.4 1.6 0.2 1
4.8 3.0 1.4 0.1 1
4.3 3.0 1.1 0.1 1
5.8 4.0 1.2 0.2 1
5.7 4.4 1.5 0.4 1
5.4 3.9 1.3 0.4 1
5.1 3.5 1.4 0.3 1
5.7 3.8 1.7 0.3 1
5.1 3.8 1.5 0.3 1
5.4 3.4 1.7 0.2 1
5.1 3.7 1.5 0.4 1
4.6 3.6 1.0 0.2 1
5.1 3.3 1.7 0.5 1
4.8 3.4 1.9 0.2 1
5.0 3.0 1.6 0.2 1
5.0 3.4 1.6 0.4 1
5.2 3.5 1.5 0.2 1
5.2 3.4 1.4 0.2 1
4.7 3.2 1.6 0.2 1
4.8 3.1 1.6 0.2 1
5.4 3.4 1.5 0.4 1
5.2 4.1 1.5 0.1 1
5.5 4.2 1.4 0.2 1
4.9 3.1 1.5 0.1 1
5.0 3.2 1.2 0.2 1
5.5 3.5 1.3 0.2 1
4.9 3.1 1.5 0.1 1
4.4 3.0 1.3 0.2 1
5.1 3.4 1.5 0.2 1
5.0 3.5 1.3 0.3 1
4.5 2.3 1.3 0.3 1
4.4 3.2 1.3 0.2 1
5.0 3.5 1.6 0.6 1
5.1 3.8 1.9 0.4 1
4.8 3.0 1.4 0.3 1
5.1 3.8 1.6 0.2 1
4.6 3.2 1.4 0.2 1
5.3 3.7 1.5 0.2 1
5.0 3.3 1.4 0.2 1
7.0 3.2 4.7 1.4 2
6.4 3.2 4.5 1.5 2
6.9 3.1 4.9 1.5 2
5.5 2.3 4.0 1.3 2
6.5 2.8 4.6 1.5 2
5.7 2.8 4.5 1.3 2
6.3 3.3 4.7 1.6 2
4.9 2.4 3.3 1.0 2
6.6 2.9 4.6 1.3 2
5.2 2.7 3.9 1.4 2
5.0 2.0 3.5 1.0 2
5.9 3.0 4.2 1.5 2
6.0 2.2 4.0 1.0 2
6.1 2.9 4.7 1.4 2
5.6 2.9 3.6 1.3 2
6.7 3.1 4.4 1.4 2
5.6 3.0 4.5 1.5 2
5.8 2.7 4.1 1.0 2
6.2 2.2 4.5 1.5 2
5.6 2.5 3.9 1.1 2
5.9 3.2 4.8 1.8 2
6.1 2.8 4.0 1.3 2
6.3 2.5 4.9 1.5 2
6.1 2.8 4.7 1.2 2
6.4 2.9 4.3 1.3 2
6.6 3.0 4.4 1.4 2
6.8 2.8 4.8 1.4 2
6.7 3.0 5.0 1.7 2
6.0 2.9 4.5 1.5 2
5.7 2.6 3.5 1.0 2
5.5 2.4 3.8 1.1 2
5.5 2.4 3.7 1.0 2
5.8 2.7 3.9 1.2 2
6.0 2.7 5.1 1.6 2
5.4 3.0 4.5 1.5 2
6.0 3.4 4.5 1.6 2
6.7 3.1 4.7 1.5 2
6.3 2.3 4.4 1.3 2
5.6 3.0 4.1 1.3 2
5.5 2.5 4.0 1.3 2
5.5 2.6 4.4 1.2 2
6.1 3.0 4.6 1.4 2
5.8 2.6 4.0 1.2 2
5.0 2.3 3.3 1.0 2
5.6 2.7 4.2 1.3 2
5.7 3.0 4.2 1.2 2
5.7 2.9 4.2 1.3 2
6.2 2.9 4.3 1.3 2
5.1 2.5 3.0 1.1 2
5.7 2.8 4.1 1.3 2
6.3 3.3 6.0 2.5 3
5.8 2.7 5.1 1.9 3
7.1 3.0 5.9 2.1 3
6.3 2.9 5.6 1.8 3
6.5 3.0 5.8 2.2 3
7.6 3.0 6.6 2.1 3
4.9 2.5 4.5 1.7 3
7.3 2.9 6.3 1.8 3
6.7 2.5 5.8 1.8 3
7.2 3.6 6.1 2.5 3
6.5 3.2 5.1 2.0 3
6.4 2.7 5.3 1.9 3
6.8 3.0 5.5 2.1 3
5.7 2.5 5.0 2.0 3
5.8 2.8 5.1 2.4 3
6.4 3.2 5.3 2.3 3
6.5 3.0 5.5 1.8 3
7.7 3.8 6.7 2.2 3
7.7 2.6 6.9 2.3 3
6.0 2.2 5.0 1.5 3
6.9 3.2 5.7 2.3 3
5.6 2.8 4.9 2.0 3
7.7 2.8 6.7 2.0 3
6.3 2.7 4.9 1.8 3
6.7 3.3 5.7 2.1 3
7.2 3.2 6.0 1.8 3
6.2 2.8 4.8 1.8 3
6.1 3.0 4.9 1.8 3
6.4 2.8 5.6 2.1 3
7.2 3.0 5.8 1.6 3
7.4 2.8 6.1 1.9 3
7.9 3.8 6.4 2.0 3
6.4 2.8 5.6 2.2 3
6.3 2.8 5.1 1.5 3
6.1 2.6 5.6 1.4 3
7.7 3.0 6.1 2.3 3
6.3 3.4 5.6 2.4 3
6.4 3.1 5.5 1.8 3
6.0 3.0 4.8 1.8 3
6.9 3.1 5.4 2.1 3
6.7 3.1 5.6 2.4 3
6.9 3.1 5.1 2.3 3
5.8 2.7 5.1 1.9 3
6.8 3.2 5.9 2.3 3
6.7 3.3 5.7 2.5 3
6.7 3.0 5.2 2.3 3
6.3 2.5 5.0 1.9 3
6.5 3.0 5.2 2.0 3
6.2 3.4 5.4 2.3 3
5.9 3.0 5.1 1.8 3
```
