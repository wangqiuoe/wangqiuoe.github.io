---
layout: post
title:  "Notes-for-Python-Package-Neural-Tangents"
date:   2021-01-18 16:52:54 +0800
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
* solve ` A=LL^T ` 
```python
from scipy.linalg import cho_factor
A = np.array([[9, 3, 1, 5], [3, 7, 5, 1], [1, 5, 9, 2], [5, 1, 2, 6]])
c, low = cho_factor(A, lower=True) # c is cholesky decomposition of A that cc'=A
np.allclose(np.matmul(np.tril(c), np.tril(c).T),A)
#True
```
* solve ` Ax=b ` given cho\_factor of A
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
* func `k_inv_y`: return ` K_{dd}^{-1}Y_d ` where the inverse calculated by `scipy.linalg.cho_factor` and `scipy.linalg.cho_solve`


