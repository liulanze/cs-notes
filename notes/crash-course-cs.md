# Crash Course Computer Science Note

1. [Early Computing](#early-computing)
2. [Electronic Computing](#electronic-computing)
3. [Boolean Logic & Logic Gate](#boolean-logic--logic-gate)
4. Representing Numbers and Letters with Binary
5. Arithmetic Logic Unit - ALU
6. [Register and RAM (CPU L1 Cache)](#register-and-ram-cpu-l1-cache)
7. [CPU](#cpu)
8. Instructions & Program
9. [Advanced CPU Design](#advanced-cpu-design)
10. [Early Programming](#early-programming)
11. The First Programming Language
12. Programming Basics
13. Algorithms
14. Data Structures
15. Alan Turing
16. Software Engineering
17. Integrated Circuit & Moore's Law
18. Operating Systems
19. Memory & Storage
20. Files & File Systems
21. Compression
22. Keyboards & Command Line Interface
23. Screens & 2D Graphics
24. The Cold War and Consumerism
25. The Personal Computer Revolution
26. Graphical User Interfaces - GUI
27. 3D Graphics
28. Computer Networks
29. The Internet
30. The World Wide Web
31. Cybersecurity
32. Hacker & Cyber Attacks
33. Cryptography
34. Machine Learning & Artificial Intelligence
35. Computer Vision
36. Natural Language Processing
37. Robots
38. Psychology of Computing
39. Educational Technology
40. The Singularity, Skynet, and Future of Computing

## Early Computing
- Abacus
- Punch card

## Electronic Computing
- Semiconductor
- Transistor
- Computer will do addressing on everywhere in memory for reading / saving data.
- ASCII -> Unicode

## Boolean Logic & Logic Gate
- `XOR`
    - `true` `XOR` `true` -> `false`
    - `true` `XOR` `false` -> `true`
    - `false` `XOR` `true` -> `true`
    - `false` `XOR` `false` -> `false`

## Register and RAM (CPU L1 Cache)
- Circuit
- ALU is consisted by a lot of logic gates. CPU leverages these ALUs to do the calculation. ALU is made by silicon, not seen by human.
- Latch <- storage, while ALU <- logic.
- A group of latches called register.
- Today memory has 64-bits registers.
- Address like row 12, col 8 can be represented with `11001000`, like `1100` is 12, while `1000` is 8.
- The random in concept `RAM` means no matter when and where it can search the certain address's data.

## CPU
- High level, when execute app, CPU (1) fetch from memory, (2) decode, (3) execute.
- Registers are very small, but very fast storage located within CPU.
- My understanding is when CPU executes certain software, the mid value it calculates will be saved in the register, also, these inter values can be updated back to its address from memory.
- Clock speed cycle: CPU receives command -> decode -> execution.
- Overclocking: increase the clock speed temporary.
- Underclocking
- Dynamic frequency scaling.

## Advanced CPU Design
- If CPU can not find the required data from CPU's cache, it called `cache miss`, otherwise `cache hit`. (This name is also carried over to system design level when it comes to the regualr memory outside the CPU.)
- Dirty Bit: When CPU does operations, like ALU calculation, in order to increase efficient, CPU will copy partial data from memory to CPU's cache (like L1 CPU cache). Also, these data will be saved temporary in the registers, calculated, then updated and saved back to the CPU cache.
- When everytime CPU cache is full (CPU cache size is like 20MB), those data from CPU cache called `dirty bit`, because they are different with the data from its original memory. So, these dirty bits will be marked, and check the memory, if data are different, update them. When these are all done, reset the CPU cache to empty.
- To increase the efficient, the CPU's fetch, decode, and execute can run in parallel. Because these three parts are done by different parts. However, sometimes it may cause issue, like the data fetched in being executed, which causes fetching the old data. So, you need to determine all the dependency or consider lock.
- The core in CPU means multiple parts that can do `fetch-decode-exec` at the same time. Core can be treated as a single CPU, but they are connected with each other as well, sharing resources like CPU cache.
- One core can have two threads.
- For Big Tech, services like searching, video services will require tons of CPUs - Super Computer.

## Early Programming

