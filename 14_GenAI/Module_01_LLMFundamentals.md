# Generative AI Interview Master Guide

# Module 1: LLM Fundamentals (Questions 1–25)

---

# 1. What is Generative AI?

## Definition

Generative AI refers to artificial intelligence systems that can **create new content** such as text, code, images, audio, or video instead of only analyzing existing data.

Unlike traditional AI, which focuses on prediction or classification, Generative AI learns the probability distribution of data and generates new samples.

---

## Examples

- ChatGPT
- Claude
- Gemini
- GitHub Copilot
- Midjourney
- DALL·E

---

## Applications

- Content generation
- Code generation
- Chatbots
- Translation
- Image generation
- Drug discovery
- Customer support

---

## Interview Answer

> Generative AI is a branch of AI that creates new content by learning patterns from training data. Modern systems are powered by Large Language Models (LLMs) and Transformer architectures.

---

# 2. What is an LLM?

## Definition

LLM stands for **Large Language Model**.

It is a neural network trained on billions or trillions of tokens to understand and generate human language.

---

## Characteristics

- Billions of parameters
- Transformer architecture
- Self-supervised training
- Context-aware generation

---

## Examples

- GPT-4
- GPT-5 family
- Llama
- Claude
- Gemini
- Mistral

---

## Interview Answer

> An LLM is a Transformer-based neural network trained on massive text corpora to predict the next token, enabling tasks such as reasoning, summarization, coding, and question answering.

---

# 3. How do LLMs work?

## High-Level Flow

```text
Input Text
      │
Tokenization
      │
Embeddings
      │
Transformer Layers
      │
Self-Attention
      │
Next Token Prediction
      │
Generated Response
```

---

## Training

The model repeatedly predicts the next token.

Example:

```
I love playing ______

↓

cricket
```

Millions of such predictions teach language patterns.

---

## Interview Answer

> LLMs tokenize input, convert tokens into embeddings, process them through Transformer layers using self-attention, and predict the most probable next token repeatedly until the response is complete.

---

# 4. GPT vs BERT

| GPT | BERT |
|------|------|
| Decoder-only | Encoder-only |
| Generates text | Understands text |
| Left-to-right | Bidirectional |
| Autoregressive | Masked Language Model |
| Chatbots | Classification, Search |

---

## Example

### GPT

```
Write an email.
```

Generates an email.

### BERT

```
Is this review positive?
```

Performs sentiment classification.

---

## Interview Answer

> GPT is optimized for text generation using a decoder architecture, while BERT is optimized for language understanding using a bidirectional encoder.

---

# 5. Encoder vs Decoder vs Encoder-Decoder

## Encoder

Reads the entire input.

Examples:

- BERT
- RoBERTa

Used for:

- Classification
- Search
- Embeddings

---

## Decoder

Generates output token by token.

Examples:

- GPT
- Llama

---

## Encoder-Decoder

Uses both.

Examples:

- T5
- BART

Ideal for:

- Translation
- Summarization

---

## Interview Answer

> Encoders focus on understanding input, decoders generate output, and encoder-decoder models combine both for sequence-to-sequence tasks.

---

# 6. Transformer Architecture

## Components

```text
Input
 │
Embedding
 │
Positional Encoding
 │
Multi-Head Attention
 │
Feed Forward Network
 │
Layer Normalization
 │
Output
```

---

## Advantages

- Parallel processing
- Long-range dependency modeling
- State-of-the-art NLP performance

---

## Interview Answer

> The Transformer architecture replaces recurrent networks with self-attention, enabling parallel computation and better modeling of long-range relationships.

---

# 7. Self-Attention

## Definition

Allows every token to attend to every other token.

Example:

```
The cat sat on the mat because it was soft.
```

"It" attends to "mat."

---

## Formula

```text
Attention(Q,K,V)
=
Softmax(QKᵀ / √d)
× V
```

---

## Benefits

- Captures context
- Handles long dependencies

---

## Interview Answer

> Self-attention computes relationships between all tokens in a sequence, allowing the model to understand context efficiently.

---

# 8. Multi-Head Attention

Instead of one attention mechanism, multiple attention heads learn different relationships simultaneously.

```text
Input

↓

Head 1 → Grammar

Head 2 → Meaning

Head 3 → Syntax

↓

Concatenate

↓

Output
```

---

## Benefits

- Better contextual understanding
- Parallel learning
- Richer representations

---

## Interview Answer

> Multi-head attention enables the model to capture different types of relationships by learning multiple attention patterns in parallel.

---

# 9. Positional Encoding

Transformers process tokens in parallel, so they need positional information.

---

## Example

```
Ram loves Shyam

Shyam loves Ram
```

Same words, different meaning.

---

## Interview Answer

> Positional encoding injects sequence order information into token embeddings because self-attention alone is position agnostic.

---

# 10. Tokenization

Converts text into tokens.

Example

```
ChatGPT is amazing

↓

["Chat", "G", "PT", "is", "amazing"]
```

---

## Types

- Word
- Character
- Subword

---

## Interview Answer

> Tokenization converts raw text into smaller units that the model can process.

---

# 11. BPE vs WordPiece vs SentencePiece

| BPE | WordPiece | SentencePiece |
|------|-----------|---------------|
| Frequency-based merges | Probability-based | Language-independent |
| GPT | BERT | T5, Llama |

---

## Interview Answer

> All three are subword tokenization algorithms. BPE merges frequent pairs, WordPiece optimizes likelihood, and SentencePiece works directly on raw text.

---

# 12. Context Window

## Definition

Maximum number of tokens a model can consider in a single interaction.

Example

```
Input Tokens

+

Output Tokens

≤ Context Window
```

---

## Importance

- Long documents
- Chat history
- RAG

---

## Interview Answer

> The context window defines how much information the model can process at once, including both prompt and generated tokens.

---

# 13. Temperature

Controls randomness.

```
Temperature = 0
```

Deterministic output.

```
Temperature = 1.2
```

More creative output.

---

## Typical Values

- 0.0–0.3 → Factual
- 0.7 → Balanced
- 1.0+ → Creative

---

## Interview Answer

> Temperature controls output randomness by adjusting the probability distribution over possible next tokens.

---

# 14. Top-K Sampling

The model considers only the top **K** most probable next tokens.

Example

```
Top 5 tokens only
```

---

## Interview Answer

> Top-K sampling limits token selection to the K highest-probability candidates, improving output quality while maintaining diversity.

---

# 15. Top-P Sampling (Nucleus Sampling)

Instead of a fixed K, choose the smallest set of tokens whose cumulative probability reaches **P**.

Example

```
P = 0.9
```

---

## Interview Answer

> Top-P dynamically selects candidate tokens based on cumulative probability, making it more adaptive than Top-K.

---

# 16. Beam Search

Generates multiple candidate sequences simultaneously and keeps the best-scoring ones.

---

## Advantages

- Better translation
- More accurate summarization

---

## Drawbacks

- Slower
- Less diverse

---

## Interview Answer

> Beam Search explores multiple output sequences in parallel and chooses the highest-probability result.

---

# 17. Hallucination

## Definition

When an LLM generates incorrect or fabricated information confidently.

---

## Causes

- Missing knowledge
- Ambiguous prompts
- No external grounding

---

## Mitigation

- RAG
- Better prompts
- Tool calling
- Fine-tuning
- Human verification

---

## Interview Answer

> Hallucination occurs when an LLM generates plausible but incorrect information. Retrieval, grounding, and validation help reduce it.

---

# 18. Prompt Engineering

## Definition

Designing prompts to improve model performance.

---

## Techniques

- Clear instructions
- Role prompting
- Examples
- Constraints
- Output formatting

---

## Interview Answer

> Prompt engineering optimizes model behavior by carefully designing instructions, examples, and context.

---

# 19. Few-shot vs Zero-shot vs One-shot

### Zero-shot

No examples.

### One-shot

One example.

### Few-shot

Multiple examples.

---

## Comparison

| Technique | Examples |
|-----------|----------|
| Zero-shot | 0 |
| One-shot | 1 |
| Few-shot | 2 or more |

---

## Interview Answer

> Zero-shot relies only on instructions, one-shot provides a single example, and few-shot provides several examples to guide the model.

---

# 20. Chain-of-Thought Prompting

Encourages the model to reason through intermediate steps.

Example

```
Let's think step by step.
```

---

## Benefits

- Better reasoning
- Improved math
- Complex problem solving

---

## Interview Answer

> Chain-of-Thought prompting encourages intermediate reasoning, improving performance on multi-step tasks.

---

# 21. Function Calling

## Definition

The model returns a structured request to invoke a predefined function.

Example

```json
{
  "function": "get_weather",
  "arguments": {
    "city": "Delhi"
  }
}
```

---

## Use Cases

- APIs
- Databases
- Calculators

---

## Interview Answer

> Function calling enables an LLM to invoke predefined functions with structured arguments instead of generating free-form text.

---

# 22. Tool Calling

## Definition

Allows the model to interact with external tools and systems.

Examples

- Search engines
- Databases
- Code execution
- Calendars

---

## Difference from Function Calling

- Function calling typically invokes predefined application functions.
- Tool calling is a broader concept that includes external services and utilities.

---

## Interview Answer

> Tool calling extends LLM capabilities by integrating external systems for real-time information and actions beyond the model's internal knowledge.

---

# 23. Model Parameters

## Definition

Parameters are the learned weights of the neural network.

Examples

- 7B
- 13B
- 70B
- 175B+

---

## Trade-offs

- More parameters → Better capability (often)
- Higher memory and compute requirements
- Slower inference

---

## Interview Answer

> Model parameters are the learned weights acquired during training. Larger models generally capture more complex patterns but require greater computational resources.

---

# 24. Fine-tuning vs Prompt Engineering

| Fine-tuning | Prompt Engineering |
|-------------|--------------------|
| Updates model weights | Does not change weights |
| Requires training data | Uses better prompts |
| Expensive | Low cost |
| Long-term behavior changes | Task-specific guidance |

---

## When to Use

### Prompt Engineering

- Fast iteration
- General tasks
- Prototyping

### Fine-tuning

- Domain specialization
- Consistent style
- Specialized terminology

---

## Interview Answer

> Prompt engineering modifies inputs to guide the model without retraining, whereas fine-tuning updates model weights to permanently adapt behavior.

---

# 25. AI Agent vs LLM

## LLM

- Generates text
- Stateless by default
- Predicts next tokens

---

## AI Agent

- Uses an LLM for reasoning
- Maintains state
- Plans tasks
- Calls tools
- Executes multi-step workflows
- Can observe results and adapt

---

## Example

### LLM

```
Write an email.
```

### AI Agent

```
Read email

↓

Search CRM

↓

Schedule meeting

↓

Send reply

↓

Update database
```

---

## Comparison

| LLM | AI Agent |
|------|----------|
| Text generation | Goal-oriented execution |
| Single response | Multi-step workflow |
| Limited actions | Uses tools and APIs |
| No planning by itself | Planning and decision making |

---

## Interview Answer

> An LLM is a language generation model, whereas an AI agent combines an LLM with memory, planning, and tool usage to autonomously accomplish complex tasks.

---

# Module Summary

- Generative AI creates new content using learned patterns from large datasets.
- LLMs are Transformer-based models trained through next-token prediction.
- Transformers rely on self-attention, multi-head attention, and positional encoding instead of recurrent networks.
- Tokenization, context windows, temperature, Top-K, Top-P, and Beam Search influence model behavior and output generation.
- Prompt engineering techniques such as zero-shot, few-shot, and Chain-of-Thought improve task performance without retraining.
- Hallucinations can be reduced using grounding techniques such as Retrieval-Augmented Generation (RAG), tool calling, and validation.
- Function calling and tool calling enable LLMs to interact with external systems and perform real-world tasks.
- Fine-tuning changes model weights, while prompt engineering changes only the input.
- AI agents extend LLMs by adding planning, memory, and tool integration to solve multi-step problems autonomously.