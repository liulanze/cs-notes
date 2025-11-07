# Lecture 6: Kernels, Triton, and Profiling

## Performance Analysis: Two Key Concepts

- **Benchmarking**: How long does it take?
- **Profiling**: Where is time being spent?

## Matrix Multiplication and Kernel Selection

For different sizes of matrices, when calling `matmul()`:
- If you are using PyTorch, it will automatically help select the best kernel option for matrix multiplication
- The framework optimizes based on the input dimensions

## NVIDIA Nsight Systems

NVIDIA Nsight Systems is a profiling tool for analyzing GPU performance. (See lecture video for detailed demonstration)

## CPU-GPU Interaction: A Key Performance Consideration

**Important insight** (around 40 minutes in the lecture):

When CPU writes a `print(loss)` statement:
1. CPU must wait for GPU to finish all computation
2. Then print the loss value
3. Only after that can it send the following commands

**Performance impact**: If you remove the CPU print statement, training speed can improve significantly.

**Why?** 
- GPU has latency
- CPU executes commands sequentially
- The print statement forces synchronization between CPU and GPU

## Key Profiling Insight

By profiling, we can observe that:
- **CPU and GPU are separate devices**
- They can work independently
- But they can also affect each other's performance

## Profiling Best Practices

For profiling steps, it's common practice to add annotations like `step_{step}` for each profiling step. This makes it easier to identify specific sections when analyzing results in Nsight.

**Example workflow**:
```python
# Add annotations for profiling
torch.cuda.nvtx.range_push("step_0")
# ... training code ...
torch.cuda.nvtx.range_pop()

torch.cuda.nvtx.range_push("step_1")
# ... training code ...
torch.cuda.nvtx.range_pop()
```

Then check the results in Nsight Systems to see where time is being spent.
