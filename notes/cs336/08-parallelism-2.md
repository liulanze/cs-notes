# Lecture 8: Parallelism 2 - Multi-GPU Communication

## Hardware Interconnect Hierarchy

### Single Node, Single GPU
- **L1 cache / Shared memory**: On-chip fast memory
- **HBM (High Bandwidth Memory)**: GPU's main memory

### Single Node, Multi-GPU
- **NVLink**: Within a node, NVLink connects GPUs directly, bypassing the CPU
- Provides high-bandwidth, low-latency communication between GPUs

### Multi-Node, Multi-GPU
- **NVSwitch**: Across nodes, NVSwitch connects GPUs directly, bypassing Ethernet
- Enables fast inter-node GPU communication

## Collective Operations

The following collective operations enable distributed computation (see phone pictures for visual demonstrations):

- **Broadcast**: Send data from one GPU to all others
- **Scatter**: Distribute different chunks of data to different GPUs
- **Gather**: Collect data from all GPUs to one GPU
- **Reduce**: Combine data from all GPUs using an operation (e.g., sum)
- **All-gather**: Each GPU gathers data from all other GPUs
- **Reduce-scatter**: Reduce and distribute results across GPUs
- **All-reduce**: Reduce data across all GPUs and broadcast result to all

## NCCL (NVIDIA Collective Communication Library)

**Purpose**: NCCL translates collective operations into low-level packets that are sent between GPUs.

**Packet (数据包)**: A small block of contiguous bytes (data fragment)

### NCCL Capabilities

NCCL can:
- **Detect topology** of hardware: number of nodes, switches, NVLink/PCIe connections
- **Optimize the path** between GPUs for efficient communication
- **Launch CUDA kernels** to send/receive data

### PyTorch Integration

The `torch.distributed` package wraps NCCL to make it easier to use at a higher level, abstracting away low-level details.

## Parallelism Dimensions Overview

| Dimension | Meaning | Parallelized Over | Used in vLLM? | Notes |
|-----------|---------|-------------------|---------------|-------|
| **Data (batch) parallelism** | Different input sequences (prompts) processed concurrently | Batch dimension (multiple requests) | ✅ Yes (core feature) | vLLM's continuous batching merges requests dynamically across users to fully utilize GPUs |
| **Tensor (width) parallelism** | Split within each layer (e.g., matmul shards across GPUs) | Model weight tensors | ✅ Yes | Uses tensor parallelism (TP) via NCCL; shards weights for large models across GPUs |
| **Expert parallelism** | Split different Mixture-of-Experts (MoE) layers across GPUs | Experts or sub-networks | ⚙️ Partial support | vLLM recently added MoE support (each GPU can host subset of experts; uses routing) |
| **Pipeline (depth) parallelism** | Different layers (model depth) distributed across GPUs | Layer groups (stages) | ✅ Yes | Supported; can combine with TP (like Megatron). Each GPU holds a model stage |
| **Sequence (length) parallelism** | Split one long sequence across GPUs by token positions | Token sequence length dimension | ❌ No | Not used in inference. PagedAttention handles memory, but doesn't shard sequences across GPUs |

## Key Takeaways

1. **Hardware matters**: Different interconnects (NVLink, NVSwitch) enable different parallelism strategies
2. **Collective operations**: Fundamental primitives for distributed computation
3. **NCCL**: Low-level library that makes GPU-to-GPU communication efficient
4. **Multiple parallelism dimensions**: Modern LLM serving combines several parallelism strategies to maximize throughput and handle large models
