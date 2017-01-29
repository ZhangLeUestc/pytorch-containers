# pytorch-containers

This repository aims to help former Torchies more seemlessly transition to the "Containerless" world of 
[PyTorch](https://github.com/pytorch/pytorch) 
by providing a list of PyTorch implementations of [Torch Table Layers](https://github.com/torch/nn/blob/master/doc/table.md).

We will build our neural net from a simple nn.Sequential() module as this exists in both 
Torch and PyTorch and build up from there.  You will notice that as we add more and more  
complexity to our network, the Torch code becomes more and more verbose.  On the other hand,
thanks to autograd, the complexity of our PyTorch code does not increase at all.  


Note: As a result of full integration with Autograd, PyTorch requires networks to be defined in the following manner:
1. Define all layers to be used in the `__init__` method of your network
2. Combine them however you want in the `forward` method of your network

And that's all there is to it!

We will build upon a generic "TableModule" class that we define as:
```Python
class TableModule(nn.Module):
    def __init__(self):
        super(TableModule,self).__init__()
        self.layer1 = nn.Linear(5,5).double()
        self.layer2 = nn.Linear(5,10).double()
    def forward(self,x):
        ...
        ...
        ...
        return ...


##ConcatTable

A simple Torch example:  
```Lua
net = nn.ConcatTable()
net:add(nn.Linear(5,5))
net:add(nn.Linear(5,10))

input = torch.range(1,5):view(1,5)
net:forward(input)
```

PyTorch Conversion: 
```Python
class TableModule(nn.Module):
    def __init__(self):
        super(TableModule,self).__init__()
        self.layer1 = nn.Linear(5,5)
        self.layer2 = nn.Linear(5,10)
    def forward(self,x):
        y = [self.layer1(x),self.layer2(x)]
        return y
        
input = Variable(torch.range(1,5).view(1,5))
net = TableModule()
net(input)
```
As you can see, PyTorch allows you to apply each member module that would have been
part of your Torch ConcatTable, directly to the same input Variable.  This offers much more 
flexibility as your architectures become more complex.

Two other things to note: 
1. To work with autograd, we must wrap our input in a Variable
2. PyTorch requires us to add a batch dimension which is why we call `.view(1,5)` on the input


## ParallelTable

A simple Torch example:
```Lua
net = nn.ParallelTable()
net:add(nn.Linear(10,5))
net:add(nn.Linear(5,10))

input1 = Torch.range(1,10):view(1,10)
input2 = Torch.range(1,5):view(1,5)
output = net:forward{input1,input2}
```

In PyTorch:
```Python
class TableModule(nn.Module):
    def __init__(self):
        super(TableModule,self).__init__()
        self.layer1 = nn.Linear(10,5)
        self.layer2 = nn.Linear(5,10)
    def forward(self,x1,x2):
        y = [self.layer1(x1),self.layer2(x2)]
        return y
        
input1 = Variable(torch.range(1,10).view(1,10))
input2 = Variable(torch.range(1,5).view(1,5))
net = TableModule()
output = net(input)
```







