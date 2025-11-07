# Lecture 7: Parallelism

## Introduction to Multi-GPU/Multi-Machine Parallelism

The lecture begins with an introduction to multi-GPU and multi-machine parallelism papers. (Reference: phone snapshots)

## Collective Communication Basics

Collective communication primitives are fundamental for distributed computing. (See phone pic 2)

Two key operations are especially important:
1. **All-Reduce**
2. **Reduce-Scatter-Gather**

(See phone pic 3 for detailed diagrams)

## Part 1: Recap

### Data Center as a Unit of Compute

The modern data center should be viewed as a single computational unit.

### Goals of Multi-Machine Scaling

What we want from multi-machine scaling:
- **Linear memory scaling**: Maximum model parameters scale with the number of GPUs
- **Linear compute scaling**: Model FLOPs scale linearly with the number of GPUs

### Foundation

Simple collective communication primitives enable these scaling properties.

## Part 2: Standard LLM Parallelization Primitives

### 1. Data Parallelism (DP)

**Concept**: Create replicas of the whole model with its parameters, run multiple engine replicas for higher throughput.

**Coordination**: Orchestrate via Ray or Kubernetes (K8s)

**Note**: ZeRO is NOT used in vLLM, as it is a training optimizer, not an inference feature.

### 2. Model Parallelism

Model parallelism splits the model itself across multiple GPUs.

#### 2.1 Pipeline Parallelism (PP)

**Concept**: Separate the model's layers across multiple GPUs, where each GPU handles one or more layers.

**Drawback**: Can suffer from pipeline bubbles (idle time when GPUs wait for data from previous stages).

#### 2.2 Tensor Parallelism (TP)

**Concept**: Similar to multi-GPU tiled matrix multiplication. Splits individual tensors/operations across GPUs.

**Best practice**: 
- Use tensor parallelism within a single node (up to 8 GPUs)
- Requires high-speed interconnects (low latency, high bandwidth)

**Rule of thumb**: Use TP when you have low-latency but high-bandwidth interconnects (like NVLink within a node).

## Summary

| Parallelism Type | What is Parallelized | Use Case |
|------------------|---------------------|----------|
| **Data Parallelism (DP)** | Training data | Increase throughput with model replicas |
| **Pipeline Parallelism (PP)** | Model layers | Split model vertically across GPUs |
| **Tensor Parallelism (TP)** | Individual tensors/operations | Split within a node with fast interconnects |
