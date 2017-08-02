Generic RISC-V SoC platform
-----------------------------------------------

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
