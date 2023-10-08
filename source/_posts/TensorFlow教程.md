---
title: TensorFlow教程
date: 2023-04-13 18:09:33
categories: 算法
tags: ["TensorFlow"]
top: true
---

对日常TensorFlow的使用进行归纳和总结。

<!-- more -->

## Tensorflow1.x

### 变量初始化

#### tf.ones

生成指定shape的矩阵，并且所有元素设置为1

```python
a = tf.ones([3, 3], dtype=tf.float32)
```

#### tf.zeros

生成指定shape的矩阵，并且所有元素设置为0

```python
a = tf.zeros([3, 3], dtype=tf.float32)
```

#### tf.ones_like

生成和`tensor`同样维度的矩阵，里面的所有元素值为1

```python
a = tf.zeros([3, 3], dtype=tf.float32)
b = tf.ones_like(a, dtype=float32)
```

#### tf.zeros_like

生成和`tensor`同样维度的矩阵，里面的所有元素值为1

```python
a = tf.zeros([3, 3], dtype=tf.float32)
b = tf.zeros_like(a, dtype=float32)
```

#### tf.fill

生成指定`dims`的矩阵，里面所有元素的值为`value`

```python
a = tf.fill([3, 3], value=4.2)
```

#### tf.constant

生成指定`shape`的矩阵，里面所有元素的值为`value`

```python
a = tf.constant(2.43, shape=[3, 3], dtype=tf.float32)
```

#### tf.random_normal

生成指定`shape`的矩阵，里面的元素的值根据正态分布随机生成，均值为`mean`，标准差为`stddev`

```python
a = tf.random_normal([3, 3], mean=0.0, stddev=0.01, dtype=tf.float32)
```

#### tf.truncated_normal

和`tf.random_normal`类似，不过只保留[mean - 2✖️stddev, mean + 2✖️stddev]范围内的随机数

```python
a = tf.truncated_normal([3, 3], mean=0.0, stddev=0.01, dtype=tf.float32)
```

#### tf.random_uniform

生成指定`shape`的矩阵，里面的元素的值根据均匀分布随机生成，最小值为`minval`，最大值为`maxval`

```python
a = tf.random_uniform([3, 3], minval=-1, maxval=1, dtype=tf.float32)
```

#### tf.lin_space

生成等差数列，起始值为`start`，结束值为`stop`，共取`num`个值

```python
a = tf.lin_space(0.0, 6.0, 5)
```

#### tf.range

生成等差数列，起始值为start，结束值为stop，以间隔delta取值

```python
a = tf.range(0, 6, 1, dtype=tf.float32)
```

#### 使用自定义矩阵初始化variable

```python
value = [0, 1, 2, 3, 4, 5, 6, 7]
init = tf.constant_initializer(value)
x = tf.get_variable("x", shape=[8], initializer=init)
```

### 模型保存

#### 保存为checkpoint

```python
saver = tf.train.Saver(max_to_keep=3)
saver.save(sess, "model.ckpt", global_step=step)
```

#### 保存为pb模型

```python
constant_graph = tf.graph_util.convert_variables_to_constants(sess, sess.graph_def, output_node_names=["probs"])
tf.train.write_graph(constant_graph, logdir="", name="model.pb", as_text=False)
```

### 模型载入

#### 载入checkpoint

```python
init_vars = tf.train.list_variables(init_checkpoint_name)
assignment_map = collections.OrderedDict()
for (name, var) in init_vars:
    assignment_map[name] = name

tf.train.init_from_checkpoint(init_checkpoint_name, assignment_map)
```

#### 载入pb

```python
sess = tf.Session()
with tf.gfile.FastGFile("model.pb", "rb") as f:
    graph_def = tf.GraphDef()
    graph_def.ParseFromString(f.read())
    sess.graph.as_default()
    output = tf.import_graph_def(
        graph_def,
        input_map={"input_ids:0": input_ids},
        return_elements=["probs:0"]
    )
sess.run(ouput, feed_dict=feed_dict)
```

## Tensorflow2.x
