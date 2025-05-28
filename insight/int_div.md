# General Division


# Division by Invariant Integers

Variant integer dividend and invariant integer divisor can be replaced by **multiplication and bit-shift**,
which could reduce the resource requirement in digital circuits.

For the complete theory, refer to [Division by Invariant Integers using Multiplication](https://dl.acm.org/doi/10.1145/178243.178249).

Suppose $m$, $d$, $l$ are non-negative integers, while $d \ne 0$ and:

$$
2^{N+l} \le m \times d \le 2^{N+l} + 2^l
$$

then for unsigned integer dividend $n$ and divisor $d$
with $0 \le n, d \le 2^{N}-1$ there is:

$$
\lfloor \frac n d \rfloor =
\lfloor \frac {m \times n} {2^{N+l}} \rfloor
$$

Python codes to calculate $m$ and $l$:

```python
import math as m

# max bit width of dividend and divisor
n = 16
# divisor
d = 255

l = m.ceil(m.log2(d))

low = m.floor(m.pow(2, n + l) / d)
high = m.floor((m.pow(2, n + l) + m.pow(2, l)) / d)
shiftN = l

while (m.floor(low / 2) < m.floor(high / 2) and shiftN > 0):
    low = m.floor(low / 2)
    high = m.floor(high / 2)
    shiftN = shiftN - 1

print(f"m = {low}")
print(f"shift number = {shiftN}")
```

**The signed divisions with quotient rounded towards 0 or rounded towards $-\infty$ are also described in the article.**


