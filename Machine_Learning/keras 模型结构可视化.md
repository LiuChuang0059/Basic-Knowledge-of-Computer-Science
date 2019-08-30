![](https://github.com/LiuChuang0059/large_file/blob/master/pic/0o5yi.jpg)

---

### 1. 安装 Graphviz

```python
brew install graphviz
```


```python
pip install graphviz
```


```python
pip install pydot_ng
```

Go to keras/utils/vis_util.py (this is the file showing error)
You will see something like this.


```python
try:
import pydot
except ImportError:
pydot = None
```
Change import pydot to **import pydot_ng as pydot**


### 2. 可视化并保存 图片

```python
from keras.utils.vis_utils import plot_model

plot_model(model,to_file='model_1.png',show_shapes=True)
```

* 得到下面的结构图

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/wfvz8.jpg)



------

### 3. 附注

* 只需要简单看一下结构，和参数

```python
model.summary()
```

* 得到下面的图

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/vhbfv.jpg)

-----





### 参考


https://github.com/XifengGuo/CapsNet-Keras/issues/7

https://www.jianshu.com/p/275aad639b5e

https://www.jianshu.com/p/3a97c0a43beb