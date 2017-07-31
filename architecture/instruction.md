RiSC-V ISA
------------------------------------

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


### rv8 (RISC-V simulator for x86-64)
rv8 is a RISC-V simulation suite comprising a high performance x86-64 binary translator, a user mode simulator, a full system emulator, an ELF binary analysis tool and ISA metadata:

- rv-jit - user mode x86-64 binary translator
- rv-sim - user mode system call proxy simulator
- rv-sys - full system emulator with soft MMU
- rv-bin - ELF disassembler and histogram tool
- rv-meta - code and documentation generator

[[rv8](https://rv8.io/)]

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
