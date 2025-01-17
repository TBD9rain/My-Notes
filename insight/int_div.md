# General Division


# Division by Invariant Integers

Variant integer dividend and invariant integer divisor can be replaced by **multiplication and bit-shift**,
which could reduce the resource requirement in digital circuits.

For the complete theory, visit [Division by Invariant Integers using Multiplication](https://dl.acm.org/doi/10.1145/178243.178249).


## Unsigned Division

Suppose $m$, $d$, $l$ are nonnegative integers, while $d \ne 0$ and:

$$
2^{N+l} \le m \times d \le 2^{N+l} + 2^l
$$

then for unsigned integer dividend $n$ and divisor $d$ 
with $0 \le n, d \le 2^{N}-1$ there is:

$$
\lfloor \frac n d \rfloor =
\lfloor \frac {m \times n} {2^{N+l}} \rfloor
$$

Matlab codes to calculate $m$ and $l$:

```matlab
% max bit width of divident and divisor
N = 32;
% divisor
d = 17;

l = ceil(log2(d));

low     = floor(2^(N + l) / d);
high    = floor((2^(N + l) + 2^l) / d);
shift_n = l;

while (floor(low / 2) < floor(high / 2) && shift_n > 0)
    low = floor(low / 2);
    high = floor(high / 2);
    shift_n = shift_n - 1;
end

m = high;
l = shift_n;
```


## Signed Division, quotient rounded towards 0


## Signed Division, quotient rounded towards $-\infty$


