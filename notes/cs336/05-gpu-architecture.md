# Lecture 5: GPU Architecture and Optimization

## GPU Hardware Architecture

At the hardware level, GPUs contain many **SM (Streaming Multiprocessors)** that independently execute blocks. Each SM further contains many **SP (Streaming Processors)** that can execute threads in parallel.

### Memory Hierarchy

- **Shared memory and L1 cache**: Located within each SM. At the software level, threads within one block can share this memory during computation.
- **L2 cache**: Still on the GPU chip, shared across all SMs.
- **Global memory (DRAM)**: Located outside the GPU chip.

### Task Execution

When there are no tasks, SMs remain idle. When a task starts, blocks are assigned to available SMs for computing.

## Thread Organization

Between blocks and threads, there is an intermediate level called **warps**:
- A block can include many warps
- A warp contains a fixed 32 threads

**Important**: Data communication between different blocks must go through global memory.

## SIMT Architecture

GPU uses **SIMT (Single Instruction, Multiple Threads)** mode: all threads within the same warp must execute the same instruction, only the data differs.

## GPU Interconnect

The GPU connects to its host through either:
- **PCIe** (hardware interface)
- **NVLink** (hardware interface)

Between different hosts, they use **InfiniBand** to connect (also hardware).

## GPU Performance Bottlenecks

To increase GPU capability, there are three key points:
1. **Hardware FLOPS**
2. **DRAM bandwidth**
3. **Interconnect bandwidth**

**Future trend**: GPU efficiency bottlenecks will be mainly memory and interconnect, because these two areas are developing much slower than hardware FLOPS.

## GPU Optimization Techniques

### 1. Reduce Precision

Lower precision leads to higher speed:
- **fp32**: 8 bytes
- **fp16**: 4 bytes

Using lower precision increases computational speed.

### 2. Kernel Fusion

The main idea of GPU CUDA optimization is to **reduce global memory usage**.

**Example**: If computation includes many steps, instead of sending each step's result back to memory and then computing the next step, compute them all at once within the same CUDA kernel, then return the final result back to memory.

#### 2.1 Burst Mode

Burst mode provides extra data every time when querying/reading memory, rather than traditionally returning just the one piece of data that was queried.

### 3. Tiled Matrix Multiplication (Tiling)

This is the big optimization technique! Tiling breaks matrix multiplication into smaller chunks (tiles) that fit in shared memory, dramatically reducing the number of global memory accesses.

---

**Note**: This lecture contains tons of fantastic knowledge that cannot be fully captured in notes. Review the video multiple times for deeper understanding.
