Generic RISC-V SoC platform
-----------------------------------------------

### 在RV32系统中，如何设定64位的时间比较寄存器timecmp

无论是在32位还是64位系统中，时间比较寄存器timecmp都是64位的，以保证其不会溢出。
出于对多核多电压以及电源关断的支持，时间比较寄存器不再是一个CSR，而是一个映射到IO地址空间上的系统寄存器。
当IO总线宽度小于64比特，比如32比特时，如何正确设定时间比较寄存器呢？
RISC-V priv spec如是说：

> "In RV32, memory-mapped writes to mtimecmp modify only one 32-bit part of the register."
>
> Figure 3.15 below shows a 'Sample code for setting the 64-bit time comparand in RV32 assuming the registers live in a strongly ordered I/O region.'
> 
> ~~~
>  	# New comparand is in a1:a0.
>         li t0, -1
>         sw t0, mtimecmp   # No smaller than old value.
>         sw a1, mtimecmp+4 # No smaller than new value.
>         sw a0, mtimecmp   # New value.
> ~~~

相关讨论：[https://goo.gl/pPjwom](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/dVQVKx7f8eE/KsHJAw01CAAJ)

### Explanation about the design intention of SIP/UIP

xTIP does require going through M-mode, 
because the design uses a single timer to reduce hardware cost, rather 
than having one per privilege mode.  All conceptual timers are 
multiplexed in software on the lone M-mode timer. 

xEIP does not require going through M-mode; in fact, it's read-only in 
M-mode, too.  It is cleared by interacting with the interrupt 
controller via MMIO.

Writable-SEIP is only writable from M-mode.  The reason is that 
the feature is intended for emulation/virtualization of the PLIC for 
S-mode by M-mode, so the bit should be cleared by interacting with the 
PLIC (with a trap into M-mode).  It would be a virtualization hole to 
allow S-mode to write it. 

[[isa-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/yO425WGpvhA/vvt7ANd9AgAJ)]

### Context switch with non-standard system state extension

When some platform has non-standard state extension, such as extra state CSRs, which needs to be pushed to stack during context switching,
some extra SBI functions should be defined in the spec for storing these states.

[[isa-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/xiFzYJEaw48/biYm9W3DBwAJ)]

### Update of `A` and `D` bit in page table entry

`A` and `D` bits in the page table entry are used to simplify the process of swapping pages from memory to secondary storage.
However, the update of `A` (when accessed) and `D` (when written to) is not hardware managed to my surprise.
So for the first load (setting `A`) or store (setting `D`), a page fault is normally raised and the page table entry is found through a page table walk.

[[hw-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/hw-dev/edqKVHAy0lQ/f_SBDsyFBAAJ)]

### ecall和ebreak的返回地址

RISC-V在中断和异常时的返回地址定义是不同的。出现中断时，中断返回地址epc被指向下一跳指令，因为中断时的指令被正确执行。
出现异常时，epc则指向当前指令，因为当前指令触发了异常。
然而，如果异常由ecall或ebreak产生，直接跳回返回地址则会造成死循环（执行ecall导致重新进入异常）。
正确的做法是时在异常处理中手动改变epc指向下一条指令。现在ecall/ebreak都是4字节指令，因此简单设定`epc=epc+4`即可。
该问题很早以前就被提出过，最近又被重新提及。

相关讨论：[https://goo.gl/6CcZ8j](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/Mn_qEm_OTOI/PayhGy6gAQAJ)


### The order of interrupts

Multiple simultaneous interrupts destined for different privilege 
modes are handled in decreasing order of destined privilege mode. 
Multiple simultaneous interrupts destined for the same privilege mode 
are handled in the following decreasing priority order: MEI, MSI, MTI, SEI, SSI, STI, UEI, USI, UTI.
Synchronous exceptions are of lower 
priority than all interrupts. 

[[isa-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/_SECLWl8qWk/WCmJHI-_CgAJ)]

### Security loophole: mapping machine memory to kernel page

Reported a security loophole before the enforcement of physical memory protection (PMP):
Kernel is allowed to map machine mode physical memory to its virtual memory space which then allow privilege up-lift.

[[Lab Mouse Security](http://blog.securitymouse.com/2017/04/the-risc-v-files-supervisor-machine.html)]
[[cnrv](https://github.com/cnrv/home/blob/master/bi-week-rpts/2017-07-20.md#安全点评)]
