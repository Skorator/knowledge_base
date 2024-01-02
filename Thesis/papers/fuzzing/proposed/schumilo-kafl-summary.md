## Challenges of kernel fuzzing: 
- Crashes and freezes mandate the usage of virtualisation to allow 
- Way more non-determinism in kernel code due to: 
	- interrupts
	- kernel threads
	- statefulness
- No "simple" way of interacting with the kernel drivers such as stdin or command line arguments
- Relevant drivers are often closed source (such as NVIDIA drivers)

## Proposition: 
New approach to perform feedback based fuzzing using Intels Process Tracing Techniques 

## Contributions: 
- OS independent fuzzing by harnessing the VMM to produce coverage
- Extensible modular design --> Fuzzer, tracing engine and target are separated
- [kernelAFL]( https://github.com/RUB-SysSec/kAFL)

## Technical Background
### Memory Layout: 
- Kernel Space Memory
- User Space Memory 

## Intels Hardware Virtualisation
### Differentiate between three types of CPUs: 
- Physical (implemented in Hardware)
- Logical (Share logical CPU - only one can run at a time)
- Virtual (It is possible to create multiple hardware-supported virtual machines (VMs) on a single logical CPU. In this case, each VM has a set of its own vCPUs)

- We further differentiate the VMM (Virutal Machine Monitor) and the Virutal Machine (VM). 
- Running on the guest OS, multiple actions or reasons will trigger a VM-Exit event and transfer control to the hypervisor
- Creating and Launching of the VMs is done by using so called Virtual Machine Control Structures (VMCS)

## Intel PT (Processor Tracing) Feature+

There are five types of control flow altering instructions (three are relevant here): 
1. **Taken-Not-Taken (TNT)**: On execution of a conditional jump, decision whether jump was (not) taken is TNT-packet
2. **Target-IP (TIP)**: If indirect jump or transfer instruction is executed, control flow can not be recovered. 
	1. Upon execution of: *indirect branch, near , ret, fa, transfer* the processor produces a TIP (stores the target instruction pointer executed by processor after the jump occurred
3. **Flow Update Packets (FUP)**: Interrupts or Traps are recorded as FUPs and usually followed by a TIP packet to indicate the following instruction

**The Intel IP incorporates Filtering possibilities**. 
- Selecting the Kernel Privilege Level **>0** or **=0**
- Setting **=0** kAFL can reduce the tracing to only the kernel mode execution


## System Design

There are three main components: 
- Fuzzing Logic (Ring 3)
- VM infrastructure (Ring 0 and Ring 3)
- User mode agent (Ring 3)

### The VM Infrastructure
- Consists of a ring 0 (EL3) **KVM-PT** component and a ring 3 **QEMU-PT** (EL0) component
- Generally, the guest-host only communicates to the host using hypercalls.
	- The host can then write guest-memory and continues the guest execution once the request has been handeled 

#### Outline of a fuzz run
#### Loader Setup
	1. VM is started the user mode agent uses hypercall to submit the address of the Kernel Panic handler to QEMU-PT
	2. QEMU PT Patches hypercall handler to panic handler address --> fast reaction in case of crash
	3. Loader uses another hypercall to set up user mode agent
----- 
##### Fuzzer Setup
	1. Agent triggers HC_SUBMIT_CR2 hypercall which is handled by KVM-PT
	2. Hypervisor extracts CR3 value and hands it to QEMU-PT for filtering
	3. Hypervisor uses HC_SUBMIT_BUFFER to inform the host where to put input
----- 
##### Fuzzer Loop
	1. Agent requests new data using HC_GET_INPUT
	2. Fuzzing Logic creates new input and sends it to QEMU-PT (has full access to guest memory)
	3. QEMU-PT copys memory into buffer specified by agent
	4. VM-Entry --> continue executing VM, enabling PT Mechanism
	5. Agent consumes input and interacts with Kernel (interpreting the input as a filesystem, trying to mount it)
	6. When Kernel interaction is done, VM-Exit and disable PT Mechanism
	7. QEMU-PT decodes the resulting tracing data and stores it into Bitmap (AFL Format)
----- 

![[schumilo-kafl-howitworks.png]]