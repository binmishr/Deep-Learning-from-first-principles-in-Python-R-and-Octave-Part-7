# Deep-Learning-from-first-principles-in-Python-R-and-Octave-Part-7

The details of the codeset and plots are included in the attached Microsoft Word Document (.docx) file in this repository. 
You need to view the file in "Read Mode" to see the contents properly after downloading the same.

A Brief Introduction
========================
#Introduction
In this post ,I implement optimization methods used in Stochastic Gradient Descent (SGD) to speed up the convergence. Specifically I discuss and implement the following gradient descent optimization techniques

    a.Vanilla Stochastic Gradient Descent
    b.Learning rate decay
    c. Momentum method
    d. RMSProp
    e. Adaptive Moment Estimation (Adam)

So for example if we had some time series data \theta_{1},\theta_{2},\theta_{3}... \theta_{t} then we we can represent 
    v_{t} = \beta v_{t-1} + (1-\beta)\theta_{t}

Here v_{t} represent the average of the data set over \frac{1}{1-\beta}. By choosing different values of \beta we can average over a larger or smaller number of the data points. 

We can write the equations as follows
    v_{t} = \beta v_{t-1} + (1-\beta)\theta_{t} \\
    v_{t-1} = \beta v_{t-2} + (1-\beta)\theta_{t-1}\\
    v_{t-2} = \beta v_{t-3} + (1-\beta)\theta_{t-2}\\

    v_{t-k} = \beta v_{t-(k+1))} + (1-\beta)\theta_{t-k}\\

    v_{t} =  (1-\beta)\theta_{t} + \beta v_{t-1}\\
    v_{t} =  (1-\beta)\theta_{t} + \beta ((1-\beta)\theta_{t-1}) + \beta v_{t-2}  \\

    v_{t} =  (1-\beta)\theta_{t} + \beta ((1-\beta)\theta_{t-1}) + \beta ((1-\beta)\theta_{t-2}+ \beta v_{t-3}  )  \\

Hence it can be seen that the v_{t} is the weighted sum over the previous values of \theta which is an exponentially decaying function.

##1.1a. Stochastic Gradient Descent (Vanilla) - Python
```{python cache=TRUE}
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
import sklearn.linear_model
import pandas as pd
import sklearn
import sklearn.datasets
exec(open("DLfunctions7.py").read())
exec(open("load_mnist.py").read())


training=list(read(dataset='training',path=".\\mnist"))
test=list(read(dataset='testing',path=".\\mnist"))
lbls=[]
pxls=[]
print(len(training))
#for i in range(len(training)):
for i in range(60000):
       l,p=training[i]
       lbls.append(l)
       pxls.append(p)
labels= np.array(lbls)
pixels=np.array(pxls)       
y=labels.reshape(-1,1)
X=pixels.reshape(pixels.shape[0],-1)
X1=X.T
Y1=y.T

print("X1=",X1.shape)
print("y1=",Y1.shape)

# Create  a list of random numbers of 16384
permutation = list(np.random.permutation(2**10))
# Subset 16384 from the data
X2 = X1[:, permutation]
Y2 = Y1[:, permutation].reshape((1,2**10))
  
layersDimensions=[784, 15,9,10] 
parameters = L_Layer_DeepModel_SGD(X2, Y2, layersDimensions, hiddenActivationFunc='relu', 
                                   outputActivationFunc="softmax",learningRate = 0.01 ,
                                   optimizer="gd",
                                   mini_batch_size =512, num_epochs = 5000, print_cost = True,figure="fig1.png")

```
##1.1a. Stochastic Gradient Descent (Vanilla) - R
```{r fig1, cache=TRUE}
source("mnist.R")
source("DLfunctions7.R")
load_mnist()
show_digit(train$x[2,])


x <- t(train$x)
X <- x[,1:60000]
y <-train$y
y1 <- y[1:60000]
y2 <- as.matrix(y1)
Y=t(y2)

# Subset 16384 random samples from MNIST 
permutation = c(sample(2^10))
# Randomly shuffle the training data
X1 = X[, permutation]
y1 = Y[1, permutation]
y2 <- as.matrix(y1)
Y1=t(y2)
layersDimensions=c(784, 15,9, 10) 
source("DLfunctions7.R")
# GD
retvalsSGD= L_Layer_DeepModel_SGD(X1, Y1, layersDimensions,
                            hiddenActivationFunc='tanh',
                            outputActivationFunc="softmax",
                            learningRate = 0.05,
                            optimizer="gd",
                            mini_batch_size = 512, 
                            num_epochs = 5000, 
                            print_cost = True)

#Plot the cost vs iterations
iterations <- seq(0,5000,1000)
costs=retvalsSGD$costs
df=data.frame(iterations,costs)
ggplot(df,aes(x=iterations,y=costs)) + geom_point() + geom_line(color="blue") +
 ggtitle("Costs vs no of epochs") + xlab("No of epochss") + ylab("Cost")
```


##1.1a. Stochastic Gradient Descent (Vanilla) - Octave

##2.1. Stochastic Gradient Descent  with Learning rate decay 
Since in SGD with in each epoch we use slight different samples gradient descent oscillates across the ravines and wanders around the minima with a fixed learning rate. In this technique of 'learning rate decay' the learning rate is slowly decreases with the number of epocs and becomes smaller and smaller so that gradient descent can take smaller steps towards the minima.

There are several techniques employed in learning rate decay

a) Exponential decay: \alpha = decayRate^{epochNum} *\alpha_{0}
b) 1/t decay : \alpha = \frac{\alpha_{0}}{1 + epochNum}
c) \alpha = \frac {k}{\sqrt(epochNum)}

In my implementation I have used the 'exponential decay'. The code snippet for Python is shown below

```{python}

```

##2.1a. Stochastic Gradient Descent  with Learning rate decay - Python
```{python cache=TRUE}
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
import sklearn.linear_model
import pandas as pd
import sklearn
import sklearn.datasets
exec(open("DLfunctions7.py").read())
exec(open("load_mnist.py").read())


training=list(read(dataset='training',path=".\\mnist"))
test=list(read(dataset='testing',path=".\\mnist"))
lbls=[]
pxls=[]
print(len(training))
#for i in range(len(training)):
for i in range(60000):
       l,p=training[i]
       lbls.append(l)
       pxls.append(p)
labels= np.array(lbls)
pixels=np.array(pxls)       
y=labels.reshape(-1,1)
X=pixels.reshape(pixels.shape[0],-1)
X1=X.T
Y1=y.T

print("X1=",X1.shape)
print("y1=",Y1.shape)

# Create  a list of random numbers of 16384
permutation = list(np.random.permutation(2**10))
# Subset 16384 from the data
X2 = X1[:, permutation]
Y2 = Y1[:, permutation].reshape((1,2**10))
layersDimensions=[784, 15,9,10] 
parameters = L_Layer_DeepModel_SGD(X2, Y2, layersDimensions, hiddenActivationFunc='relu', 
                                   outputActivationFunc="softmax",
                                   learningRate = 0.01 , lrDecay=True, decayRate=0.9999,
                                   optimizer="gd",
                                   mini_batch_size =512, num_epochs = 5000, print_cost = True,figure="fig2.png")
```


##2.1b. Stochastic Gradient Descent  with Learning rate decay - R

```{r fig2, cache=TRUE}
source("mnist.R")
source("DLfunctions7.R")
load_mnist()



x <- t(train$x)
X <- x[,1:60000]
y <-train$y
y1 <- y[1:60000]
y2 <- as.matrix(y1)
Y=t(y2)

# Subset 16384 random samples from MNIST 
permutation = c(sample(2^10))
# Randomly shuffle the training data
X1 = X[, permutation]
y1 = Y[1, permutation]
y2 <- as.matrix(y1)
Y1=t(y2)
layersDimensions=c(784, 15,9, 10) 
# Learning rate decay
retvalsSGD= L_Layer_DeepModel_SGD(X1, Y1, layersDimensions,
                                  hiddenActivationFunc='tanh',
                                  outputActivationFunc="softmax",
                                  learningRate = 0.05,
                                  lrDecay=TRUE,
                                  decayRate=0.9999,
                                  optimizer="gd",
                                  mini_batch_size = 512, 
                                  num_epochs = 5000, 
                                  print_cost = True)

#Plot the cost vs iterations
iterations <- seq(0,5000,1000)
costs=retvalsSGD$costs
df=data.frame(iterations,costs)
ggplot(df,aes(x=iterations,y=costs)) + geom_point() + geom_line(color="blue") +
 ggtitle("Costs vs number of epochs") + xlab("No of epochs") + ylab("Cost")
```



##2.1c. Stochastic Gradient Descent  with Learning rate decay - Octave
```{python}

```


##3.1. Stochastic Gradient Descent  with Momentum
Stochastic Gradient Descent  with Momentum uses the exponentially weighted average
method discusses above and more generally moves faster into the ravine than across it.

The equations are

v_{dW}^l = \beta v_{dW}^l + (1-\beta)dW^{l}\\
v_{db}^l = \beta v_{db}^l + (1-\beta)db^{l} \\

W^{l} = W^{l} - \alpha v_{dW}^l\\
b^{l} = b^{l} - \alpha v_{db}^l

where v_{dW} and v_{db} are the momentum terms which are exponentially weightted with
the corresponding gradients dW abd db at the corresponding layer 'l'


The code snippet for Stochastic Gradient Descent with momentum in R is shown below
```{r}
```

##3.1a. Stochastic Gradient Descent  with Momentum- Python
```{python cache=TRUE}
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
import sklearn.linear_model
import pandas as pd
import sklearn
import sklearn.datasets
exec(open("DLfunctions7.py").read())
exec(open("load_mnist.py").read())


training=list(read(dataset='training',path=".\\mnist"))
test=list(read(dataset='testing',path=".\\mnist"))
lbls=[]
pxls=[]
print(len(training))
#for i in range(len(training)):
for i in range(60000):
       l,p=training[i]
       lbls.append(l)
       pxls.append(p)
labels= np.array(lbls)
pixels=np.array(pxls)       
y=labels.reshape(-1,1)
X=pixels.reshape(pixels.shape[0],-1)
X1=X.T
Y1=y.T

print("X1=",X1.shape)
print("y1=",Y1.shape)

# Create  a list of random numbers of 16384
permutation = list(np.random.permutation(2**10))
# Subset 16384 from the data
X2 = X1[:, permutation]
Y2 = Y1[:, permutation].reshape((1,2**10))
layersDimensions=[784, 15,9,10] 

parameters = L_Layer_DeepModel_SGD(X2, Y2, layersDimensions, hiddenActivationFunc='relu', 
                                   outputActivationFunc="softmax",learningRate = 0.01 ,
                                   optimizer="momentum", beta=0.9,
                                   mini_batch_size =512, num_epochs = 5000, print_cost = True,figure="fig3.png")
```

##3.1b. Stochastic Gradient Descent  with Momentum- R
```{r fig4, cache=TRUE}
source("mnist.R")
source("DLfunctions7.R")
load_mnist()



x <- t(train$x)
X <- x[,1:60000]
y <-train$y
y1 <- y[1:60000]
y2 <- as.matrix(y1)
Y=t(y2)

# Subset 16384 random samples from MNIST 
permutation = c(sample(2^10))
# Randomly shuffle the training data
X1 = X[, permutation]
y1 = Y[1, permutation]
y2 <- as.matrix(y1)
Y1=t(y2)
layersDimensions=c(784, 15,9, 10) 
retvalsSGD= L_Layer_DeepModel_SGD(X1, Y1, layersDimensions,
                                  hiddenActivationFunc='tanh',
                                  outputActivationFunc="softmax",
                                  learningRate = 0.05,
                                  optimizer="momentum",
                                  beta=0.9,
                                  mini_batch_size = 512, 
                                  num_epochs = 5000, 
                                  print_cost = True)

#Plot the cost vs iterations
iterations <- seq(0,5000,1000)
costs=retvalsSGD$costs
df=data.frame(iterations,costs)
ggplot(df,aes(x=iterations,y=costs)) + geom_point() + geom_line(color="blue") +
 ggtitle("Costs vs number of epochs") + xlab("No of epochs") + ylab("Cost")
```

##4.1. Stochastic Gradient Descent  with RMSProp
Stochastic Gradient Descent  with RMSProp tries to move faster towards the minima while
dampening the oscillations across the ravine.

The equations are

s_{dW}^l = \beta_{1} s_{dW}^l + (1-\beta_{1})(dW^{l})^{2}\\
s_{db}^l = \beta_{1} s_{db}^l + (1-\beta_{1})(db^{l})^2 \\

W^{l} = W^{l} - \frac {\alpha s_{dW}^l}{\sqrt (s_{dW}^l + \epsilon) }\\
b^{l} = b^{l} - \frac {\alpha s_{db}^l}{\sqrt (s_{db}^l + \epsilon) } \\

where v_{dW} and v_{db} are the momentum terms which are exponentially weightted with
the corresponding gradients dW abd db at the corresponding layer 'l'

The code snippet in Octave is showb below

```{python}

```


##4.1a. Stochastic Gradient Descent  with RMSProp - Python
```{python}
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
import sklearn.linear_model
import pandas as pd
import sklearn
import sklearn.datasets
exec(open("DLfunctions7.py").read())
exec(open("load_mnist.py").read())


training=list(read(dataset='training',path=".\\mnist"))
test=list(read(dataset='testing',path=".\\mnist"))
lbls=[]
pxls=[]
print(len(training))
#for i in range(len(training)):
for i in range(60000):
       l,p=training[i]
       lbls.append(l)
       pxls.append(p)
labels= np.array(lbls)
pixels=np.array(pxls)       
y=labels.reshape(-1,1)
X=pixels.reshape(pixels.shape[0],-1)
X1=X.T
Y1=y.T

print("X1=",X1.shape)
print("y1=",Y1.shape)

# Create  a list of random numbers of 16384
permutation = list(np.random.permutation(2**10))
# Subset 16384 from the data
X2 = X1[:, permutation]
Y2 = Y1[:, permutation].reshape((1,2**10))
  
layersDimensions=[784, 15,9,10] 
parameters = L_Layer_DeepModel_SGD(X2, Y2, layersDimensions, hiddenActivationFunc='relu', 
                                   outputActivationFunc="softmax",learningRate = 0.01 ,
                                   optimizer="rmsprop", beta1=0.7, epsilon=1e-8,
                                   mini_batch_size =512, num_epochs = 5000, print_cost = True,figure="fig4.png")
```

##4.1b. Stochastic Gradient Descent  with RMSProp - R
```{r fig5, cache=TRUE}
source("mnist.R")
source("DLfunctions7.R")
load_mnist()



x <- t(train$x)
X <- x[,1:60000]
y <-train$y
y1 <- y[1:60000]
y2 <- as.matrix(y1)
Y=t(y2)

# Subset 16384 random samples from MNIST 
permutation = c(sample(2^10))
# Randomly shuffle the training data
X1 = X[, permutation]
y1 = Y[1, permutation]
y2 <- as.matrix(y1)
Y1=t(y2)
layersDimensions=c(784, 15,9, 10) 
retvalsSGD= L_Layer_DeepModel_SGD(X1, Y1, layersDimensions,
                                  hiddenActivationFunc='tanh',
                                  outputActivationFunc="softmax",
                                  learningRate = 0.001,
                                  optimizer="rmsprop",
                                  beta1=0.9,
                                  epsilon=10^-8,
                                  mini_batch_size = 512, 
                                  num_epochs = 5000 , 
                                  print_cost = True)


#Plot the cost vs iterations
iterations <- seq(0,5000,1000)
costs=retvalsSGD$costs
df=data.frame(iterations,costs)
ggplot(df,aes(x=iterations,y=costs)) + geom_point() + geom_line(color="blue") +
 ggtitle("Costs vs number of epochs") + xlab("No of epochs") + ylab("Cost")
```
##4.1b. Stochastic Gradient Descent with RMSProp - Octave


##5.1. Stochastic Gradient Descent  with Adam
Adaptive Moment Estimate is a combination of the momentum (1st moment) and RMSProp(2nd moment). The equations for Adam are below

v_{dW}^l = \beta_{1} v_{dW}^l + (1-\beta_{1})dW^{l}\\
v_{db}^l = \beta_{1} v_{db}^l + (1-\beta_{1})db^{l} \\

The bias corrections for the 1st moment

vCorrected_{dW}^l= \frac {v_{dW}^l}{1 - \beta_{1}^{t}}\\
vCorrected_{db}^l= \frac {v_{db}^l}{1 - \beta_{1}^{t}}


Similarly the moving average for RMSProp


s_{dW}^l = \beta_{2} s_{dW}^l + (1-\beta_{2})(dW^{l})^2\\
s_{db}^l = \beta_{2} s_{db}^l + (1-\beta_{2})(db^{l})^2 \\

sCorrected_{dW}^l= \frac {s_{dW}^l}{1 - \beta_{2}^{t}}\\
sCorrected_{db}^l= \frac {s_{db}^l}{1 - \beta_{2}^{t}}

The Adam Gradient Descent is 

W^{l} = W^{l} - \frac {\alpha vCorrected_{dW}^l}{\sqrt (s_{dW}^l + \epsilon) }\\
b^{l} = b^{l} - \frac {\alpha sCorrected_{db}^l}{\sqrt (s_{db}^l + \epsilon) } \\


The code snippet of Adam in R is included below


```{python}

```


##5.1a. Stochastic Gradient Descent  with Adam - Python
```{python cache=TRUE}
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
import sklearn.linear_model
import pandas as pd
import sklearn
import sklearn.datasets
exec(open("DLfunctions7.py").read())
exec(open("load_mnist.py").read())


training=list(read(dataset='training',path=".\\mnist"))
test=list(read(dataset='testing',path=".\\mnist"))
lbls=[]
pxls=[]
print(len(training))
#for i in range(len(training)):
for i in range(60000):
       l,p=training[i]
       lbls.append(l)
       pxls.append(p)
labels= np.array(lbls)
pixels=np.array(pxls)       
y=labels.reshape(-1,1)
X=pixels.reshape(pixels.shape[0],-1)
X1=X.T
Y1=y.T


# Create  a list of random numbers of 16384
permutation = list(np.random.permutation(2**10))
# Subset 16384 from the data
X2 = X1[:, permutation]
Y2 = Y1[:, permutation].reshape((1,2**10))
layersDimensions=[784, 15,9,10] 

parameters = L_Layer_DeepModel_SGD(X2, Y2, layersDimensions, hiddenActivationFunc='relu', 
                                   outputActivationFunc="softmax",learningRate = 0.01 ,
                                   optimizer="adam", beta1=0.9, beta2=0.9, epsilon = 1e-8,
                                   mini_batch_size =512, num_epochs = 5000, print_cost = True, figure="fig5.png")
```

##5.1b. Stochastic Gradient Descent  with Adam - Python
```{r fig6,cache=TRUE}
source("mnist.R")
source("DLfunctions7.R")
load_mnist()



x <- t(train$x)
X <- x[,1:60000]
y <-train$y
y1 <- y[1:60000]
y2 <- as.matrix(y1)
Y=t(y2)

# Subset 16384 random samples from MNIST 
permutation = c(sample(2^10))
# Randomly shuffle the training data
X1 = X[, permutation]
y1 = Y[1, permutation]
y2 <- as.matrix(y1)
Y1=t(y2)
layersDimensions=c(784, 15,9, 10) 

retvalsSGD= L_Layer_DeepModel_SGD(X1, Y1, layersDimensions,
                                  hiddenActivationFunc='tanh',
                                  outputActivationFunc="softmax",
                                  learningRate = 0.005,
                                  optimizer="adam",
                                  beta1=0.7,
                                  beta2=0.9,
                                  epsilon=10^-8,
                                  mini_batch_size = 512, 
                                  num_epochs = 5000 , 
                                  print_cost = True)

#Plot the cost vs iterations
iterations <- seq(0,5000,1000)
costs=retvalsSGD$costs
df=data.frame(iterations,costs)
ggplot(df,aes(x=iterations,y=costs)) + geom_point() + geom_line(color="blue") +
 ggtitle("Costs vs number of epochs") + xlab("No of epochs") + ylab("Cost")
```
