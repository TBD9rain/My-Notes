# General Division

## Non-restoring Division

Suppose that:
- $D$: dividend, whose bit width is $n$.\
    From left to right, bits are $d_{n - 1}$ to $d_0$, respectively.
- $S$: divisor, whose bit width is $m$.\
    From left to right, bits are $s_{m - 1}$ to $s_0$, respectively.
- $Q$: quotient, whose bit width is $n$.\
    From left to right, bits are $q_{n - 1}$ to $q_0$, respectively.
- $R$: remainder, whose bit width is $m$.\
    From left to right, bits are $r_{m - 1}$ to $r_0$, respectively.

*Note: $S=0$ is out of consideration, extra handling is necessary.*

These variables satisfy:
$$
D = Q \cdot S + R
$$

**This equation is even even if $Q$ is too big and $R$ is negative.**


### Unsigned Inputs

Non-restoring division is implemented as loop subtraction or addition.
Let $A_i$ the remainder result of the loop $i \in [1, 2, ..., n]$, whose bit width is $n + m$.
The $n + m$ bit of $A_i$, $a_{i, n + m - 1}$, is the sign bit.

At first, calculate
$$
A_1 = D - (S \ll (n - 1))
$$

If $A_1 \ge 0$, or $a_{1, n + m - 1} = 0$,
$1$ should be assigned to $q_{n - 1}$.
Then calculate
$$
\begin{aligned}
A_2 &= A_1 - (S \ll (n - 2)) \\
&= D - (S \ll (n - 1)) - (S \ll (n - 2))
\end{aligned}
$$

**The $- (S \ll (n - 1))$ is reserved, and $- (S \ll (n - 2))$ is tried.**

If $A_1 < 0$, or $a_{1, n + m - 1} = 1$,
$0$ should be assigned to $q_{n - 1}$.
Then calculate

$$
\begin{aligned}
A_2 &= A_1 + (S \ll (n - 2)) \\
&= D - (S \ll (n - 1)) + (S \ll (n - 2)) \\
&= D - (S \ll (n - 2))
\end{aligned}
$$

**The $- (S \ll (n - 1))$ is compensated, and $- (S \ll (n - 2))$ is tried.**

After loop like above until $i = n$,
the $q_0$ is obtained, and the whole $Q$ is determined.
For $R$, it's directly truncated from $A_n$.
It is important to note that the $A_n$ might be negative and need compensate by adding $S$.


#### Digital Circuit Implementation Notes

The $A_i \pm (S \ll (n - i))$ only affects the $m + n - i + 1$ to $n - i + 1$ bits.\
And $n - i$ to $1$ bits of $A_i$ are identical to $n - i$ to $1$ bits from $D$.\
Therefore, the bit width of the register for $A_i$ variable could be $m + 1$, with the highest bit as sign bit.

Assign $0$ to $A_0$ as initial value.
For each loop of $n$ loops,
the highest bit of $D$ is left shifted into $A_{i - 1}$ register,
**instead of right shifting $S$**.

After calculating $A_i = A_{i - 1} \pm (S \ll (n - i))$,
$A_i$ overwrites the $A_{i - 1}$ register.
Meanwhile,
the $q_{i}$ could be left shifted into register for $D$ to minimize register resource requirement.

At last,
compensate the $R$ value based on the sign of $A_n$, or $a_{n, m + 1}$.


### Signed Inputs

Signs are processed before division calculation,
and make the signed division converted to unsigned division.
After unsigned division,
the signs should be restored.

For signed inputs,
the quotient sign is determined by the signs of both dividend and divisor.
The sign of the remainder could be selective.
The usual choice is to align the remainder sign with the dividend.


#### Digital Circuit Implementation Notes

When $D = -2^{n - 1}$ and $S = -1$, mathematically $Q = 2^{n - 1}$.
However the maximum positive value of signed quotient is $2^{n - 1} - 1$, when bit width is $n$. 
Therefore, extra handling for this situation is necessary.


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


