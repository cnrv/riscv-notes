Generic RISC-V SoC platform
-----------------------------------------------

### Context switch with non-standard system state extension

When some platform has non-standard state extension, such as extra state CSRs, which needs to be pushed to stack during context switching,
some extra SBI functions should be defined in the spec for storing these states.

[[isa-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/xiFzYJEaw48/biYm9W3DBwAJ)]

### Update of `A` and `D` bit in page table entry

`A` and `D` bits in the page table entry are used to simplify the process of swapping pages from memory to secondary storage.
However, the update of `A` (when accessed) and `D` (when written to) is not hardware managed to my surprise.
So for the first load (setting `A`) or store (setting `D`), a page fault is normally raised and the page table entry is found through a page table walk.

[[hw-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/hw-dev/edqKVHAy0lQ/f_SBDsyFBAAJ)]

### The order of interrupts

Multiple simultaneous interrupts destined for different privilege 
modes are handled in decreasing order of destined privilege mode. 
Multiple simultaneous interrupts destined for the same privilege mode 
are handled in the following decreasing priority order: MEI, SEI, UEI, 
MSI, SSI, USI, MTI, STI, UTI. Synchronous exceptions are of lower 
priority than all interrupts. 

[[isa-dev](https://groups.google.com/a/groups.riscv.org/forum/#!msg/isa-dev/_SECLWl8qWk/WCmJHI-_CgAJ)]

### Security loophole: mapping machine memory to kernel page

Reported a security loophole before the enforcement of physical memory protection (PMP):
Kernel is allowed to map machine mode physical memory to its virtual memory space which then allow privilege up-lift.

[[Lab Mouse Security](http://blog.securitymouse.com/2017/04/the-risc-v-files-supervisor-machine.html)]
[[cnrv](https://github.com/cnrv/home/blob/master/bi-week-rpts/2017-07-20.md#安全点评)]
