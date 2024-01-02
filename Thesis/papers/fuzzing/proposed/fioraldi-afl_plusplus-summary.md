# AFL++

This paper presents AFL++, a community-driven state-of-the-art open-source tool for fuzzing research. It offers: 

- Custom mutator API 


## Contributions: 
- AFL++ propsed, which incorporates recent research into one tool 
- Custom Mutator API allowing for approachable way of incorporating future research into AFL++
- [AFL++](https://github.com/AFLplusplus)
  
## AFL 
AFL is a mutational, coverage guided fuzzer. It mutates a set of test cases to reach previously unexplored points in the program.
When this happens, the test case triggering new coverage is saved as part of the test case queue.

### Coverage guided Feedback

Is a hybrid metric that combines edge coverage with number of executions on respective edge in one run. It is bucketed into power of two to combat path explosion. 
Input is considered interesting if at least one new bucket for an edge is explored. Buckets (hitcounts) are logged into bitmap during execution. AFL approximates
the [weighted minimum set cover](https://en.wikipedia.org/wiki/Set_cover_problem) to maintain a set of favored test cases regarding coverage. Weights are speed and size.

### Mutations

Two categories: 

1. **Deterministic**: Based on context (bit flips, additions, substitutions etc.)
2. **Havoc**: Randomly stacked to include changes to size of test case (add, delete parts of input). Merging of two cases also possible (called **splicing**)

### Forkserver

No ``execve()`` but IPC controlled Forkserver that writes input when target needs to be executed and then tells the target to fork itself. 
The child will fork and parent waits for termination. Can also fork later in target. 

--> saves initialisation and startup routines

### Persistent Mode 

Patches a loop into the target so that multiple testcases (one per iteration)can be executed in one fork. Does not work if 
iterations cause big state changes.

## Smart Scheduling

1. **AFLFast**: Priotitizes low-frequency paths to explore more branches. Two problems: 
   1. In which order are seeds picked to stress low-frequency paths?
   2. Can the amount of inputs generated from each seed (energy) be tuned?
2. **MOpt**: Introduces mutation scheduling.
   1. Give probabilities to mutation operators using [Particle Swarm Optimization](https://en.wikipedia.org/wiki/Particle_swarm_optimization)
   
## Bypassing Roadblocks

Traditionally, coverage guided fuzzers suffer from roadblocks that prevent full code exploration. 

- Large comparisons
- String checks
- Checksum checks

### LAF-Intel

Bypass hard multibyte comparisons byte by byte. Solved by splitting the comparisons to multiple LLVM passes

1. Simplify `lt, gt` operators to chains of `==` operations 
2. Change signed to unsigned comparisons
3. Split large (16, 32, 64 bit) unsigned int comparisons to chains of multiple 8 bit comparisons

## RedQueen

Explored to bypass hard comparisons and checksum checks without using taint tracking or symbolic execution. It focusses on
comparisons that are Input-to-state (I2S), a type of comparison directly dependend on at least one argument of the input. 

Solved by: Increasing entropy in colorization stage, replacing bytes with random data

--> Observing an operand of such a comparison can reduce the number of guesses to locate its position in the input. These I2S inputs
are then replaced, patching out checksum comparisons. At the end of the fuzzing stage, RedQueen repairs the checksums of the new input. 
In case of failure: Remove the patch

## Mutate Structured Input

Problem: Fuzzers generate mostly invalid inputs, making state of the program inaccessible at stages after parsing.

--> Use input model to restrict the space of generated inputs. This allows more deep path explorations in a program.

### AFL Smart

Uses PEACH pits as input model format. 
1. Extract test case from queue
2. Parse with *deferred cracking* (lazy) --> allows fallback if Smart not needed
3. Result of parsing: virtual structure that represents an AST, which is then used for mutations instead of raw bytes.
   1. Havoc: Stack mutations along others
   2. Deterministic: Only use these mutations on virtual structure

## AFL Additions

### Seed Scheduling

AFL++ incorporates AFLFast and extends it. Included schedules: *fast, coe, explore, quad, lin, exploit* and newly **mmopt** and **rare**.



Links: 
[[Kernel]]
[[link_files/Fuzzing|Fuzzing]]