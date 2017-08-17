RISC-V toolchain
-------------------------------------------

### Enable unaligned memory for small code size

Unaligned memory access is definite evil and should be avoid by all means.
However, it is really desprate, unaligned memory access can be supported by traps.
To enable unalign memory acess:

> That only happens if you do -Os -mtune=size. If you just do -Os it shouldn't do misaligned accesses.

[[riscv-gcc PR #88](https://github.com/riscv/riscv-gcc/pull/88)]

### multilib support

Multilib is a compiler utility that allow multiple crosscompiler (for different ISA/ABI to coexist).
The current RISCV GCC already supports multilib (when configure `--enable-multilib`).

[[sw-dev 1](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/nkiTe3stA2o/hT2ebjBmCQAJ)]
[[sw-dev 2](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/nkiTe3stA2o/Ntb5x6y8AAAJ)]
[[sw-dev 3](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/fO_Xv_kFfRI/I2A0eyzlCQAJ)]
[[sw-dev 4](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/fO_Xv_kFfRI/AFnDQtAYBgAJ)]


### what is bool

Any compiler generated bool should be 0x1.

[[isa-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/IutUMddVTzA/xjp7Qqb4BAAJ)]

### gp register for long jump optimization at linking time

[[sw-dev 1](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/60IdaZj27dY/5MydPLnHAQAJ)]
[[sw-dev 2](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/60IdaZj27dY/5IqqFBL0AQAJ)]
[[mcu-eclipse](https://gnu-mcu-eclipse.github.io/arch/riscv/programmer/#the-gp-global-pointer-register)]

### Register clobbering for context switching

Use `-fcall-saved-t6` etc for registers to spare.

[[sw-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/e1A6WdmdHvg/7Q0Hf1ORBAAJ)]

### SBI ecall

Assembly segment of SBI ecall (subject to changes)

[[sw-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/exbrzM3GZDQ/edmBl2ezAAAJ)]
