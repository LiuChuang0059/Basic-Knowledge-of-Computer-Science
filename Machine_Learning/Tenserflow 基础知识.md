
##  1. 核心概念

Tensorflow 框架主要由两个核心块组成 ， 一个是计算图 Graph，一个是执行 session。


* Graph : 图形就像一个中心枢纽，将所有变量、占位符、常量连接到操作。

* Session : 会话创建一个 runtime : 操作被执行， tensor 被计算， 它还分配内存并保存中间结果和变量的值。

Graph 用来创建计算图， Session 用来执行和计算节点。 这样做的好处： 在定义计算图的过程中，我们可以构造非常复杂的表达式，而无需直接计算它们并分配内存中所需的空间。
> 例如在 Numpy 中， 定义一个大的矩阵， trillion by a trillion, 我们会立即得到一个超出内存的错误。 在 TensorFlow中，我们定义一个 Tensor， 只包含数据 shape 和数据类型， 没有真实的数值。

声明和执行之间的这一核心区别非常重要，因为这允许TensorFlow将计算负载分布到连接到不同机器的不同设备(cpu、gpu、TPUs)上。

其次 ： 将计算拆分成小的、微分的部分，以方便自动求导，自动进行反向传播



```Python
>>> a = tf.zeros((int(1e10),int(1e10)))
>>> b =  np.zeros((int(1e12),int(1e12)))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: array is too big; `arr.size * arr.dtype.itemsize` is larger than the maximum possible size.
>>>

```



### 1. 计算图

所谓的计算图就是将计算的过程描述为一个有向的图， 有向图中包含节点和边。

计算图中的节点表示： 运算操作， 变量， 占位符（operations, variables, or placeholders.）

* 运算操作 ： 根据特定的规则创建或者操作数据， 简称 Ops
* 变量 ：represent shared, persistent state that can be manipulated by running Ops on those variables.


计算图中的边： 对应数据或者 多维数组，经由边流到不同的操作中，边讲信息从一个 节点传递到另一个

下面是一个简单的例子 ：

```python
a = 15
b = 5
prod = a * b
sum = a +b
res = prod / sum
print(res)
```

对每个操作(+ * / ) 创建节点

创建输入变量 a, b



![](https://i.loli.net/2019/08/22/AX76gxyYrhtpfb9.png)


**创建计算图的好处是什么?**

1. 计算图是一个更加抽象的方式藐视计算程序。 大多数计算程序主要由两部分组成 ： 基本操作一个 每个操作要被执行的顺序， 通常是逐行运行。这意味着对于上面的程<!--  -->序，我们需要🧵计算 a* b ,  计算完成后，才能计算 a + b 。 程序强调执行的顺序，但是计算图强调操作之间的依赖关系。 换句话说，关心的是操作的输出如何从一个流向下一个。

2. 这样的设计允许 **并行计算和不同驱动之间调度**。 观察我们上面的代码， 我们可以同时执行加法和乘法运算操作，因为两个操作不互相影响。所以我们可以根据图的拓扑性来合理的调度操作顺序，以最有效的方式执行。 可以在多个 GPU 甚至是多个机器上执行。TensorFlow正是这样做的，它可以将不依赖于彼此的操作分配给不同的核心，而实际上编写程序的人只需构造一个有向图，就可以将输入最小化。

3. 另一个好处是 **可移植性** 图是代码的一种独立于语言的表示形式。因此，我们可以用Python构建图形，保存模型，并使用另一种语言(比如c++)恢复模型，如果您想要快速地进行操作的话。


**将上面的小程序写成 TensorFlow 形式：**

```python
with tf.Session() as sess: # 执行完毕自动关闭 Session， 不需要
    a = tf.constant(15 , name='a') # name 用来未来查看图
    b = tf.constant(5 , name='b')
    prod = tf.multiply(a,b, name='Multiply')  # element-wise. 元素对应相乘 ， a,b type 相同
    sum_tf = tf.add(a,b, name='Add')
    res = tf.divide(prod, sum_tf, name= 'Divide')

    # run Session
    Out = sess.run(res)
    print(Out)

 ```

 TensorFlow为当前线程提供了一个默认图，它是同一上下文中所有API函数的隐式参数。一般来说，只依赖默认图形就足够了。然而，对于高级用例，我们也可以创建多个图。 所以我们直接创建一个 Constant tensor， 接着创建一些 operations。 这些都会自动添加到默认图之中。

 到目前位置，没有任何实际的数值传给相应的 tensor， 也没有执行任何的计算。 我们需要执行 session， 告诉 tensorflow 执行 Graph。

 我们已经创建一个 session 对象，我们需要做的就是调用 sess.run  传递一个需要被计算的 operation。 为了计算 res， 我们还需要计算 pord 和 sum_tf 以及 a,b


**下面我们使用 TensorBoard 可视化我们构建的 Graph**
```python
file_writer = tf.summary.FileWriter('logs', sess.graph)
```
之后在命令行
```sh
$ tensorboard --logdir /Users/liuchuang/anaconda3/envs/tensor_learning/logs
```
在 http://localhost:6006. 查看生成的 Graph


![](https://i.loli.net/2019/08/22/OYnFXG18LUyt4wd.png)

------




### 2.  图详解

计算图形不仅仅是一个模糊的、非物质的抽象;它是一个存在的计算对象，并且可以检查。如果我们完全在头脑中表示复杂的图，则很难调试它们，但是检查和调试实际的图对象就容易得多。

可以使用 tf.get_default_graph() 来获取图对象，返回一个指向默认全局图对象的指针。

```python
import tensorflow as tf
g = tf.get_default_graph()
print(g)

###### Output ######
<tensorflow.python.framework.ops.Graph object at 0x62879a080>
```


数学上的图的定义包括 边和节点。 tensorflow 中图的定义也是这样， 包含 tf.Operations （节点） 和 tf.Tensor（边）。

当我们创建一个节点的时候， 实际发生了是三个事情

* 集合所有指向新节点的 tf.Tensor 对象（输入边）

* 创建一个新节点， 一个 tf.Operation 对象

* 创建一个或者多个输出边 （tf.Tensor） 并返回指向他们的指针



#### 1. 我们有三种方法来理解这些部分是如何组织在一起的

```python
import tensorflow as tf
a = tf.constant(2, name='a')
b = tf.constant(3, name='b')
c = a + b

print("Our tf.Tensor objects:")
print(a)
print(b)
print(c)
```

Output
```
Our tf.Tensor objects:
Tensor("a:0", shape=(), dtype=int32)
Tensor("b_1:0", shape=(), dtype=int32)
Tensor("add_4:0", shape=(), dtype=int32)
```



1. 列出所有的节点:  tf.Graph.get_operations()
```python
g = tf.get_default_graph()
ops_list = g.get_operations()
print(ops_list)
```

```
[<tf.Operation 'a' type=Const>, <tf.Operation 'b' type=Const>, <tf.Operation 'add' type=Add>]
```

2. 检查节点： tf.Operation.inputs and tf.Operation.outputs 返回 两个tf.Tensor 列表（节点的输入输出边）

* tf.Operation.inputs
```python
print(list(a_op.inputs))
print(list(b_op.inputs))
print(list(c_op.inputs))
```
Output
```
[]
[]
[<tf.Tensor 'a:0' shape=() dtype=int32>, <tf.Tensor 'b_1:0' shape=() dtype=int32>]
```
看到 a,b 节点是没有输入的， add 节点有两个输入 a, b


* tf.Operation.outputs
```python
print(a_op.outputs)
print(b_op.outputs)
print(c_op.outputs)
```

```
[<tf.Tensor 'a:0' shape=() dtype=int32>]
[<tf.Tensor 'b_1:0' shape=() dtype=int32>]
[<tf.Tensor 'add_4:0' shape=() dtype=int32>]
```


3. 检查边：  tf.Tensor.op 返回 一个 tf.Operation 这个 tensor 作为其输出。 tf.Tensor.consumers() 返回一列 tf.Operations ， tensor 作为他们的输入

* tf.Operation objects  :  compressed form

```python
a_op = a.op
b_op = b.op
c_op = c.op

print("Our tf.Operation objects, printed in compressed form:")
print(a_op.__repr__())
print(b_op.__repr__())
print(c_op.__repr__())
```

Output
```
Our tf.Operation objects, printed in compressed form:
<tf.Operation 'a' type=Const>
<tf.Operation 'b_1' type=Const>
<tf.Operation 'add_4' type=Add>
```

* tf.Operation objects : pretty print

```python
print(c_op)
```
Output

```
name: "add_4"
op: "Add"
input: "a"
input: "b_1"
attr {
  key: "T"
  value {
    type: DT_INT32
  }
}
```

* 3. tf.Tensor.consumers()

```python
print(a.consumers())
print(b.consumers())
print(c.consumers())
```
Output
```
[<tf.Operation 'add_4' type=Add>]
[<tf.Operation 'add_4' type=Add>]
[]

```
看到 a, b  作为 add 的输入， 流向 add， add 没有下文流向


#### 2. 对 tf.Variable objects 的讨论

```python
import tensorflow as tf
a = tf.constant(2, name='a')
b = tf.get_variable('b', [], dtype=tf.int32)
c = a + b
```


1. 列出所有的节点:  tf.Graph.get_operations()

```python
g = tf.get_default_graph()
ops_list = g.get_operations()
print(b)
print('\n')
print(ops_list)
```
Output

```
<tf.Variable 'b:0' shape=() dtype=int32_ref>

[<tf.Operation 'a' type=Const>, <tf.Operation 'b/Initializer/zeros' type=Const>, <tf.Operation 'b' type=VariableV2>, <tf.Operation 'b/Assign' type=Assign>, <tf.Operation 'b/read' type=Identity>, <tf.Operation 'add' type=Add>]
```

2. Variable 详细组成

Variable 对象实际上是4个操作的组合（以及相应的张量）

两个是通过 tf.Operations

```python
print("the core : {} ".format(b.op.__repr__()))
print("the initializer: {}".format(b.initializer.__repr__()))
```
Output
```
the core : <tf.Operation 'b' type=VariableV2>
the initializer: <tf.Operation 'b/Assign' type=Assign>
```

两个是通过 tf.Tensors

```python
print("the initial-value: {}".format(b.initial_value))
print("the current-value: {}".format(b.value()))
```
Output
```
the initial-value: Tensor("b/Initializer/zeros:0", shape=(), dtype=int32)
the current-value: Tensor("b/read:0", shape=(), dtype=int32)
```

3. 输入输出

tf.Variable core-op 没有输入， 一个张量形式输出

```python
print(list(b.op.inputs))
print(b.op.outputs)
```
Output
```
[]
[<tf.Tensor 'b:0' shape=() dtype=int32_ref>]
```

总的来说，你可以假设一个tf.Variable 是你在任何给定情况下想要的东西。例如，如果您想为变量赋值，它将解析为core-op;如果要在计算中使用该变量，它将解析为current-value-op;等。



#### 3. 多个图

开启 session 会默认创建一个 graph，通常这是足够的，但是实际上是可以创建多个 graph 的

* 多个图就需要多个 session，

* 如果不通过python/numpy传递数据，就不能在它们之间传递数据，而python/numpy在分布式环境中不起作用

* 不推荐使用多个图



------

### 3. constants :
* 不可改变的参数，常用来定义常量, 返回值是 tf.tensor
```python
tf.constant(
    value,
    dtype=None,
    shape=None,
    name='Const',
    verify_shape=False
)
```

> 常见的操作

```python
tf.zeros([2, 3], tf.int32) ==> [[0, 0, 0], [0, 0, 0]]
# input_tensor is [[0, 1], [2, 3], [4, 5]]
tf.zeros_like(input_tensor) ==> [[0, 0], [0, 0], [0, 0]]

tf.ones(shape, dtype=tf.float32, name=None)
tf.ones_like(input_tensor, dtype=None, name=None, optimize=True)

tf.fill(dims, value, name=None)
tf.fill([2, 3], 8) ==> [[8, 8, 8], [8, 8, 8]]
```

> 生成序列

```python
tf.lin_space(10.0, 13.0, 4) ==> [10. 11. 12. 13.]

tf.range(3, 18, 3) ==> [3 6 9 12 15]
tf.range(5) ==> [0 1 2 3 4]
```

> **注意 tensor 是不可以迭代的**
```python
for _ in tf.range(4): # TypeError

```

----

### 4.  placeholders and feed_dict
* 允许从外部导入数据， 不需要被初始化。 只需要简单的定义数据的类型和 shape。可以将他看作图中的空节点，数据之后会填补上。 占位符通常用于 输入和 labels



一个更有价值的应用程序可能需要构造一个计算图，**它要能够接受输入**，以某种方式处理它，然后返回一个输出。

placeholder 是一种用来接受外部输入的节点

```python

input_placeholder = tf.placeholder(tf.int32)
with tf.Session() as sess:
    print(sess.run(input_placeholder))
```
```
InvalidArgumentError: You must feed a value for placeholder tensor 'Placeholder_2' with dtype int32
```

直接运行报错， 需要我们输入以一个值， 使用 **feed_dict**
```python
input_placeholder = tf.placeholder(tf.int32)
with tf.Session() as sess:
    print(sess.run(input_placeholder, feed_dict={input_placeholder: 3}))

```

feed_dict 的输入格式： key ：是图中占位符的节点变量(实际上是指向占位符节点的指针)， value: 通常是 标量或者 numpy arrays


----

### 5.  Variable ：
* 变量可以在任何时间点进行修改，变量必须在 session 运行图形之前进行初始化。它们表示图中可更改的参数。变量的一个很好的例子就是神经网络中的权重或偏差。
> 发现 Variable 是大写字母开头， 因为 tf.Variable  是一个类 包含很多 opration

模型中的参数就是变量。 在训练模型的时候，希望能偶每步都能更新参数，在测试的时候，希望模型的参数保持固定。

创建变量： tf.get_variable(name, shape)

* name : string , 用来区分变量对象， 要保证在全局图中没有重复

* shape； 张量的形状， 标量就是 []

```python
count_variable = tf.get_variable("count_1", shape=[])
with tf.Session() as sess:
    print(sess.run(count_variable))
```

```
FailedPreconditionError                   Traceback (most recent call last)
Attempting to use uninitialized value count_1
```

当变量节点第一次被创建的时候， 只储存一个 null ， 计算他就会抛出错误， 需要传递值进入才能进一步计算。

🈶️两种给变量传递值的方法

1.  tf.assign(target, value)

* 作用通常就是简单的赋值



```python
count_variable = tf.get_variable("count_2", shape=[])
zero_node = tf.constant(0.)
assign_node = tf.assign(count_variable, zero_node)
with tf.Session() as sess:
    sess.run(assign_node)
    print(sess.run(count_variable))

#######Output######
0.0
```


2. initializer

```python
const_init_node = tf.constant_initializer(0.)
count_variable = tf.get_variable("count_3", [], initializer=const_init_node)
with tf.Session() as sess:
    print(sess.run(count_variable))
```

```
FailedPreconditionError: Attempting to use uninitialized value count_3
	 [[N
```

添加了 initializer 之后发现还是报错， 这是因为tensorflow 中计算图和session 是分开的。
我们设置 initializer 让 get_variable 指向 const_init_node , 这个命令实际上只是在图中在两个节点之间加了一个连接， 变量的值仍然是 null ， 我们需要让 session 执行变量值的更新。

```python
const_init_node = tf.constant_initializer(0.)
count_variable = tf.get_variable("count_4", [], initializer=const_init_node)
init = tf.global_variables_initializer()
with tf.Session() as sess:
    sess.run(init)
    print(sess.run(count_variable))
```

tf.global_variables_initializer() 会自动对每一个 tf.initializer添加其对应的关联。
run(init) 的时候 告诉每个 initializer 初始化变量


**完整代码**

```python
import tensorflow as tf
from tensorflow.python.framework import ops


weights = tf.Variable(tf.random_normal([2,3]), name = "weights")
biases = tf.Variable(tf.zeros([3]), name = "biases")
custom_variable = tf.Variable(tf.zeros([3]), name = "custom")

all_variables_list = ops.get_collection(ops.GraphKeys.GLOBAL_VARIABLES)

print(all_variables_list)

#############output######
[<tf.Variable 'weights:0' shape=(2, 3) dtype=float32_ref>, <tf.Variable 'biases:0' shape=(3,) dtype=float32_ref>, <tf.Variable 'custom:0' shape=(3,) dtype=float32_ref>]
```

```python
############# 自定义初始化 #########
# 只初始化一部分变量

variable_list_custom = [weights , custom_variable]

init_custom_op = tf.variable_initializer(var_list = variable_list_custom)




############ 全局初始化 ##########
# Method 1
init_all_op = tf.global_variables_initializer()

# Method 2
init_all_op = tf.variable_initializer(var_list = all_variables_list)




######### 使用其他的变量进行初始化 #######
# 创建另一个变量， 和 weights 一样
weightsNew = tf.Variable(weights.initialized_value(), name = "WeightsNew")
init_WeightsNew_op = tf.variables_initializer(var_list=[WeightsNew])

```

```python
with tf.Session() as sess:
    # Run the initializer operation.
    sess.run(init_all_op)
    sess.run(init_custom_op)
    sess.run(init_WeightsNew_op)
```


----

### 6. Operations ：
* 图中的节点用来执行 tensor 计算的
> 类似于 numpy 中的计算操作

```python
a = tf.constant([2, 2], name='a')
b = tf.constant([[0, 1], [2, 3]], name='b')
with tf.Session() as sess:
	print(sess.run(tf.div(b, a)))             ⇒ [[0 0] [1 1]]
	print(sess.run(tf.divide(b, a)))          ⇒ [[0. 0.5] [1. 1.5]]
	print(sess.run(tf.truediv(b, a)))         ⇒ [[0. 0.5] [1. 1.5]]
	print(sess.run(tf.floordiv(b, a)))        ⇒ [[0 0] [1 1]]
	print(sess.run(tf.realdiv(b, a)))         ⇒ # Error: only works for real values
	print(sess.run(tf.truncatediv(b, a)))     ⇒ [[0 0] [1 1]]
	print(sess.run(tf.floor_div(b, a)))       ⇒ [[0 0] [1 1]]
```

----


### 7. Session

会话的作用是处理内存分配和优化，能够实际执行由图形指定的计算。 可以将计算图看作一个模版， 他列出了我们所有要做的步骤。 为了执行这个图， 我们需要开启一个会话， 让我们逐个节点的遍历模版，同时分配一组内存记录输出。

> The session contains a pointer to the global graph, which is constantly updated with pointers to all nodes. That means it doesn’t really matter whether you create the session before or after you create the nodes. 2


创建 session 对象之后，使用 sess.run(node) 返回节点的值， tensorflow 会执行所有必须的步骤去计算值。

```python
import tensorflow as tf
two_node = tf.constant(2)
three_node = tf.constant(3)
sum_node = two_node + three_node
with tf.Session() as sess:
    print(sess.run(sum_node))
##### OUtput ######
5
```

也可以传递到 session 一个列表

```python
two_node = tf.constant(2)
three_node = tf.constant(3)
sum_node = two_node + three_node
with tf.Session() as sess:
    print(sess.run([two_node,sum_node]))
###### Output#######
[2, 5]
```
通常, sess.run() 调用往往是TensorFlow最大的瓶颈之一，因此调用次数越少越好。只要有可能，在一个ses .run()调用中返回多个项目，而不是进行多个调用。


-----


### 8. 计算路径：
```python
input_placeholder = tf.placeholder(tf.int32)
three_node = tf.constant(3)
sum_node = input_placeholder + three_node
with tf.Session() as sess:
    print(sess.run(three_node))
    print(sess.run(sum_node))

```
```
Output

3
---------------------------------------------------------------------------
InvalidArgumentError
You must feed a value for placeholder tensor 'Placeholder_4' with dtype int32

```


发现我们即使没有计算 input_placeholder ，程序还是报错。 这是因为当我们在一个节点 A上调用sess.run 的时候， 我们需要计算 A 所有依赖的节点(B,C 等)， 如果 B，C 还有依赖节点， 还要进一步计算，知道没有前序节点。

所以计算 sum 的时候， 需要计算 input_placeholder， 和 three_node .当计算到 input_placeholder 的时候，就会报错。

另外， 由于图结构姐， 我们不需要激素哪所有的节点，所以计算  print(sess.run(three_node))， 没有报错

> Tensorflow 仅通过必需的节点自动路由计算这一事实是它的巨大优势。如果计算图非常大并且有许多不必要的节点，它就能节约大量运行时间。它允许我们构建大型的“多用途”图形，这些图形使用单个共享的核心节点集合根据采取的计算路径来做不同的任务。对于几乎所有应用程序而言，根据所采用的计算路径考虑 sess.run() 的调用方法是很重要的。


----

### 9. Type of data

一个 tensor 只能是一种数据类型 tensor.dtype， 但是可以改变数据类型 通过 tf.cast

```python
type_float = tf.constant(3.123456789, tf.float32)
type_int = tf.cast(type_float, dtype=tf.int32)
```


----
----


## 2. 从头开始实现 tensorflow 的 API

### 1. Graph

Graph 包含一系列 Operations 对象(计算单元)。 除此之外， Graph 还包括一系列的 variables and placeholders: 表示在操作之间流动的数据元。

所以我们需要三个列表来储存这些对象。
此外，我们的图需要一个名为as_default的方法，我们可以调用该方法来创建一个全局变量，该变量用于存储当前的图实例。这样，在创建 操作、占位符或变量时，我们就不必传递对图的引用。

```python
class Graph():
	"""
	"""

	def __init__(self):
		self.operations = []
		self.variables = []
		self.placeholders = []
		self.constants = []

	def as_default(self):
		global _defalut_graph
		_defalut_graph = self
```


### 2. Operations

Operations 是计算图中的节点， 执行 tensor 的计算操作。 一个Operations 需要有以下的特征：

* a list of input_nodes

* 实现一个前向的方程

* 实现一个反向的方程

* 记录输出值

* 将 Operations自身添加到 默认图中

input_nodes 是将要输入到 operation 中的数据。

forward ： 计算前向传播中的输出值， backward ： 计算反向传播中 操作的梯度。 所以一个操作要学会如何计算一个输出以及相对于每个输入变量的局部梯度。


```python

class Operations():
	def __init__(self, input_nodes = None):
		self.input_nodes = input_nodes
		self.output = output

		_defalut_graph.operations.append(self)


	def forward(self):
		pass

	def backward(self):
		pass

```

其中 forward 需要对于每一个 operation 进行特定的实现。 为了简单， 我们只考虑 上面的例子，创建一个 BinaryOperation

```python
class BinaryOperation(Operations):
	def __init__(self, a,b):
		super().__init__([a,b]) # 将 a, b 初始化作为 input_nodes

```

现在我们可以基于 BinaryOperation 实现一些  + -* /  的操作。 对于每种操作，我们都假定输入的 a,b 要么是 scalers， 要么是 arrays， 这样可以直接使用 numpy 中已经实现的计算方法

```python
class Add(BinaryOperation):
	"""
  	Computes a + b, element-wise
  	"""
  	def forward(self, a,b):
  		return a + b

  	def backward(self,upstream_grad):
  		raise NotImplementError
```


```python
class Multiply(BinaryOperation):
	"""
  	Computes a * b, element-wise
  	"""
  	def forward(self, a,b):
  		return a * b

  	def backward(self,upstream_grad):
  		raise NotImplementError
```


```python
class Divide(BinaryOperation):
	"""
  	Computes a / b, element-wise
  	"""
  	def forward(self, a,b):
  		return np.true_divide(a,b)

  	def backward(self,upstream_grad):
  		raise NotImplementError
```


```python
class matmul(BinaryOperation):
	"""
  	Multiply  a * b, element-wise
  	"""
  	def forward(self, a,b):
  		return a.dot(b)

  	def backward(self,upstream_grad):
  		raise NotImplementError
```

### 3. placeholders

计算图中不是所有的节点都是 operation, 有些是图的 输入， 当我们计算 graph 的输出的时候， 需要提供这些数值。

在 tensorflow 中，有不同的方式向 图提供数据，：Placeholder, Variable or Constant

```python

class Placeholder():
	def __init__(self):
		self.value = None
		_defalut_graph.placeholders.append(self) # 将自己添加到 默认图中
```

placeholders的值是在 session 执行的时候提供， 那时候我们再实现

### 4. Constant

一旦被初始化之后，不能进行更改。 variables 恰恰相反， 表示可变的参数，例如 权重和 bias。 这种区别非常重要，因为变量在向后传递期间进行了优化，而常量和占位符则没有。所以我们不能简单地用一个变量来表示常数。占位符是有用的，但也感觉有点被误用了。为了提供这样的特性，我们引入了常量。

```python

class Constant():
  def __init__(self, value=None):
    self.__value = value
    _default_graph.constants.append(self)

  @property    # 使用装饰器 检查 value 的值
  def value(self):
    return self.__value

  @value.setter
  def value(self, value):
    raise ValueError("Cannot reassign value.")
```

上述的代码，可以公开查询 value 的值，但是当想更改的时候，会 出现 ValueError。


### 5. Variable

```python
class Variable():
  def __init__(self, initial_value=None):
    self.value = initial_value
    _default_graph.variables.append(self)
```



### 6. Session

到目前为止，我们已经可以构建计算图了，我们已经完成了大部分的板块，最后需要完成的就是 Session

session 执行的时候，需要获取两个参数 :  operation , feed_dict （为占位符提供数值）

为了计算输出， 我们需要对节点进行拓扑排序，确保我们以正确的顺序执行。 这个顺序可以简单表示为 有向无环图中节点的顺序。

算法：

1. 选择最后一个要计算的节点 ，就是 传递给 Session.run() 的

2. 对每个节点的 input_nodes 执行深度优先搜索

3. 知道到达一个节点没有输入节点， 将那个节点标记为 访问过

```python
def topology_sort(operation):
    ordering = []
    visited_nodes = set()

    def recursive_helper(node):
      if isinstance(node, Operation):
        for input_node in node.input_nodes:
          if input_node not in visited_nodes:
            recursive_helper(input_node)

      visited_nodes.add(node)
      ordering.append(node)

    # start recursive depth-first search
    recursive_helper(operation)

    return ordering
```
获得 节点的顺序， 我们接下来就是实现 run ： 对于不同的节点，分别计算他们的输出

```python
class Session():
  def run(self, operation, feed_dict={}):
    nodes_sorted = topology_sort(operation)

    for node in nodes_sorted:
      if type(node) == Placeholder:
        node.output = feed_dict[node]
      elif type(node) == Variable or type(node) == Constant:
        node.output = node.value
      else:
        inputs = [node.output for node in node.input_nodes]
        node.output = node.forward(*inputs)

    return operation.output
```


----

### 7. 完整代码

将上面的代码存为 tf_api.py 文件 然后导入这个模块开始使用

```python

import tf_api as tf
import numpy as np
# create default graph
tf.Graph().as_default()

# construct computational graph by creating some nodes
a = tf.Constant(15)
b = tf.Constant(5)
prod = tf.multiply(a, b)
sum = tf.add(a, b)
res = tf.divide(prod, sum)

# create a session object
session = tf.Session()

# run computational graph to compute the output for 'res'
out = session.run(res)
print(out)

```

-----


## 3.  Tensorflow 创建模型常见问题

### 1. keras or tf.keras ？

目前 keras 已经是 tensorflow 的一部分 , 建议使用 tf.keras 而不是 keras ，因为这样可以更好的和 tensorflow 其他的 api 集成， 例如 eagerexecution， tf.data


```python
import tensorflow as tf
from tensorflow import keras
```

有三种方法创建一个模型


* 序列式

* 函数式

* 子类化


----


### 2. 优化器

先看一个简单的 线性回归问题模型


```python
import tensorflow as tf

### build the graph
## first set up the parameters
m = tf.get_variable("m", [], initializer=tf.constant_initializer(0.))
b = tf.get_variable("b", [], initializer=tf.constant_initializer(0.))
init = tf.global_variables_initializer()

## then set up the computations
input_placeholder = tf.placeholder(tf.float32)
output_placeholder = tf.placeholder(tf.float32)

x = input_placeholder
y = output_placeholder
y_guess = m * x + b

loss = tf.square(y - y_guess)

## finally, set up the optimizer and minimization node
optimizer = tf.train.GradientDescentOptimizer(1e-3)
train_op = optimizer.minimize(loss)

### start the session
sess = tf.Session()
sess.run(init)

### perform the training loop
import random

## set up problem
true_m = random.random()
true_b = random.random()

for update_i in range(100):
  ## (1) get the input and output
    input_data = random.random()
    output_data = true_m * input_data + true_b

  ## (2), (3), and (4) all take place within a single call to sess.run()!
    _loss, _ = sess.run([loss, train_op], feed_dict={input_placeholder: input_data, output_placeholder: output_data})
    print(update_i, _loss)
```

其中 optimizer = tf.train.GradientDescentOptimizer(1e-3) 不是在图中加入一个节点，只是简单的创建一个 python 对象。

train_op = optimizer.minimize(loss) 则是向图中加入一个节点，在变量 train_op 中储存一个指向他的指针。
train_op 节点没有输出，但是有一个非常复杂的 side effect

train_op 通过计算图对他的输入 loss 进行反向传播，寻找变量节点。 对于每一个在反向路径上的节点， 计算这个 loss 对这个变量的梯度下降。然后根据梯度下降，更新这个变量的值。（原始值 - 梯度* 学习率）

当我们调用 sess.run(train_op)的时候， 对所有的变量进行了一次梯度下降。



-----


### 3. tf.Print -- Debugging

tf.Print 实际上是一种 tensorflow 节点，既有输出也有 side effect 。
它需要两个输入内容

* 一个要被拷贝的节点： 可以是图中任何一个节点。

* 一个要 print 的列表： side effect


```python
import tensorflow as tf
two_node = tf.constant(2)
three_node = tf.constant(3)
sum_node = two_node + three_node
print_sum_node = tf.Print(sum_node, [two_node, three_node])
sess = tf.Session()
print sess.run(print_sum_node)
```

![](https://i.loli.net/2019/08/25/DhogcljAZGBbmOW.png)

注意， tf.Print 中 打印只是换一个 side effect ，只发生在计算流经 tf.Print 节点的时候。
如果 tf.Print 节点不再计算的路径中， 那么什么也不会被打印。

> 即使 tf.Print 节点正在复制的原始节点位于计算路径上，但 tf.Print 节点本身可能不是。这个问题要注意！当这种情况发生时，它会让你感到非常沮丧，你需要费力地找出问题所在。一般来说，最好在创建要复制的节点后立即创建 tf.Print 节点。

```python

import tensorflow as tf
two_node = tf.constant(2)
three_node = tf.constant(3)
sum_node = two_node + three_node
### this new copy of two_node is not on the computation path, so nothing prints!
print_two_node = tf.Print(two_node, [two_node, three_node, sum_node])
sess = tf.Session()
print(sess.run(sum_node))

###### Output ######
5

```

![](https://i.loli.net/2019/08/25/Adwz4hsNXVO7ta6.png)

-----

### 4. 向 tensorflow 导入数据

#### 1. 有两种常见的方式导入数据

1. 导入到 内存： 将所有数据导入到一个 array
> 数据较小

2. 使用 tensorflow data pipline ： 使用 tensorflow 内置的 API导入数据，将数据输入到机器学习模型中。
当数据集很大的时候，这个方法表现的更好。

tensorflow 的pipeline 会分批或者分块的导入数据。 这样允许模型进行分布式运算

#### 2. 创建 tensorflow pipeline

1. 创建数据
```python
import numpy as np
x_input = np.random.sample((1,2))
```

2. 创建 placeholder

注意指定形状
```python
x = tf.placeholder(tf.float32, shape = [1,2], name = 'x')
```

3. 定义数据集 ： 填充 placeholder
```python
dataset = tf.data.Dataset.from_tensor_slices(x)
```

4. 创建 pipeline
```python
iterator = dataset.make_initializable_iterator()
get_next = iteraror.get_next()
```

初始化 pipeline ， 调用迭代器获取下一批数据

5. 执行

```python
with tf.Session() as sess:
    # feed the placeholder with data
    sess.run(iterator.initializer, feed_dict={ x: x_input })
    print(sess.run(get_next)) # output [ 0.52374458  0.71968478]

```

-----
----

# 参考

1. [斯坦福CS20 TensorFlow学习笔记(1)：Overview of Tensorflow
Share this](http://imshuai.com/cs20-tensorflow-notes-1)

2. [Understand TensorFlow by mimicking its API from scratch](https://medium.com/@d3lm/understand-tensorflow-by-mimicking-its-api-from-scratch-faa55787170d)

3. [这是我看过解释TensorFlow最透彻的文章](https://juejin.im/post/5b345a49f265da599c561b25)

4. TensorFlow 实战， 黄文坚

5. [TensorBoard：图的直观展示](https://www.tensorflow.org/guide/graph_viz)

6. [有向图](https://zh.wikipedia.org/wiki/%E5%9B%BE_%E6%95%B0%E5%AD%A6#%E6%9C%89%E5%90%91%E5%9B%BE%E5%92%8C%E7%84%A1%E5%90%91%E5%9B%BE)

7. [使用@property](https://www.liaoxuefeng.com/wiki/897692888725344/923030547069856)

8. [斯坦福CS20 TensorFlow学习笔记(2)：TensorFlow Ops](https://imshuai.com/cs20-tensorflow-notes-2)

9. [Demystify the TensorFlow APIs](https://medium.com/google-developer-experts/demystify-the-tensorflow-apis-57d2b0b8b6c0)

10. [Tensorflow: The Confusing Parts (1)](https://jacobbuckman.com/post/tensorflow-the-confusing-parts-1/)

11. [Tensorflow: The Confusing Parts (2)](https://jacobbuckman.com/post/tensorflow-the-confusing-parts-2/)

12. [More on Graph Inspection](https://jacobbuckman.com/post/graph-inspection/)

13. [What is TensorFlow? Introduction, Architecture & Example](https://www.guru99.com/what-is-tensorflow.html)

14. [TensorFlow Basics: Tensor, Shape, Type, Graph, Sessions & Operators](https://www.guru99.com/tensor-tensorflow.html)


15. [machinelearningmindset TensorFlow-Course](https://github.com/machinelearningmindset/TensorFlow-Course/blob/master/codes/python/1-basics/variables/code/variables.py)



