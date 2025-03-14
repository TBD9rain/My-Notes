# Lattice

## Diamond

### Reveal

#### Unintended Caught Values

##### Parameter Bit-select Signal

In v3.11.1.441,
the **parameter involved bit-select variable** (excluding constant bit-select signal) for instantiation port assignments can not be correctly recognized and caught.
The unintended bits of the original variable might be used.

Solution:
- Create a wire type variable assigned the parameter involved bit-select variable.
- Use the created variable for instantiation port assignments.


