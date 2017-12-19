RISC-V toolchain
-------------------------------------------

### GCC能够利用特定处理器的load/store延迟优化程序吗？
RISC-V的GCC已经支持mtune参数，在该参数中将load/store的代价设定为5([https://github.com/riscv/riscv-gcc/blob/riscv-gcc-7.2.0/gcc/config/riscv/riscv.c\#L299](https://github.com/riscv/riscv-gcc/blob/riscv-gcc-7.2.0/gcc/config/riscv/riscv.c#L299))，即增加内存指令相对ALU指令的惩罚比重。
不过根据一个GCC维护者的解释，mtune中的内存访问代价只被用于指令选取。
即当所有的逻辑寄存器用尽需要将数据暂存入栈(spill)，mtune的代价会被用于选择spill哪一个逻辑寄存器。
而利用内存访问延迟最有效的方法是在指令调度的时候使用。该方式需要提供一个具体的流水线描述文件。
现在RISC-V，或者具体地说，Rocket处理器还没有提供。

RISC-V sw-dev上的讨论: https://groups.google.com/a/groups.riscv.org/d/msg/sw-dev/E4FYldExpCU/fAcAK6I8BgAJ

### 在反汇编中使用原始机器指令和机器寄存器

使用objdump对可执行文件进行反汇编默认使用宏指令代替特殊指令（比如用`li a5, 3`代替`addi x15, x0, 3`）和使用ABI寄存器命名代替机器命名（用`a5`替代`x15`）。如果需要反汇编成最原始的机器代码和机器寄存器，可以使用` --disassembler-options=no-aliases,numeric`参数。

邮件列表中的讨论：[https://goo.gl/As1jec](https://groups.google.com/a/groups.riscv.org/d/msg/sw-dev/Z2MheQOf_Tc/3sQ6SQPxAgAJ)

### 使用编译参数在汇编代码中选择性地使用RVC压缩指令

在全局禁止使用RVC指令的时候，汇编代码可以通过在代码中局部地启用RVC编译参数来启用压缩指令。
在如下的例子中：

```asm
.option push;
.option rvc; 
    c.addi a0, 1;
    c.slli a0, 1;
.option pop
```

在`.option rvc;`和`.option pop`之间便可以使用RVC指令。而之外的部分，则会只能使用正常指令集。

Andrew Waterman举的例子：[https://goo.gl/iMoTy1](https://groups.google.com/a/groups.riscv.org/d/msg/sw-dev/ShgP4nXkKQE/39r5PyUfBQAJ)


### GCC规定函数栈默认对齐16字节

除了RV32E之外，无论是RV32还是RV64，函数栈都将默认对齐16子节(128比特)。
社区对此决定显然有不同意见，一边是嵌入式设计者主张使用更小的对齐大小，比如RV32使用8字节，
而另一边则是兼容性系统的设计者主张使用较大的对齐大小，以支持双精度甚至4倍精度或者以后可能遇到的扩展。

最后的讨论结果是，函数栈的默认对齐其实是ABI的一部分。ABI本身是为了函数库共享和二进制一致性而产生的。
这些功能其实都只在较兼容的系统上才有意义。对栈对齐大小敏感的嵌入式系统往往都使用静态编译或不遵守ABI，所以ABI的设计应该服从兼容性系统的要求。
那么，GCC的栈对齐大小定义为16字节是合理的。不过为了能让嵌入式系统使用自己的（非标准的）对齐大小，
GCC将增加`-mpreferred-stack-boundary`参数来自定义栈对齐。

具体讨论：
#### David Chisnell对于此问题较深刻的见解: [https://goo.gl/v4kbqL](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/SFcqfIrRhQc/ROcp8Vq8DQAJ)
#### riscv-elf-psabi-doc的issue \#21：[https://git.io/v565x](https://github.com/riscv/riscv-elf-psabi-doc/issues/21)
#### riscv-gcc的PR \#95：[https://git.io/v565h](https://github.com/riscv/riscv-gcc/pull/95)

### Alternative toolchain compilation script from MCU Eclipse

For what it's worth I (Tommy Murphy) would recommend Liviu's GNU MCU Eclipse scripted Docker approach to building the tools as it's much more structured, predictable and stable than building them manually using instructions that appear elsewhere which are often incomplete, confusing or simply incorrect/out of date.
His build process pulls specific tarballs of the riscv-gcc, riscv-binutils-gdb and binutils-newlib sources from his github repo but with minor modifications to the script you can pull your own (e.g. if you want to build the bleeding edge stuff as I have done in the past few days).

https://gnu-mcu-eclipse.github.io/toolchain/riscv/build-procedure/

### 现有GCC编译器支持的编译目标类型

Michael Clark 很好地总结了当前riscv-unknown-elf-gcc编译器支持的编译目标和相应的参数配置。

> #### RISC-V Newlib ELF Toolchain Link options
> 
> 1. Default: crt. libc, libgloss (binaries work with the riscv-pk and riscv-isa-sim, or in riscv-linux)
> 
> `	riscv-unknown-elf-gcc`
> 
> 2. Alternative default: crt, libc and libgcc (will fail to link unless libgloss POSIX system call stubs are implemented)
> 
> `	riscv-unknown-elf-gcc -nostdlib -lc -lgcc`
> 
> 3. crt, libc, libnosys and libgcc (will link but POSIX calls return -ENOSYS)
> 
> `	riscv-unknown-elf-gcc -nostdlib -lc -lnosys -lgcc`
> 
> 4. “Default bare metal” linkage (user supplies _start symbol, default text address 0x10000)
> 
> `	riscv-unknown-elf-gcc -nostdlib -nostartfiles`
> 
> 5. “Actual bare metal" linkage (user supplies _start symbol and a linker script)
> 
> `	riscv-unknown-elf-gcc -nostdlib -nostartfiles -Wl,-T,myprog-link.ld`

相关讨论: [https://goo.gl/gcrqcw](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/8szTggvdi48/MXLKXkkRAQAJ)

### GCC将主动忽略所有非标准的扩展指令

RISC-V指令集允许用户定义自己的非标准扩展指令集。
这些指令集可以被定义成参数用于GCC的`-march`选项，但是必须以`x`开头。
GCC会自动忽略所有以`x`开头的非标准扩展指令但是将参数传递给汇编器。
这样就可以支持用嵌入式汇编的方法通过修改汇编器来使用非标准扩展。

> The RISC-V ISA allows custom ISA extensions to be defined by users.
> These extensions must come after all the standard extensions, and must
> start with 'x'. This patch allows these custom extensions to be passed
> via the '-march' flag and ignores them -- we don't plan on supporting
> any custom extensions in GCC, so I think that's always the right thing
> to do. These extensions will be passed to the assembler, which is
> expected to have either been modified to support the extension or
> produce an error.

详情请见 riscv-gcc PR \#91: [https://git.io/v5umy](https://github.com/riscv/riscv-gcc/pull/91)


### What is (or where is) Linux’s asm-generic

A long time ago, every time Linux was ported to a new architecture, the architecture-specific code and headers were copy-and-pasted from another architecture, and then modified to fit the new architecture. This led to a maze of duplicated headers, all slightly different. Every change or bugfix to these headers and code had to be duplicated for every architecture in the kernel, taking care to respect the small differences.

To reduce the duplication, the asm-generic directory was created. It has that name because architecture-dependent headers on Linux are named asm/something.h, while architecture-independent headers are named linux/something.h (on an installed system, they can be found at /usr/include/asm and /usr/include/linux). The headers which had been duplicated several times were deduplicated and moved to that directory. 

[sw-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/8szTggvdi48/ou02OME-AAAJ)

### 到底riscv\*\*-unknown-elf是不是bare-metal的交叉编译器？

估计大多数人和我一样都觉得，难道不是吗？嗯，还真不一定是。
[GNU MCU Eclipse RISC-V Embdedded GCC](https://gnu-mcu-eclipse.github.io/)的作者Liviu Ionescu最近就提出了这么个问题，然后引起了激烈的讨论。
看起来，现在RISC-V提供的两个GCC工具链其实是这个样子的：

- **riscv\*\*-unknown-linux-gnu** 是针对Linux操作系统的编译器，会链接glibc，利用Linux系统调用和相应应用接口(ABI)完成底层工作。
- **riscv\*\*-unknown-elf** 是针对嵌入式系统的编译器，使用newlib的C库，不针对具体操作系统，利用libgloss定义的应用接口(ABI)完成底层操作。现在libgloss借用了Linux的ABI，所以使用riscv\*\*-unknown-elf编译的程序有可能也能正常运行于RISC-V Linux中。

如果某一个嵌入式平台需要实现自己的底层调用，现在的办法一般是替换底层的ABI实现，但保留由libgloss定义的接口。
如果希望进一步连ABI都彻底抛弃而直接调用硬件，现在只能通过附加的gcc参数来实现，比如`-nostdlib -lc` (不使用标准库但是用libc)。

为了提供针对最基础嵌入式平台的bare-metal环境，SiFive的Palmer Dabbelt现已着手提供一个新的gcc工具链，暂且命名为**riscv\*\*-unknown-none**。

相关讨论：
#### Liviu Ionescu的问题 [https://goo.gl/VMBSPe](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/m_VQUQV_qw0/PA-TCzwDAQAJ)
#### Palmer Dabbelt的回答 [https://goo.gl/bc8zSu](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/8szTggvdi48/q8kOlY1AAgAJ)
#### 新工具链 [https://goo.gl/nD9HJD](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/8szTggvdi48/q8kOlY1AAgAJ)

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
