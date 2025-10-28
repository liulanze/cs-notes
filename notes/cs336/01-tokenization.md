# Tokenization

## Prefill / Decoding

### 1

"什么是Prefill和Decoding？Prefill模型会一次性处理你输入的整个prompt，理解它的意思，并生成一个初始的上下文表示，为后面的生成做好铺垫。Decoding是生成阶段，模型会基于prefill阶段的上下文，逐步生成输出的每个词（或叫token），直到完成整个回答。"

What are Prefill and Decoding? A prefill pass processes your entire input prompt at once, understands its meaning, and builds an initial contextual representation to set up the generation. Decoding is the generation phase: based on the prefill context, the model produces the output token by token until the full answer is complete.

### 2

“为什么要有Prefill和Decode？（1）LLM是自回归模型，意思是他生成文本时类似于接龙一样，每生成一个词，都会把这个词加到上下文中，再去预测下一个词。比如生成今天天气很好时，先生成今天，然后根据今天生产天气，再根据今天天气生成很好。这种方式保证文本连贯，有逻辑，但也意味着必须一步步来。 （2）如果模型一次性生成所有词，比如预测整句话，计算会变得非常复杂，因为它需要同时考虑所有可能的词组合，消耗的算力和时间会大幅增加。而分成prefill和decode两个阶段，prefill先输入，decode在逐步生成，能把任务分成小块，更高效。(3)每个生成的词都依赖于前面的词，比如天气后面接很好比接跑步更合理，这种依赖性要求模型逐步生成，而不是跳跃性预测。”

Why have Prefill and Decode? (1) LLMs are autoregressive: generation works like a chain—after generating a word, it’s appended to the context to predict the next one. For example, to produce “今天天气很好,” it first generates “今天,” then, based on “今天,” generates “天气,” and then, based on “今天天气,” generates “很好.” This ensures coherence and logic, but it also means generation must proceed step by step. (2) If the model tried to generate everything at once (e.g., an entire sentence), computation would become very complex because it would need to consider all possible combinations simultaneously, greatly increasing compute and time. Splitting into prefill (ingesting the input) and decode (stepwise generation) breaks the task into smaller pieces and is more efficient. (3) Each generated token depends on prior tokens—for instance, after “天气,” “很好” is more reasonable than “跑步.” This dependency requires incremental generation rather than jumping straight to the final output.

### 3

"Prefill阶段具体做了什么？假设你输入的prompt是今天天气怎么样，prefill会做这些事：（1）模型会先把今天天气怎么样拆成一个个小单元（token），比如今天，天气，怎么样。然后把这些token转成数字表示（嵌入向量，embedding），tokenization。（2）LLM用的是Transformer架构，里面有个核心机制叫自注意力（self-attention）。在prefill阶段，模型会分析prompt里每个token和其他token的关系，比如怎么样跟天气关系更密切，通过这种计算，模型会生成一个包含全局上下文的表示，理解整个句子的意思。（3）在注意力计算中，模型会为每个token生成Key/Value matrix，这些向量记录了每个词在上下文context中的信息。这些key和value会被存到KV cache中，后面decode阶段会直接用他们，省的重复计算。"

What does the Prefill phase actually do?
Suppose your input prompt is “今天天气怎么样 (How’s the weather today).” The prefill phase performs the following steps:
(1) The model first breaks down the prompt into small units called tokens—for example: 今天 (today), 天气 (weather), 怎么样 (how). Then, it converts these tokens into numerical representations known as embeddings (this process is called tokenization).
(2) LLMs use the Transformer architecture, whose core mechanism is self-attention. During the prefill stage, the model analyzes the relationship between each token and every other token in the prompt—for instance, recognizing that “怎么样” is more closely related to “天气.” Through this computation, the model builds a global contextual representation that captures the overall meaning of the entire sentence.
(3) In this attention computation, the model generates Key and Value matrices for each token. These vectors record contextual information for each word within the sentence. The keys and values are then stored in the KV cache, which the model will directly reuse during the decoding phase—saving time and avoiding redundant calculations.

为什么 Prefill 阶段只缓存 K/V，而不是 Q？

因为在解码阶段（decoding），我们要一个一个地生成新的 token。

每生成一个新的 token，它会有新的 Q（query）去查询之前所有 token 的上下文信息。

而之前的 K/V 不变、可以复用。
所以我们只需要缓存 K/V，
每次 decode 时计算新的 Q，去和缓存的 K/V 做 attention。

🔍 总结一句话：

Prefill 阶段：生成**所有**token的 Q/K/V，全量计算 self-attention；同时缓存 K/V。initial Q的计算结果通过 self-attention 得到的语义关系，会被整合进 hidden states（隐层表示）。

Decoding 阶段：只为当前新 token 计算 Q，复用缓存的 K/V，进行增量 self-attention。

### 4

“Decode阶段具体做了什么？（1） 他会先预测下一个token，基于prefill生成的上下文，模型预测第一个token的概率分布，比如很，不错，晴朗，等等，然后利用算法选一个（比如很） （2） 更新上下文，把很加到上下文里，现在上下文变成今天天气怎么样很，同时，计算很的key/value，更新到kv cache中。（3）重复步骤，基于最新上下文，继续预测下一个token，知道生成结束符或达到最大长度。”

What does the Decode phase actually do?
(1) It first predicts the next token. Based on the context built during the prefill phase, the model estimates a probability distribution over possible next tokens—such as “很 (very),” “不错 (nice),” or “晴朗 (clear)”—and then selects one using a sampling algorithm (for example, choosing “很”).
(2) The context is then updated: the token “很” is appended to the existing context, so the new context becomes “今天天气怎么样很.” At the same time, the model computes the key/value representations for this new token and adds them to the KV cache.
(3) The model repeats this process—using the updated context to predict the next token—until it reaches an end-of-sequence token or the predefined maximum generation length.

在 decoding 阶段，当新 token（比如“很”）被生成时：

模型会 计算这个新 token 的 Q/K/V；

旧 token 的 K/V 不会重新计算；

旧 token 的 Q 也不会重新计算。

也就是说，只有新 token 的 Q 会变化（因为它是新生成的 token 才需要发起查询），而之前的 token 的 Q/K/V 都保持不变。

### 5

Tokenization / detokenization happens on CPU usually, no matmul or parallel computing.
