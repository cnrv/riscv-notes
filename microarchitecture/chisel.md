Chisel
----------------------------

### Remove log2Up and log2Down from API
Log2Up is to be removed because Log2Up != Log2Ceil where the later is math operation.

For the rare case where log2Up is used to get a minimal width to store 0, a workaround is:

    (log2Ceil(x) max 1)

[[chisel3](https://github.com/freechipsproject/chisel3/pull/528)]