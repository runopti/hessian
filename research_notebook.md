#2016/02/20

-Read https://en.wikipedia.org/wiki/Power_iteration

-Implemented power method in test folder. (powermethod.lua)

#2016/02/21

-'lapp' = a small and focused Lua module which aims to make standard command-line parsing easier and intuitive.

(http://lua-users.org/wiki/LappFramework)

-Made /src/models/cnn_mnist.lua  this is just a model.

-Started developing code 


#2016/02/22

-looks like in MNIST, the default learning rate is 0.05. (source: https://github.com/torch/demos/blob/master/train-a-digit-classifier/train-on-mnist.lua)

-and batchsize default is 10 for MNIST

-gradients with respect to the inputs of the module will be stored in gradInput, which is the output of updateGradInput(input, gradOutput) 

-When you want to initialize the weights the same way throughout one experiment, if you set something like: 

>torch.manualSeed(123)

then it's all good. because manualSeed(123) defines a sequence of random numbers.

If you do net = nn.Sequential() twice in your code, the initial weights will be different because it's looking the first element in the sequence of random numbers,
but the second call is looking at the second element in the sequence of random numbers 

-It looks like the parameter update happens at updateParameter(lr) in Module:sharedAccUpdateGradParameters in Module:backward()?? 

-In normal train() code as in mnist/cifar tutorials, model:backward() only calculates df/dw. The actual parameter updates happen in optim.sgd(feval, parameters, optimState) according to this: https://github.com/torch/optim/blob/master/sgd.lua

**Today's Tips**

-how to flatten tensors? 
> torch.norm(cparam[2]:view(cparam[2]:nElement()))

-how to time : 
```{python}
 local tic = torch.tic()
 [some job]
 torch.toc(tic) # this will produce the time difference between tic and now
```

-How to use :add()
> If torch.add(tensor1, tensor2)

```{python}
a:add(b) # accumulates all elements of b into a.
torch.add(y, a, b) puts a + b in y.
y:add(a, b) puts a + b in y.
y = torch.add(a, b) returns a new Tensor.
```

> If torch.add(tensor1, value, tensor2)

```{python}
x:add(value, y) multiply-accumulates values of y into x.
z:add(x, value, y) puts the result of x + value * y in z.
torch.add(x, value, y) returns a new Tensor x + value * y.
torch.add(z, x, value, y) puts the result of x + value * y in z
```

> If torch.add(tensor, value)

```{python}
Add the given value to all elements in the Tensor.
y = torch.add(x, value) returns a new Tensor.
x:add(value) add value to all elements in place.
```

-How to use variations of :add() such as :addmv(), :addr(), :addmm()

```{python}
torch.addmv(vec1, mat, vec2)
Performs a matrix-vector multiplication between mat (2D Tensor) and vec2 (1D Tensor) and add it to vec1

torch.addr(mat, vec1, vec2)
Performs the outer-product between vec1 (1D Tensor) and vec2 (1D Tensor).

torch.addmm(M, mat1, mat2)
Performs a matrix-matrix multiplication between mat1 (2D Tensor) and mat2 (2D Tensor).
```


#2016/2/23

-Checked that gradParameters are updated after model:backward(inputs, df_dx) in terminal but parameters not.

-Found out ipairs is an iterator function for Lua. Usage:
```{python}
for i, ver in ipairs(t) do   # t has to be a table; index starts from 1!!
    print(ver)
end
```



#2016/2/24

***Today's tips:***

-How to use :split()
```{python}
a = torch.LongTensor({4,5,2,1,7}):split(3) 
# will make  a table 
# such that {torch.LongTensor({4,5,2}), torch.LongTensor({1,7})} in a
```


-How to use :select()
```{python}
# if mat is a 2D matrix
mat:select(1, t) # selects t th row of the matrix.
mat:select(2, t) # selects t th column of the matrix. 
```

- Read the source code of confusion matrix. 
```{python}
self.valids[t] = self.mat[t][t] / self.mat:select(1,t):sum()
# the last part is summing up the t th row of the matrix. So this calculates the t th class accuracy and put it in self.valids[t].

self.unionvalids[t] = self.mat[t][t] / (self.mat:select(1,t):sum()+self.mat:select(2,t):sum()-self.mat[t][t])
# the last part is summing up the t th row and t th column. 
```

- The stuff that appears when you print confusion matrix is written here : function ConfusionMatrix:__tostring__()


