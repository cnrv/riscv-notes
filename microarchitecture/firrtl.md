FIRRTL
----------------------

### Constant propagation across module boundaries

FIRRTL will be able to check constant ports and propagate the const across module boundaries.

Here is an interesting remark about Rocket `hartid`:

> Because deduplication is critical for hierarchical place and route.
> We can discuss changing the API for when deduplication does or does not occur
> but it seems prudent for this PR to preserve the status quo
> that is relied upon by users (in rocket-chip for example, see `hartid`
> which is an input to preserve deduplication)

[[firrtl](https://github.com/freechipsproject/firrtl/pull/633)]
