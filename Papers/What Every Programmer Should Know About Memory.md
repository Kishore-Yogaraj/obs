https://www.youtube.com/watch?v=SQpS3NuOFT8&t=197s

I want to read this paper because I want to learn about how we actually talk to the computer and how to make it easier to talk to the computer. I think as we grow into the time of AI, robotics, and low level development is what will be the key of our worlds. People who can really talk to computers at the lowest level will excel in what the world has to offer. Looking at the highest paid developers and most important people in the world, they do what they're able to because they're able to write code at the lowest level. I personally think there is a power and significance in being able to write code at the lowest level and talk directly to a computer

Part 3 of the paper is very important
- When your program is on the disk how it gets pulled into RAM and how that starts moving through the various caches
- L3 Shared Cache
- L2 Cache
- L1 Cache
- Split between L1 data cache and instruction cache
- Talks about cache lines
- Tags each cache lines
- Cache set
- Offset
- Walk me through the memory hierarchy of a computer?
- What are the various levels of cache?
- Why is L1 cache separate between data and instruction?
- Associativity
	- Direct map caching
	- Full associative caching
	- Nway set associativity

Chapter 4 is on virtualization or virtual memory
- How does the operating system provide the illusion that you have more resources to play with than what's physically available by the hardware presented
- Memory address translation
- Multi level page tables
- Translation look aside buffers

Chapter 5 is about NUMA hardware
- Layout of CPU cares relative to memory
- NUMA vs UMA

Chapter 6.4
- Focuses on concurrency optimization
- Context switches
- Difference between hardware threads and software threads
- What is oversubscribing
- Performance degradation

### What is a CPU cache
- CPU caches is a small very fast type of memory located inside or very close to the processor core
- Its purpose is to to store frequently used data and instructions so the CPU doesn't have to fetch them from the much slower main memory every time
**Why do they exist?**
- Modern CPUs can execute billions of instructions per second but RAM is much slower in comparison. Without a cache, the CPU would spend most of its time waiting for data to arrive
**L1 Cache**
- Closest to the CPU core
- Extremely fast but very small (16-128 KB)
- Split into instruction cache and data cache
**L2 Cache**
- Larger than LQ
- Slower than LQ but still much faster than RAM
- Can be private to each core or shared between a few cores
**L3 Cache**
- Shared across multiple cores in multi core CPUS
- Much larger
- Slower than L1 and L2 but still faster than ram

