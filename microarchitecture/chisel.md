Chisel
----------------------------

### Iterate IO bundle

Here is an issue about how to iterate an IO bundle and print out the names. Interesting to know.

[[Chisel3 PR](https://github.com/freechipsproject/chisel3/issues/662)]

### Chisel mega refactor
- Input(...) and Output(...) now (effectively) recursively override their elements' directions
- Nodes given userDirection (Input, Output, Flip - what the user assigned to that node) and actualDirection (Input, Output, None, but also Bidirectional and BidirectionalFlip for mostly Aggregates), because of the above (since a higher-level Input(...) can override the locally specified user direction).
- DataMirror (node reflection APIs) added to chisel3.experimental. This provides ways to query the user given direction of a node as well as the actual direction.
- checkSynthesizable replaced with requireIsHardware and requireIsChiselType and made available in chisel3.experimental.

[[chisel3 PR](https://github.com/freechipsproject/chisel3/pull/617)]
[[chisel3 issue](https://github.com/freechipsproject/chisel3/issues/578)]

### Remove log2Up and log2Down from API
Log2Up is to be removed because Log2Up != Log2Ceil where the later is math operation.

For the rare case where log2Up is used to get a minimal width to store 0, a workaround is:

    (log2Ceil(x) max 1)

[[chisel3](https://github.com/freechipsproject/chisel3/pull/528)]
