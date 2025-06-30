# Introduction

**Cyclic redundancy check (CRC)** is an error-detecting code used to detect accidental changes to raw data in digital
networks and storage devices.

A CRC code is a systematic cyclic code.
A cyclic code is a linear block code where any cyclic shift of a valid codeword results in another valid codeword.
A systematic code is an error-detecting or error-correcting code in which the input data are embedded in the encoded output.

CRC works by applying a CRC computation with the raw data and
appending the fixed-length CRC value to the raw data to form a codeword.
When the codeword is being verified, the same CRC computation is applied,
and if the CRC values match, the raw data is considered intact.

The CRC computation requires a **generator polynomial**.
The polynomial is the divisor in a polynomial long division,
which takes the raw data as the dividend and in which the quotient is discarded and the remainder is the CRC value.

In practice, commonly used CRC employs the finite filed of two elements, GF(2), which are 0 and 1.
A CRC is called n-bit CRC when the CRC value length is $n$.
The highest degree of the generator polynomial is $n$, which means it has $n+1$ items.

The CRC performance for error-detecting could be measured with Hamming distance to a specified data bit length.


# Computation

For CRC consisting of 0 and 1,
the CRC computation is a modulo-2 division with the raw data appended n-bit 0 as dividend and the generator polynomial as divisor.

For example, the generator polynomial of a 5-bit CRC is
$x^5 + x^4+ x^3 + x^2 + 1$,
which equals to
$1 \cdot x^5 + 1 \cdot x^4 + 1 \cdot x^3 + 1 \cdot x^2 + 0 \cdot x^1 + 1$,
represents the binary divisor $\text{6'b11\_1101}$.

When the 16-bit raw data is $\text{0x9AC7}$ in hexadecimal, which represents binary dividend $\text{16'b1001\_1010\_1100\_0111}$,
the modulo-2 division is computed as following:

```
1001101011000111 00000
111101
----------------------
0110111011000111 00000
 111101
----------------------
0001010011000111 00000
   111101
----------------------
0000101001000111 00000
    111101
----------------------
0000010100000111 00000
     111101
----------------------
0000001010100111 00000
      111101
----------------------
0000000101110111 00000
       111101
----------------------
0000000010011111 00000
        111101
----------------------
0000000001101011 00000
         111101
----------------------
0000000000010001 00000
           11110 1
----------------------
0000000000001111 10000
            1111 01
----------------------
0000000000000000 11000
```

The remainder is $\text{5'b1\_1000}$ in binary, which is the CRC computation result.


# Specification

Most polynomial specifications either drop the MSb or LSb, since they are always 1.
There are types of integer polynomial representations for the same generator polynomial $x^5 + x^2 + 1$, including:

|Representation Type|Polynomial Expansion                                                       |Integer Representation |
|:---               |:---                                                                       |---:                   |
|normal             |$1 \cdot x^5 + [0 \cdot x^4 + 0 \cdot x^3 + 1 \cdot x^2 + 0 \cdot x^1 + 1]$|$\text{0x05}$          |
|reversed normal    |$[1 + 0 \cdot x^1 + 1 \cdot x^2 + 0 \cdot x^3 + 0 \cdot x^4] + 1 \cdot x^5$|$\text{0x14}$          |
|reciprocal         |$1 + [0 \cdot x^1 + 1 \cdot x^2 + 0 \cdot x^3 + 0 \cdot x^4 + 1 \cdot x^5]$|$\text{0x09}$          |
|reciprocal reversed|$[1 \cdot x^5 + 0 \cdot x^4 + 0 \cdot x^3 + 1 \cdot x^2 + 0 \cdot x^1] + 1$|$\text{0x12}$          |

Integer representations with the MSb or the LSb is also available.


# Hardware Implementation

Modulo-2 division could be implemented with shift register and exclusive-OR.

Codes for the 1-bit data input CRC generation with $x^5 + x^2 + 1$ as generator polynomial are as following.

```verilog
function automatic [4:0] crc;
    input [4:0] crcIn;
    input [0:0] data;
begin
    crc[0] = crcIn[4] ^ data[0];
    crc[1] = crcIn[0];
    crc[2] = crcIn[1] ^ crcIn[4] ^ data[0];
    crc[3] = crcIn[2];
    crc[4] = crcIn[3];
end
endfunction
```

The implementation is left shifting and MSB first.
The `crcIn` is the previous CRC remainder or the initial value.

There are also multi-bit implementation for acceleration.

If the CRC calculation is data LSB first,
then it is implemented with **right shifting** and is called **"LSB"** or **"little endian style"**.
On the other hand, for data MSB first,
the CRC implementation is based on **left shifting** and is called **"MSB"** or **"big endian style"**.


# Application

To choose appropriate CRC length and generator polynomial, visit [Best CRCs](https://users.ece.cmu.edu/~koopman/crc/).

To generate HDL codes for CRC generation, visit [Generator for CRC HDL Code](https://bues.ch/cms/hacking/crcgen).


