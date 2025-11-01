kimi-k2

Python 标准库 `math`（或 `numpy`）里比较两个**浮点数是否“足够接近”**的函数。  
它解决的是浮点数**精度误差**带来的“看起来不相等，但其实应该算相等”的问题。

---

### 1️⃣ 基本语法
```python
from math import isclose
isclose(a, b, *, rel_tol=1e-09, abs_tol=0.0) -> bool
```
- `a, b`：要比较的两个数（`float`、`int`、`Decimal` 等）。  
- `rel_tol`：相对容差（relative tolerance）。  
- `abs_tol`：绝对容差（absolute tolerance）。  

---

### 2️⃣ 判断规则
返回 `True` 当且仅当：
```
|a - b| ≤ max(rel_tol * max(|a|, |b|), abs_tol)
```
即：  
- 如果两数本身**很大**，就按**相对误差**算；  
- 如果两数**接近 0**，就按**绝对误差**兜底，避免因 `rel_tol * 0 = 0` 而永远 `False`。

---

### 3️⃣ 你给的用法
```python
isclose(prediction_val, reference_val, abs_tol=1e-3)
```
- **只指定了 `abs_tol=1e-3`**，`rel_tol` 取默认值 `1e-9`。  
- 含义：  
  - 当两个数都很大时，用默认的 **1e-9 相对误差**；  
  - 当两个数都很小时（或其中一个为 0），只要差的绝对值 ≤ **0.001** 就算相等。

---

### 4️⃣ 示例
| prediction_val | reference_val | 结果 | 原因 |
|----------------|---------------|------|------|
| 3.14159        | 3.14160       | True | 差 1e-5 < 1e-3（绝对容差） |
| 1.0            | 1.001         | True | 差 1e-3 ≤ 1e-3 |
| 1e-6           | 0.0           | True | 差 1e-6 ≤ 1e-3（绝对容差兜底） |
| 1e6            | 1e6 + 1       | True | 相对误差 1e-6 < 1e-9 |
| 1.0            | 1.01          | False| 差 0.01 > 1e-3 |

---

### ✅ 一句话
`isclose(..., abs_tol=1e-3)` 就是在说：  
> **只要两数差的绝对值 ≤ 0.001，或者相对误差 ≤ 1e-9，就算它们相等。**


