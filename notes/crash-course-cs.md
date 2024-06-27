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
18. [Operating Systems](#operating-systems)
19. Memory & Storage
20. [Files & File Systems](#files--file-systems)
21. Compression
22. Keyboards & Command Line Interface
23. [Screens & 2D Graphics](#screens--2d-graphics)
24. The Cold War and Consumerism
25. The Personal Computer Revolution
26. Graphical User Interfaces - GUI
27. 3D Graphics
28. [Computer Networks](#computer-networks)
29. The Internet
30. The World Wide Web
31. [Cybersecurity](#cybersecurity)
32. Hacker & Cyber Attacks
33. Cryptography
34. Machine Learning & Artificial Intelligence
35. [Computer Vision](#computer-vision)
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
- Transister is made by Photolithography technology, and each chip contains billions of transistors.
- For Apple's M chip, it integrated CPU, GPU and memory, because of that, the data transfer delay has been detracted significantly. Therefore, for sure their memory / GPU are Apple brand as well.

## Early Programming
- Compiler is a software. Every language has its own compiler.
- The reason why C/C++/Go is so fast is that the compiler of them can directly transform the code into machine code (well, there is assembly language transform process between language code and machine code process for sure). However, languages like Java/Python need extra steps to generate machine code like interpreted language.
- Captcha
- Integrated circuits (ICs)
- Motherboard (PCB) is not a IC, but components / devices on the motherboard can be ICs.
- The circuit on the motherboard can be made by copper if they only transfer electronic bit. Otherwise if it needs calculation like check statement on 0/1, they need to be made by silicon.
- PCB material works with ICs to be powerful.
- Chips can be existed in many devices, like CPU/GPU/memory/disk. Chip is made by silicon, used for solve data, execute program, manage connection, etc.

## Operating Systems
- BIOS (Basic Input / Output System) / UEFI (Unified Extensible Firmware Interface) are pre-installed on the motherboard as firmware. Those firmwares are storaged in a certain type of a chip, usually a non-volatile storage device like ROM, EPROM, or Flash.
    1. When boot the machine, BIOS / UEFI are the softwares which be executed firstly, their responsibility is to check system health (POST - Power on self test), initialize and test computer hardware, to make sure every hardware is ready. Yeah I found this feature when assumbling my own PC.
    2. Then bootloader, BIOS / UEFI are responsible to load OS from connected hard ware storage device (SSD, HDD, or CD). Usually `grub.cfg` under boot partition.
    3. Provide interaction between OS and hardware.
    4. Provide support on hardware setup.
- OS has two features:
    - System needs -> Kernel
    - Dev needs -> /pkg/bins
- In the history, because Kernel is so small, that public popular people can use it on a cheaper machine. When Kernel faces issue, back in the long history it will keep printing `Kernel Panic` without anymore logs..

## Files & File Systems
- Each file on the hard drive is saved continuous and close.
- Directory file can save the location (where start and where end) along with file size of each file.
- When file gets modified, its information from the directory file will also be updated.
- Filesystem separate the disk into same size blocks.
- For each file, it can take multiple blocks, also, block it takes can be not saved fully.
- When some files get deletd, on the disk, it won't be wipped, instead, its directory file will update the info saying these blocks are free again! When new data overwrite into these old data, data overwrited finally.
- This "filesystem block" idea has also be used in memory tech, called virtual memory. It makes program thought they are used on a consistent available memory, but in real, the physical memory has been devided into many memory fragmentations.

## Screens & 2D Graphics
- Video can save its pixel, and it can detect where the pixel not change often to only save one time for all. While, for where pixel changes often:
    - Save the change
    - Or use the pixels stored in the previous frame to create animations and patches. For example, after a hand is saved, you only need to save its motion coordinates to create an animation. ("The hand here is actually the hand in the past").
    - If you have ever experienced that the video frames are garbled when you are watching it, it means that the video uses patch technology to store pixels, and the patches are messed up.
- For 3A games, instead of saving pixel, saving motion coordinates can save more spaces (VRAM, general memory).
- Apple pencil uses tech to record its motion coordinates.
- VRAM - Video RAM.

## Computer Networks
- hop counts the number of routers when machine A goes to machine B.
- hop limit
- To avoid transfer too large data at once, data will be devided into coupel packets. Each packet is transferred by routers, and packet contains info for destination, for example, the format is decided by IP protocol.
- The packets of a same file can use different routers to reach the destination. TCP/IP can solve packets misorder issue.
- When device wants to connect to net. It firstly connect LAN to family LAN (local area network), then router in family can connect to a WAN (wide area network) from community. And an even larger WAN can be connected by community to maybe a city. Eventually to the end maybe countrywide.
- WLAN - wireless local area network. WiFi.
- IP gets the packets to the right computer, while UDP gets the packets to the right program running on that computer.
- With UDP, there will be no shake, so after sending the packets, you won't know whether the packets have been arrived or not, so when networks is bad, you will see video stuck because some of the packets have not arrived.
- Also, nowadays, mostly developers just use TCP/IP, not UDP, because (1) TCP header contains dequence number, when arrived, it can sort. (2) TCP/IP packets when arrive, it can tell back to the sender that arrived (ACK).
- However, UDP still useful, when you need high speed stream, like live video, CSGO, which are time sensitive, that you may use UDP as protocol.
- Because there are tons of domain names, so domain name / ip addresses are saved using tree. Actually, in the real world, big data will save by tree mostly. I feel because tree can search fast and sort fast, also, you can insert or delete nodes easily.
- www != internet, internet includes www (a software under the hood), Ins, WOW, Wechat...
- Request web uses HTTP methods.

## Cybersecurity
- malware
- trojan horses pretend a good software, but actually a malware.
- exploit
- botnet -> thoes are machines that be hacked by hacker, used for sending spams, mining bitcoin, or attacking.
- cyber warfare
- cryptography - use cipher algorithm to convert plain text to cipher text.
- encryption <=> decryption
- AES encryption
- Symmetric encryption is like you and me painting, the bottom color A is the public key, and I paint a color B on this bottom color A, and you also paint another color C on the bottom color A, then we switch board, I add my color B on your (A + C), same you add your C on my (A + B), we will finally get the same (A + B + C).
- Asymmetric encryption is like I have a box with a private key, I leave the box open and give it to you, you put some contents in it, and close the box and return it back to me. I can use my private key to open the box seeing the content.

## Computer Vision
- frame number
- AI should thank game, which requires high performance of GPU which encourage the GPU to improve year by year, which creates today's AI.
- Computer Vision (CV) - resolve pixel, leverage Kernel to solve pixel, convolution.
- Convolution Neural Network - CNN
