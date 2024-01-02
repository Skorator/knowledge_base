# Fuzzing Introduction

### What is fuzzing? 

- Dynamic program testing. During runtime in contrast to static program testing (doesn't work for black box)
### Fuzzing History 
- Where did it start, milestones (AFL, AFL++)


### Why should we be fuzzing? 
- More thorough, less false positives

### Difference between Kernel and User space fuzzing
- Static Analysis vs Dynamic Analysis, where does fuzzing exceed SA? 

## Background 

### How does fuzzing actually work? (More detail)
- Blackbox, Greybox fuzzing
- Instrumentation (compile time, feed back generation)
- Targets, Mutators, Harness writing, AFL++?

## Challenges in Fuzzing


# State-of-the-art Survey

## Explain the Taxonomy 
- How are papers compared, selected?
- Which papers are presented?
- Refer to this when concluding the paper
##  VUzzer: Application Aware Evolutionary Fuzzing

## DiFuze: Interface Aware Fuzzing of Kernel Drivers

## kAFL: Hardware Assisted Feedback Fuzzing

## FuzzNG: No grammar, no problem

## ACTOR: Action-guided kernel fuzzing#

### INTRODUCE TABLE THAT COMPARE ALL THE TAXONOMY RELEVANT PROPERTIES

## Discussion 
- Compare the papers, reflect on how they improved upon each other, how did fuzzing evolve?

## Conclusion
- Where is fuzzing now
### Challenges
- Mobile phones daily interaction, first thing, last thing, collect huge amounts of data 
- Importance is "obvious"
- Security measures include: Address Space Layout Randomisation, Data Execution Protection, and SELinux, Advanced Sandboxing
- Less attacks on userspace applications --> more focus on kernel space applications 
- Vulnerability increase kernel vs userspace (62 difuze)
- Two types of kernel code (kernel core, device drivers)
- Third party vendors make most of bugs!! (62 difuze)
- Device drivers are essential part of modern devices
- Can find more vulnerabilites than static analysis with a lower false positive rate 

