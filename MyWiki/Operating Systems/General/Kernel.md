- The Kernel code is always persistent in memory. It facilitates the interaction between Software and Hardware components. Using device drivers, a full kernel controls **ALL** hardware resources. 
- Interaction with the kernel usually happens through  *systemcall*'s which are invoked by calling wrapper functions. 
- The critical code of the kernel is loaded into a separate part of memory called the "kernel space". Tasks such as running processes, managing hardware devices such as hard disks and handling interrupts are done there. 

### Architecture: 

There are different kinds of kernel architectures, some of which are:
#### Micro Kernels: 
- [TODO](https://en.wikipedia.org/wiki/Microkernel)

#### Hybrid Kernels: 
- [Hybrid Kernel](https://en.wikipedia.org/wiki/Hybrid_kernel)

#### Monolithic Kernels: 
- [TODO](https://en.wikipedia.org/wiki/Monolithic_kernel)
The Linux Kernel is a monolithic kernel. That means that the whole OS runs in one address space (kernel space) and the CPU works in a so called **supervisor mode**. 

Monolithic Kernels allow for dynamic (un-)loading of kernel modules during runtime. The advantage of having a "modular" Kernel is that unloaded modules do not need to be stored in RAM. This allows for a more efficient use of the often very sparse Random Access Memory (RAM) of the computer. 

##### Linux kernel interfaces: 
- [TODO](https://en.wikipedia.org/wiki/Linux_kernel_interfaces) 




Links: 
[[Kernel]]
