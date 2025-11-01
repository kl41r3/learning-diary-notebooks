```python
from sympy import N, simplify
from sympy.parsing.latex import parse_latex
from sympy.parsing.sympy_parser import parse_expr
```

`sympy`: symbolic python computation

`N`: numerical. 
```python
print(N(sqrt(2)))  # Output: 1.4142135623730951
print(N(sqrt(2), 5))  # Output: 1.4142 (with 5 digits of precision)
```

`simplify`.
```python
expr = (sin(x)**2 + cos(x)**2)
print(simplify(expr))  # Output: 1
```

