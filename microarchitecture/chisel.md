Chisel
----------------------------

### Rocket-Chip支持使用dontTouch属性保留端口

最近接个月，Chisel和FIRRTL的自动常数推断和信号去除功能不断完善，已经能较好地自动去掉电路中未被使用的信号和常量信号。
但是这导致了整体Rocket-Chip自动生成的一个问题。在顶部未被连接的端口会被自动去除，导致很多端口丢失。
现在我们终于可以使用Chisel的一个实验属性：dontTouch来确保需要保留的端口不被自动去除。

#### Rocket-Chip PR \#1033 [https://git.io/vd6vf](https://github.com/freechipsproject/rocket-chip/pull/1033)

### 告别Chisel cloneType 重载

cloneType是Chisel中关于类型复制的重定义函数。
所有需要放入列表型数据类型，比如Vec和Seq的Bundle类型对象，都需要对cloneType进行重载。
这基本是一个重复性工作，但是忘记则会编译出错。
现在，Chisel将能够自己生成对应cloneType函数，终于不再需要设计者手动重载了！

#### Chisel3 PR \#653 [https://git.io/vdVOD](https://github.com/freechipsproject/chisel3/pull/653)

### Bundles with structurally typed fields in compatibility mode fail in Scala 2.12

This is definitely a gotcha of Chisel.

Compiled with Scala 2.12, the following code:

    import Chisel._
    val myReset = true.B
    class ModuleExplicitReset(reset: Bool) extends Module(_reset = reset) {
      val io = new Bundle {
        val done = Bool(OUTPUT)
      }
      io.done := false.B
    }
produces

Error:(14, 10) value done is not a member of Chisel.Bundle
      io.done := false.B
Wrapping the io definition in IO() or extending chisel3.core.UserModule instead of chisel3.core.LegacyModuleeliminates the error. It seems to be a property of the LegacyModule definition.

[[Chisel3 issue 606](https://github.com/freechipsproject/chisel3/issues/606)]

### Unexplanable issue with using UIntToOH1

Clearly they do not understand what had happened !?  :-)

[[Rocket PR](https://github.com/freechipsproject/rocket-chip/pull/946)]

### Iterate IO bundle

Here is an issue about how to iterate an IO bundle and print out the names. Interesting to know.

[[Chisel3 issue](https://github.com/freechipsproject/chisel3/issues/662)]

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
