---
title: Pythonの进阶写法
data: 2026-01-10 
author: 空空杯
excerpt: 居然还能这么写
categories: 
  - 芝士,与你分享
---
## 1.循环嵌套的合并
源码:
```python
tokens = [token for line in tokens for token in line]
```
解释码:
```python
tokens_2d = [['I','love','Python'], ['Hello','world']] # 原始2D列表

tokens_1d = [] # 初始化空列表存1D结果

# 外层循环：遍历2D列表的每一行（每个子列表）
for line in tokens_2d:
    # 内层循环：遍历当前行的每个词元
    for token in line:
        # 将词元添加到1D列表中
        tokens_1d.append(token)
print(tokens_1d)  # 输出：['I', 'love', 'Python', 'Hello', 'world']
```

## 2.下划线的奇妙用法
1. 单下划线
```python
_var # 约定俗成的私有成员,实际上Python并无私有变量这一概念

# 有时还需要配合@property来实现变量的只读属性
@property
def token_freqs(self):
    return self._token_freqs

vocab = Vocab(tokens=[['I','love'], ['I','Python']])
# 访问词频排序结果（无需加括号）
print(vocab.token_freqs)  # 输出：[('I', 2), ('love', 1), ('Python', 1)]

# 尝试修改（会报错）
vocab.token_freqs = [('fake', 999)]
# 报错：AttributeError: can't set attribute 'token_freqs'
```
2. 单末尾下划线\
```python
var_ # 用来避免与Python关键字产生命名冲突
```
3. 双前导下划线
```python
__var # 为了防止变量在子类中被重写
```
4. 双前导和双末尾下划线 
```python
__var__ # 神奇方法,类似于重构Python中已有的方法
```
5. 单下划线 
```python
# 用于作为临时变量或者无意义变量的名称
>>> car = ('red', 'auto', 12, 3812.4)
>>> color, _, _, mileage = car

>>> color
'red'
>>> mileage
3812.4
>>> _
12
```
## 3.匿名函数
源码:
```python
sorted(counter.items(), key=lambda x: x[1],reverse=True)
```
lambda语法解释:
```python
# argument可以为若干个参数
# expression为若干个表达式的返回值
lambda arguments: expression

x = lambda a : a + 10
print(x(5)) # 输出结果为15
```
## 4.字典快速生成
源码:
```python
self.token_to_idx = {token: idx for idx, token in enumerate(self.idx_to_token)}
```
解释码:
```python
self.token_to_idx = {}
for idx, token in enumerate(self.idx_to_token):
    self.token_to_idx[token] = idx  # 键=词元，值=索引
```