# Lecture 2: PyTorch, Resource Accounting

## Parameters in Neural Networks

A **parameter** is a trainable weight inside a neural network - basically, a number that the model learns during training. Each parameter controls how strongly one neuron affects another. During real-world training, the model adjusts millions or billions of these numbers to minimize its prediction error.

## Floating-Point Precision

- **float32 (fp32)**: The default precision in deep learning. FP32 is sufficient for most use cases and is already quite large.
- **Better alternatives**: bfloat16 (bf16) or float16 (fp16), and even fp8
- **Training with FP32**: Safe but requires a lot of memory
- **Performance comparison** (GPU speed):
  - **fp8**: Fastest
  - **fp16/bf16**: Fast
  - **fp32**: Slowest, but safest

## Floating-Point Operations (FLOPs)

A **floating-point operation (FLOP)** is a basic arithmetic operation like addition or multiplication.

### Two Confusing Acronyms (pronounced the same):

1. **FLOPs**: Floating-point operations - measures the amount of computation done
2. **FLOPS**: Floating-point operations per second - measures hardware speed

**Example**: GPT-4 took approximately $2 \times 10^{25}$ FLOPs to train.

## Matrix Multiplication FLOPs

For `matmul()`, we have one multiplication ($x[i][j] \times w[j][k]$) and one addition per $(i, j, k)$ triple.

**Formula**: 
$$\text{actual\_num\_flops} = 2 \times B \times D \times K$$

### Example Code:

```python
B = 16384  # Batch size / number of tokens
D = 32768  # Input dimension
K = 8192   # Output dimension

device = get_device()
x = torch.ones(B, D, device=device)
w = torch.randn(D, K, device=device)
y = x @ w
```

**Note**: When initializing tensors in PyTorch with the `device` parameter, PyTorch automatically moves data from CPU to GPU for computation.

### Real-World Interpretation:

- **B**: Number of data points (token count)
- **D, K**: Number of parameters

**FLOPs for forward pass**: $2 \times (\text{tokens}) \times (\text{parameters})$

This formula generalizes to Transformers.

## Model FLOPs Utilization (MFU)

**Definition**: 
$$\text{MFU} = \frac{\text{actual FLOPS}}{\text{promised FLOPS}}$$

- **Promised FLOPS**: Theoretical maximum, ignoring communication overhead
- **Good MFU**: ≥ 0.5
- **Higher MFU**: Achieved when matrix multiplication dominates computation

## Training: Forward and Backward Passes

In machine learning, training happens in two phases per iteration:

### Forward Pass - Compute Loss

**What happens**: Data flows forward through the network to generate predictions.

1. **Input** → pass data through the network
2. **Layer by layer computation**:
   - Linear transformation: $z = Wx + b$
   - Activation function: $a = \sigma(z)$
3. **Output** → get predictions
4. **Compute loss**: Measure how wrong the predictions are
   - Example: Mean Squared Error: $L = \frac{1}{n}\sum(y_{pred} - y_{true})^2$

**Result**: A single number (the loss) that indicates how poorly the model is performing.

### Backward Pass - Compute Gradients

**What happens**: Error flows backward through the network to compute how to improve.

1. **Start at the loss**: Calculate $\frac{\partial L}{\partial \text{output}}$
2. **Chain rule backwards through each layer**:
   - How does loss change with respect to each weight? $\frac{\partial L}{\partial W}$
   - How does loss change with respect to each bias? $\frac{\partial L}{\partial b}$
3. **Propagate gradients back** layer by layer using the chain rule:
   $$\frac{\partial L}{\partial W_1} = \frac{\partial L}{\partial a_2} \cdot \frac{\partial a_2}{\partial z_2} \cdot \frac{\partial z_2}{\partial W_1}$$

**Result**: Gradients for every parameter showing which direction and how much to adjust them.

### Update Weights

After both passes, use gradient descent:
$$W_{new} = W_{old} - \alpha \cdot \frac{\partial L}{\partial W}$$

**Training cycle**: forward → compute loss → backward → compute gradients → update weights → repeat
