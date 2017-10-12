RiSC-V ISA
------------------------------------

### RISC-V的Formal Specification工作组

这个小组正在制定 RISC-V 的 Formal specification。目前已经有五个组织，尝试着制定和实现。使用的语言包括L3、Verilog HDL、Haskell以及BSV。

目前 Rishiyur所带的小组正要整合这几个 specification 和实现。

有兴趣的朋友可以加入这小组参与讨论，或参考这些repo：

1. Prashant Mundkur (SRI); written in L3. [Repo](https://github.com/pmundkur/l3riscv).
2. Clifford Wolf (individual): written in Verilog. [Repo](https://github.com/cliffordwolf/riscv-formal) and [slide](http://www.clifford.at/papers/2017/riscv-formal/slides.pdf).
3. Adam Chlipala and group (MIT): written in Haskell.
4. Rishiyur Nikhil (Bluespec, Inc.): written in BSV. [Repo](https://github.com/rsnikhil/RISCV_ISA_Formal_Spec_in_BSV).
5. Team of Peter Sewell in Cambridge : written in SAIL. [Repo](https://bitbucket.org/Peter_Sewell/sail/src/07fad742df72ff6e7bfb948c1c353a2cf12f5e28/risc-v/riscv.sail?fileviewer=file-view-default).

Link: [https://goo.gl/iCUq1A](https://groups.google.com/a/groups.riscv.org/forum/?utm_medium=email&utm_source=footer#!topic/isa-dev/DxKrkE6_LOM)

### LL/SC vs CAS
Both load-lock/store-check(LL/SC) and compare-and-swap(CAS) can be used for cirticial section.
LL/SC is chosen due to the [ABA](https://en.wikipedia.org/wiki/ABA_problem) issue.

It is dangerous for simple processors to break AMO (atomic operations, including CAS) into two operations.
"Splitting any operation into micro-ops can fail part-way through." Then it is no longer atomic.

[[riscv-isa-manual](https://github.com/riscv/riscv-isa-manual/issues/93)]
[[paper](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.140.8385)]
[[hw-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/hw-dev/siW5CT4V5bY/284nC0_pBAAJ)]

### 在LR/SC之间应禁止所有中断

LR/SC是RISC-V支持复杂原子操作的方式。
LR指令读一个地址并对该地址上锁。
上锁后，处理器有16个周期完成一段小的原子操作片段。
片段完成后，SC指令尝试写原上锁地址，如果锁没有被破坏，则写操作成功。否则，原子操作片段重新执行。
任何对锁的直接操作，或者执行时间超过16个周期都会导致锁丢失。
同时，在LR/SC之间也禁止任何中断服务。这里不是说主动禁止中断服务，而是任何中断都会导致锁丢失。
这就引发了关于两个问题的确认：

- 即使是deligated 用户级别中断服务也会导致锁丢失。
- 这便导致原子操作片段中不能使用需要trap-and-emulate的指令，比如乘除法指令。实际上，spec规定无论硬件实现与否，都不要使用乘除法指令。

#### 相关讨论 [https://goo.gl/UAciir](https://groups.google.com/a/groups.riscv.org/d/msg/isa-dev/FKWUf92UYsg/qDWvkhY_AAAJ)

### Proposal to add explicit cache control instructions
- Need to explicit cache control instructions to fence, prefetch, pin, flush and optimise.
- Need to be side-effect free for microarchitects that do not support.
- Reuse the op space of fence.
- Range support. What happens if interrupts in range operation.
- Differenciate I$ and D$.
- Recognise the existance of hierarchical cache.
- Whether to assume inclusive by default?
- Destructive operations.

[[isa-dev 1](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/EYAG7yQRnaQ/sia-7H6WAQAJ)]
[[isa-dev 2](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/Xa1y68PxjAU/MB2rLM1zAAAJ)]
[[isa-dev 3](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/Xa1y68PxjAU/WlbR93D0AAAJ)]
[[isa-dev 4](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/eKkGAN2-jss/4uRoQi2TBAAJ)]
[[cnrv 1](https://github.com/cnrv/home/blob/master/bi-week-rpts/2017-07-20.md#explicit-cache-instruction-重启讨论)]
[[cnrv 2](https://github.com/cnrv/home/blob/master/bi-week-rpts/2017-08-03.md#直接缓存操作explicit-cache-control指令第3版-第4版)]

### Proposal to add `MUX` in RISC-V B
- possibility to use micro-op fusion instead
- simpler code if allow side-effect
- replace with `CMOV` in normal OOO processor
- concern for constant execution time for security (side-channel attack)

[[isa-dev 1](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/TWo_avwkMkU/zQ48SwjrBAAJ)]
[[isa-dev 2](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/TWo_avwkMkU/FQoGpN7uBAAJ)]
[[isa-dev 3](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/TWo_avwkMkU/86YLAa4CBQAJ)]
[[isa-dev 4](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/TWo_avwkMkU/YJSlP4kEBQAJ)]
[[isa-dev 5](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/TWo_avwkMkU/Am8HI8mzBQAJ)]
[[cnrv](https://github.com/cnrv/home/blob/master/bi-week-rpts/2017-07-20.md#提议向risc-v-b扩展指令集bit操作扩展添加选择mux指令)]


### Fused AUIPC+JALR for direct jump
- The concept and requirement for micro-op fussion.
- Use RVC with micro-op for complex single cycle operations.
- The use of source/destination registers for return address stack (RAS) calibration.
- Alternative link register x5 for milli-code

[[isa-dev 1](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/uZUTszCtgAA/ioz2o3iJCgAJ)]
[[isa-dev 2](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/uZUTszCtgAA/wZkm4MmOCgAJ)]
[[isa-dev 3](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/uZUTszCtgAA/f3jDhbjEAAAJ)]
[[cnrv](https://github.com/cnrv/home/blob/master/bi-week-rpts/2017-07-20.md#为什么要定义x5为可选链接寄存器alternative-link-register)]
[[stackoverflow](https://stackoverflow.com/questions/44556354/jal-what-is-the-alternate-link-register-x5-for)]

### RoCC example assembly
C/Assembly macros for talking with Rocket Custom Coprocessors (RoCCs)

[[rocc-software](https://github.com/IBM/rocc-software)]
