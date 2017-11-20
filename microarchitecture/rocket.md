Rocket Core/SoC
-----------------------------------

### 控制Rocket处理器中定点除法器的延时

Rocket处理器中那个地定点除法器被刻意地配置为最慢模式，即64比特除法需要64周期完成。
该配置地原因有两个，一个是并行除法器会影响处理器的时序，导致处理器的最高频率下降。
另外一个是除法器基本在所有的处理器中都执行较慢，所以会被编译器和程序开发者主动避免。优化除法器的性能提升不大。
如果需要配置并行除法器，减少除法器延时，可改变除法器的`divUnroll`参数。

GitHub上的讨论：[https://git.io/vFQCF](https://github.com/freechipsproject/rocket-chip/issues/1114)


### Diplomacy information
TODO: write a blog post explaining the diplomacy package.

[[Rocket-chip Issue 880](https://github.com/freechipsproject/rocket-chip/issues/880)]

### Enable building maskrom and bootrom code from src

[[Rocket-Chip PR 951](https://github.com/freechipsproject/rocket-chip/pull/951)]

### RoCC interface

[[BSG](https://bitbucket.org/taylor-bsg/bsg_riscv)]

