# MP2 Notebook

> Notebook converted to Markdown (outputs stripped; see the report PDF for results).

# (Optional) Colab Setup
If you aren't using Colab, you can delete the following code cell. This is just to help students with mounting to Google Drive to access the other .py files and downloading the data, which is a little trickier on Colab than on your local machine using Jupyter. 

```python
# you will be prompted with a window asking to grant permissions
from google.colab import drive
drive.mount("/content/drive")
```

```python
# fill in the path in your Google Drive in the string below. Note: do not escape slashes or spaces
import os
datadir = "/content/assignment2"
if not os.path.exists(datadir):
  !ln -s "/content/drive/My Drive/YOUR PATH HERE/assignment2/" $datadir
os.chdir(datadir)
!pwd
```

# Implement a Neural Network

This notebook contains testing code to help you develop a neural network by implementing the forward pass and backpropagation algorithm in the `models/neural_net.py` file. 

You will implement your network in the class `NeuralNetwork` inside the file `models/neural_net.py` to represent instances of the network. The network parameters are stored in the instance variable `self.params` where keys are string parameter names and values are numpy arrays.

```python
import numpy as np

from models.neural_net import NeuralNetwork

# For auto-reloading external modules
# See http://stackoverflow.com/questions/1907993/autoreload-of-modules-in-ipython
%load_ext autoreload
%autoreload 2

def rel_error(x, y):
    """Returns relative error"""
    return np.max(np.abs(x - y) / (np.maximum(1e-8, np.abs(x) + np.abs(y))))
```

The cell below initializes a toy dataset and corresponding model which will allow you to check your forward and backward pass by using a numeric gradient check. Note that we set a random seed for repeatable experiments.

```python
input_size = 2
hidden_size = 10
num_classes = 3
num_inputs = 5
optimizer = 'SGD'


def init_toy_model(num_layers):
    """Initializes a toy model"""
    np.random.seed(0)
    hidden_sizes = [hidden_size] * (num_layers - 1)
    return NeuralNetwork(input_size, hidden_sizes, num_classes, num_layers, optimizer)

def init_toy_data():
    """Initializes a toy dataset"""
    np.random.seed(0)
    X = np.random.randn(num_inputs, input_size)
    y = np.random.randn(num_inputs, num_classes)
    return X, y
```

# Implement forward and backward pass

The first thing you will do is implement the forward pass of your neural network. The forward pass should be implemented in the `forward` function. You can use helper functions like `linear`, `relu`, and `sigmoid` to help organize your code.

Next, you will implement the backward pass using the backpropagation algorithm. Backpropagation will compute the gradient of the loss with respect to the model parameters `W1`, `b1`, ... etc. Use a sigmoid fuction with mse loss for loss calcuation. Fill in the code blocks in `NeuralNetwork.backward`. 

# Gradient  check

If you have implemented your forward pass through the network correctly, you can use the following cell to debug your backward pass with a numeric gradient check. This function assumes that your gradient is divided by y.shape[0] * y.shape[1], where y is the first input to the backward function. You should use mse loss after the sigmoid layer for this gradient check. If your backward pass has been implemented correctly, the max relative error between your analytic solution and the numeric solution should be around 1e-7 or less for all parameters.


```python
from copy import deepcopy

from utils.gradient_check import eval_numerical_gradient

X, y = init_toy_data()


def f(W):
    net.forward(X)
    return net.backward(y)

for num in [2, 3]:
    net = init_toy_model(num)
    net.forward(X)
    net.backward(y)
    gradients = deepcopy(net.gradients)

    for param_name in net.params:
        param_grad_num = eval_numerical_gradient(f, net.params[param_name], verbose=False)
        print('%s max relative error: %e' % (param_name, rel_error(param_grad_num, gradients[param_name])))
```
