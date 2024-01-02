### Kernel and Userspace
- Kernel space:
	- Restricted part of virtual memory only used for the operating system kernel, device drivers and kernel extensions
- User space (for Usermode programs): 
	- Memory area where application software stores its data
	- User mode describes all code and programs that run outside the systems kernel and interact with the kernel such as "I/O Programs", "File System Manipulation", "Application Software" etc.
	- Each process has its own memory space and usually can't see the memory of other processes (base of memory protection in modern OS)
	- There **CAN** be shared process memory to allow for inter process communication
	- 
![[user_and_kernel_space.png]]

Links: 
[[Basics]]
[[Kernel]]
[[Operating Systems]]
