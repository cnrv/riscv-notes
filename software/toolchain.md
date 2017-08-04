RISC-V toolchain
-------------------------------------------

### gp register for long jump optimization at linking time

[[sw-dev 1](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/60IdaZj27dY/5MydPLnHAQAJ)]
[[sw-dev 2](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/60IdaZj27dY/5IqqFBL0AQAJ)]
[[mcu-eclipse](https://gnu-mcu-eclipse.github.io/arch/riscv/programmer/#the-gp-global-pointer-register)]

### Register clobbering for context switching

Use `-fcall-saved-t6` etc for registers to spare.

[[sw-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/e1A6WdmdHvg/7Q0Hf1ORBAAJ)]


### Possible ABI combinations

A list of multilibs that a build of the tools supports (when configure `--enable-multilib`).

[[sw-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/nkiTe3stA2o/Ntb5x6y8AAAJ)]

### SBI ecall

Assembly segment of SBI ecall (subject to changes)

[[sw-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/exbrzM3GZDQ/edmBl2ezAAAJ)]
