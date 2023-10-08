---
title: PyTorch教程
date: 2023-04-13 18:09:12
categories: 算法
tags: ["PyTorch"]
top: true
---

对日常Pytorch的使用进行归纳和总结。

<!-- more -->

## 张量

### 张量创建

```python
x = torch.empty(5, 3)  # 构建5✖️3矩阵，不初始化
x = torch.rand(5, 3)  # 构建5✖️3矩阵，随机初始化
x = torch.zeros(5, 3, dtype=torch.long)  # 构建5✖️3矩阵，全零初始化，设定参数类型
x = torch.tensor([0, 0]) # 直接使用数据构建张量
# 基于已存在张量创建新的张量
y = torch.randn_like(x, dtype=torch.float)
```

### 获取张量信息

```python
# 获取形状
shape = x.shape  # shape是属性
size = x.size()  # size是方法
# 获取tensor的value
x = torch.randn(1)
print(x)
print(x.item())
```

### 张量运算

```python
torch.add(x, y)
torch.add(x, y, out=result)
y.add_(x)  # inplace方法，y会发生改变
```

{% note info %}
    任何使张量发生变化的操作（in-place）都有一个前缀‘_’。
{% endnote %}

### 张量形状改变

```python
x = torch.randn(4, 4)
y = x.view(16)
z = x.view(-1, 8)
```

```python
# 增加维度，相当于tensorflow中的expand_dims
x = torch.randn(4, 4)
y = x.unsqueeze(0)
```

## 自动微分

### requires_grad

```python
# 创建张量，通过设置requires_grad=True来跟踪与它相关的计算，默认为False
x = torch.ones(2, 2, requires_grad=True)
y = x + 2
print(x)
print(y)
```

### 反向传播

```python
loss.backward()  # 反向传播
grad = x.grad  # 查看梯度
```

### 停止计算梯度

```python
# 可用.detach()来防止将来的计算被跟踪
out = out.detach()  # 来到out的梯度会被阻断

# 可将代码包裹在with torch.no_grad()中来停止自动求导
x = torch.randn(5, 3, requires_grad=True)
print((x ** 2).requires_grad)
with torch.no_grad():
    print((x ** 2).requires_grad)
```

## 定义网络

```python
import torch
import torch.nn as nn
import torch.nn.functional as F


# 定义网络
class Net(nn.Module):

    def __init__(self):
        super(Net, self).__init__()
        # 1 input image channel, 6 output channels, 5x5 square convolution kernel
        self.conv1 = nn.Conv2d(1, 6, 5)
        self.conv2 = nn.Conv2d(6, 16, 5)
        # an affine operation: y = Wx + b
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        # Max pooling over a (2, 2) window
        x = F.max_pool2d(F.relu(self.conv1(x)), (2, 2))
        # If the size is a square you can only specify a single number
        x = F.max_pool2d(F.relu(self.conv2(x)), 2)
        x = x.view(-1, self.num_flat_features(x))
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

    def num_flat_features(self, x):
        size = x.size()[1:]  # all dimensions except the batch dimension
        num_features = 1
        for s in size:
            num_features *= s
        return num_features


net = Net()
print(net)

# 模型可训练的参数可以通过调用 net.parameters() 返回
params = list(net.parameters())
print(len(params))
print(params[0].size())  # conv1's .weight

input = torch.randn(1, 1, 32, 32)
output = net(input)
print(output)

target = torch.randn(1, 10)
criterion = nn.MSELoss()

loss = criterion(output, target)

# 为了实现反向传播损失，我们所有需要做的事情仅仅是使用 loss.backward()。你需要清空现存的梯度，要不然梯度将会和现存的梯度累计到一起。
net.zero_grad()     # zeroes the gradient buffers of all parameters

print('conv1.bias.grad before backward')
print(net.conv1.bias.grad)

loss.backward()

print('conv1.bias.grad after backward')
print(net.conv1.bias.grad)

# 使用torch.optim更新网络
import torch.optim as optim

# create your optimizer
optimizer = optim.SGD(net.parameters(), lr=0.01)

# in your training loop:
optimizer.zero_grad()   # zero the gradient buffers
output = net(input)
loss = criterion(output, target)
loss.backward()
optimizer.step()    # Does the update
```

## 使用GPU

```python
# 定义模型
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
model.to(device)  # 移动模型到cuda

features = features.to(device)  # 移动数据到cuda
labels = labels.to(device)  # 或者 labels = labels.cuda() if torch.cuda.is_available() else labels
# 注意，模型不需要重新赋值，直接model.to(device)就可以，但数据要重新赋值

# 如果要使用多个GPU训练模型，编写如下代码，则模型会在每一个GPU上拷贝一个副本，并将数据平分到各个GPU上进行训练
if torch.cuda.device_count() > 1:
    model = nn.DataParallel(model)

# 清空cuda缓存，该方法在OOM时十分有用
torch.cuda.empty_cache()
```

## 数据并行

## 模型保存

### 状态字典state_dict

在PyTorch中，`torch.nn.Module`模型的可学习参数包含在模型的参数属性中，可通过`model.parameters()`进行访问。`state_dict`是Python字典对象，它通过键值对存储了模型的参数。

```python
# 定义模型
class TheModelClass(nn.Module):
    def __init__(self):
        super(TheModelClass, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

# 初始化模型
model = TheModelClass()

# 初始化优化器
optimizer = optim.SGD(model.parameters(), lr=0.001, momentum=0.9)

# 打印模型的状态字典
print("Model's state_dict:")
for param_tensor in model.state_dict():
    print(param_tensor, "\t", model.state_dict()[param_tensor].size())

# 打印优化器的状态字典
print("Optimizer's state_dict:")
for var_name in optimizer.state_dict():
    print(var_name, "\t", optimizer.state_dict()[var_name])
```

### 保存和加载模型

```python
# 保存
torch.save(model.state_dict(), PATH)

# 加载
model = TheModelClass(*args, **kwargs)
model.load_state_dict(torch.load(PATH))
model.eval()
```

在 PyTorch 中最常见的模型保存使‘.pt’或者是‘.pth’作为模型文件扩展名。  
请记住，在运行推理之前，务必调用model.eval()去设置 dropout 和 batch normalization 层为评估模式。如果不这么做，可能导致 模型推断结果不一致。

{% note class info %}
`load_state_dict()`函数只接受字典对象，而不是保存对象的路径。这就意味着在你传给`load_state_dict()`函数之前，必须反序列化你保存的`state_dict`。例如，你无法通过 model.load_state_dict(PATH)来加载模型。
{% endnote %}

### 保存更多参数（checkpoint）

```python
# 保存
torch.save({
            'epoch': epoch,
            'model_state_dict': model.state_dict(),
            'optimizer_state_dict': optimizer.state_dict(),
            'loss': loss,
            ...
            }, PATH)

# 加载
model = TheModelClass(*args, **kwargs)
optimizer = TheOptimizerClass(*args, **kwargs)

checkpoint = torch.load(PATH)
model.load_state_dict(checkpoint['model_state_dict'])
optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
epoch = checkpoint['epoch']
loss = checkpoint['loss']

model.eval()
# - or -
model.train()
```

当保存成 Checkpoint 的时候，可用于推理或者是继续训练，保存的不仅仅是模型的 state_dict 。保存优化器的 state_dict 也很重要, 因为它包含作为模型训练更新的缓冲区和参数。你也许想保存其他项目，比如最新记录的训练损失，外部的torch.nn.Embedding层等等。  
要保存多个组件，请在字典中组织它们并使用torch.save()来序列化字典。PyTorch 中常见的保存checkpoint 是使用 .tar 文件扩展名。  
要加载项目，首先需要初始化模型和优化器，然后使用torch.load()来加载本地字典。这里,你可以非常容易的通过简单查询字典来访问你所保存的项目。

当保存一个模型由多个torch.nn.Modules组成时，例如GAN(对抗生成网络)、sequence-to-sequence (序列到序列模型), 或者是多个模 型融合, 可以采用与保存常规检查点相同的方法。换句话说，保存每个模型的 state_dict 的字典和相对应的优化器。如前所述，可以通 过简单地将它们附加到字典的方式来保存任何其他项目，这样有助于恢复训练。

```python
# 保存
torch.save({
            'modelA_state_dict': modelA.state_dict(),
            'modelB_state_dict': modelB.state_dict(),
            'optimizerA_state_dict': optimizerA.state_dict(),
            'optimizerB_state_dict': optimizerB.state_dict(),
            ...
            }, PATH)

# 加载
modelA = TheModelAClass(*args, **kwargs)
modelB = TheModelBClass(*args, **kwargs)
optimizerA = TheOptimizerAClass(*args, **kwargs)
optimizerB = TheOptimizerBClass(*args, **kwargs)

checkpoint = torch.load(PATH)
modelA.load_state_dict(checkpoint['modelA_state_dict'])
modelB.load_state_dict(checkpoint['modelB_state_dict'])
optimizerA.load_state_dict(checkpoint['optimizerA_state_dict'])
optimizerB.load_state_dict(checkpoint['optimizerB_state_dict'])

modelA.eval()
modelB.eval()
# - or -
modelA.train()
modelB.train()
```

### 模型热启动加载部分参数

在迁移学习或者训练复杂大模型时，部分加载模型或者加载部分模型是常见的方法。模型热启动可以帮助模型更快的收敛，或者进行小步长的微调。

```python
# 保存
torch.save(modelA.state_dict(), PATH)

# 加载
modelB = TheModelBClass(*args, **kwargs)
modelB.load_state_dict(torch.load(PATH), strict=False)
```

无论是从缺少某些键的 state_dict 加载还是从键的数目多于加载模型的 state_dict 加载, 都可以通过在`load_state_dict()`函数中将`strict`参数设置为 False 来忽略非匹配键的函数。

如果要将参数从一个层加载到另一个层，但是某些键不匹配，主要修改正在加载的 state_dict 中的参数键的名称以匹配要在加载到模型中的键即可。

### 保存torch.nn.DataParallel模型

```python
# 保存
torch.save(model.module.state_dict(), PATH)
# 加载
torch.load_state_dict(torch.load(PATH))
```

`torch.nn.DataParallel`是一个模型封装，支持并行GPU使用。要普通保存 DataParallel 模型, 请保存`model.module.state_dict()`。

## 函数

### torch.tril

返回下三角矩阵，下三角中的元素保留，其他元素变为0。

```python
>>> import torch
>>> a = torch.randn(3, 3)
>>> a
tensor([[ 0.4925,  1.0023, -0.5190],
        [ 0.0464, -1.3224, -0.0238],
        [-0.1801, -0.6056,  1.0795]])
>>> torch.tril(a)
tensor([[ 0.4925,  0.0000,  0.0000],
        [ 0.0464, -1.3224,  0.0000],
        [-0.1801, -0.6056,  1.0795]])
>>> b = torch.randn(4, 6)
>>> b
tensor([[-0.7886, -0.2559, -0.9161,  0.2353,  0.4033, -0.0633],
        [-1.1292, -0.3209, -0.3307,  2.0719,  0.9238, -1.8576],
        [-1.1988, -1.0355, -1.2745, -1.7479,  0.3736, -0.7210],
        [-0.3380,  1.7570, -1.6608, -0.4785,  0.2950, -1.2821]])
>>> torch.tril(b)
tensor([[-0.7886,  0.0000,  0.0000,  0.0000,  0.0000,  0.0000],
        [-1.1292, -0.3209,  0.0000,  0.0000,  0.0000,  0.0000],
        [-1.1988, -1.0355, -1.2745,  0.0000,  0.0000,  0.0000],
        [-0.3380,  1.7570, -1.6608, -0.4785,  0.0000,  0.0000]])
>>> torch.tril(b, diagonal=1)
tensor([[-0.7886, -0.2559,  0.0000,  0.0000,  0.0000,  0.0000],
        [-1.1292, -0.3209, -0.3307,  0.0000,  0.0000,  0.0000],
        [-1.1988, -1.0355, -1.2745, -1.7479,  0.0000,  0.0000],
        [-0.3380,  1.7570, -1.6608, -0.4785,  0.2950,  0.0000]])
>>> torch.tril(b, diagonal=-1)
tensor([[ 0.0000,  0.0000,  0.0000,  0.0000,  0.0000,  0.0000],
        [-1.1292,  0.0000,  0.0000,  0.0000,  0.0000,  0.0000],
        [-1.1988, -1.0355,  0.0000,  0.0000,  0.0000,  0.0000],
        [-0.3380,  1.7570, -1.6608,  0.0000,  0.0000,  0.0000]])
```

## 其他

### 去除模型中的某一层

#### 使用自定义nn.Module替换指定层

自定义一个继承自nn.Module的类，并在forward方法中直接返回输入值，达到去除某一层的效果。

```python
import torch
import torch.nn as nn
import torchvision.models

class Identity(nn.Module):
    def __init__(self):
        super(Identity, self).__init__()

    def forward(self, x):
        return x

if __name__ == "__main__":
    resnet18_modify = torchvision.models.resnet18(pretrained=True)
    resnet18_modify.fc = Identity()
```

#### 将需删除的层指定为空的nn.Sequential()

```python
import torch
import torch.nn as nn
import torchvision.models

if __name__ == "__main__":
    resnet18_modify = torchvision.models.resnet18(pretrained=True)
    resnet18_modify.fc = nn.Sequential()
```

#### 用list保存，然后重组网络

```python
import torch
import torch.nn as nn
import torchvision.models

if __name__ == "__main__":
    resnet18_modify = torchvision.models.resnet18(pretrained=True)
    modules = list(resnet18_modify.children())[:-1]
    resnet18_modify = nn.Sequential(*modules)
```
