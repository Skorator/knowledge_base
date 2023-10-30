The idealised abstraction of physical memory of the device. It maps the actual storage in such a way that it appears to the process as if it was a contiguous section of storage. The Memory Management Unit (MMU) automatically translates the *virtual addresses* used by the processes, to the *physical addresses* in the computer. Virtual Memory can address more memory than there is in physical form. 

There are multiple components needed to perform Virtual Memory mapping. 

- Page table:
	- Page: The section of memory given to a process, appearing contiguous. Each process therefore has its own page table containing the mappings for its virtual memory to actual physical memory addresses
- Frame table:
	- Contains Addresses of actual physical memory blocks so called "frames". They exist in powers of 2 e.g. $2^0$ to $2^{12}$
- Transation Lookaside Buffer (TLB):
	- Cache for Operating Systems (OS) recently used mappings. 


### Virtual Address Resolution 
1. MMU checks TLB
	1. TLB Hit --> physical address returned 
2. TLB Miss: TLB miss handler looks up memory mapping in the page table 
	1. Mapping exists --> address written to TLB (necessary, since computer accesses memory through TLB)
3. Mapping does not exist: **PAGE FAULT**
	1. Scenario 1: There is no mapping e.g. the virtual address is invalid
	1. Scenario 2: The page is **moved out** e.g. move to secondary memory (mostly HDD or SSD)
		1. The page needs to be moved in. If the memory is full another page has to be moved out and the TLB updated to reflect the change.


![[virtual_address_resolution.png]]


Links: 
[[Basics]]
[[Virtual Memory]]
[[Operating Systems]]