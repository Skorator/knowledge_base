
# Action-guided kernel fuzzing

## Short summary
Instead of viewing the code as the execution of a series of instructions, ACTOR looks at series of **actions**. These actions are mined before fuzzing a target and describe higher level operations such as *memory allocation/deallocation* or *memory reads/writes*. By identifying semantic connections between syscalls and higher level actions, darts are created that store triplets of **(syscall, action, strack_trace )**. Using these dart structures, ACTOR is able to later synthesise fuzzer programs specifically crafted to trigger selected types of bugs such as UAF, DF, OOB or others. Its own DSL, allows specialist to easily create bug templates by specifying the action patterns of bugs. An example for this could be *alloc, free, use* for a **UAF**. The programs are then created by a synthesis engine which receives the bug templates and the previously created dart groups.

All this is implemented on TOP of Syzkaller. The semantic labelling of the darts is conducted using LLVM's static analysis capabilites. Action recording is performed with a modified version of KASAN. 

[[Kernel]]
[[Fuzzing]]
[[KASAN]]
