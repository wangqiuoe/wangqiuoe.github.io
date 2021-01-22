---
layout: post
title:  "Notes for Python Package Neural Tangents"
date:   2021-01-18 16:52:54 +0800
bibliography: reference.bib 
categories: jekyll update 
---
## about python
### `func(*x)`
if `x` is a tuple, or list, then will pass all elements of `x` to `func` instead of a tuple or list `x`, i.e., 

```python
x1 = ('F', 20, 'chinese')
x2 = ('M', 26, 'american')
x3 = ('M', 27, 'english')

def funcOut(*data):
    print(*data)
    names, ages, nationalities = zip(*data)
    print('----')
    print(names)
    print(ages)
    print(nationalities)

data = [x1,x2,x3]
funcOut(*data)

"""
('F', 20, 'chinese') ('M', 26, 'american') ('M', 27, 'english')
----
('F', 'M', 'M')
(20, 26, 27)
('chinese', 'american', 'english')
"""

```

### `setattr(obj, name, value)` 
Sets the named attribute on the given object to the specified value.

```python
class TestObj():
    param1 = 0

testObj = TestObj
print('testObj.param1 =', testObj.param1)
setattr(testObj, 'param1', 5)
print('testObj.param1 =', testObj.param1)

"""
testObj.param1 = 0
testObj.param1 = 5
"""
``` 

### Cholesky decomposition
* solve $$ A=LL^T $$
```python
from scipy.linalg import cho_factor
A = np.array([[9, 3, 1, 5], [3, 7, 5, 1], [1, 5, 9, 2], [5, 1, 2, 6]])
c, low = cho_factor(A, lower=True) # c is cholesky decomposition of A that cc'=A
np.allclose(np.matmul(np.tril(c), np.tril(c).T),A)
#True
```
* solve $$ Ax=b $$ given cho\_factor of A
```python
from scipy.linalg import cho_factor, cho_solve
A = np.array([[9, 3, 1, 5], [3, 7, 5, 1], [1, 5, 9, 2], [5, 1, 2, 6]])
c, low = cho_factor(A) #low:True
b=[1, 1, 1, 1]
x = cho_solve((c, low),b)
np.allclose(A @ x,b)  #True
```

## about stax
### `predict.py`
* func `k_inv_y`: return $$ (K_{dd} + \sigma^2\mathbb{I})^{-1}Y_d $$ where the inverse calculated by `scipy.linalg.cho_factor` and `scipy.linalg.cho_solve`, and the subscript $$d$$ denotes train set.
### `stax.py`
* `Dense()`, `Relu()` are all layer objects, which return `(init_fn, apply_fn, kernel_fn)`. Pay attention to `kernel_fu`, which realizes the forward propogation of recursive covariance function from layer to layer.
* `ABRelu()` is used to realize `Relu()` with `a=0, b=1` which is equal to `max(x,0)`. And it realizes the analytical function of intergation (equation 5 and 11 in [1]) when activation function is relu. It uses

$$
arctan(\frac{\sqrt{1-\nu^{2}}}{\nu}) = arccos(\nu).
$$

* `Erf()`: is similar to `Tanh()` as shown in picture, and  mathematically 

$$ erf(z) = \frac{2}{\sqrt{\pi}} \int^z_0e^{-t^2}dt $$

{: style="text-align:center"}
![](http://wangqiuoe.github.io/figures/tanh_erf.png){:width="500"}.

### `class Kernel`
#### Attribute:
* cov1: covariance of the first batch of inputs, $$ k(x,x) $$
* cov2: optional covariance of the second batch of inputs, $$ k(x',x') $$ .
* nngp: covariance between the first and second batches (NNGP), $$ k(x,x') $$.

## References
 - [1] [Deep Neural Networks as Gaussian Processes](https://arxiv.org/abs/1711.00165)

