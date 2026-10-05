# GenAI

A complete, practical guide to building LLM applications with LangChain. Every concept is implemented as a small, focused, runnable Python file so you can learn one idea at a time.

Part of the AI Engineer series: https://github.com/Itz-Me-Sumit/AI-Engineering

---

## Repository Structure

GenAI/
    llms/
    prompts/
    output_parsers/
    chains/
    memory/
    runnables/
    rag/
        document_loaders/
        text_splitters/
        retrievers/

---

## 1. LLMs

How to connect to and use different language models through LangChain's unified interface.

- Chat models vs. completion models
- Using hosted providers (OpenAI, Anthropic, Google Gemini) and routing providers (OpenRouter)
- Using open-source models (Hugging Face, local models)
- Key parameters: temperature, max tokens, top-p, stop sequences
- Streaming, batching and async invocation
- Switching providers with minimal code change

---

## 2. Prompts

Prompt design is the control layer of any LLM application. This section covers every major prompt pattern.

- Prompt Template: parameterized text prompts with input variables
- Chat Prompt Template: system, human and AI message roles in one prompt
- One-Shot Prompting: a single example to guide output format
- Few-Shot Prompting: multiple examples (including dynamic example selection) for consistent behavior
- Chain of Thought: step-by-step reasoning prompts for complex tasks
- Partial Prompts: pre-filling some variables (like date or user role) and supplying the rest at runtime
- Messages Placeholder: injecting chat history or dynamic message lists into a prompt
- Prompt Composition: combining multiple prompts into reusable pieces
- Prompt Versioning and Reuse: saving and loading prompts from files
- System Prompt Design: role, tone, constraints and output rules
- Prompt Safety: handling prompt injection and guardrail instructions

---

## 3. Output Parsers

LLMs return text. Output parsers turn that text into data your code can trust.

- Structured Output Parser: define response fields with schemas
- JSON Output Parser: get valid JSON back from the model
- CSV Output Parser: comma-separated lists to Python lists
- Pydantic Parser: strongly typed, validated output using Pydantic models
- Output Fixing Parser: automatically repairs malformed output by asking the model to correct itself
- Format instructions: injecting parser instructions into prompts

---

## 4. Chains

Chains connect prompts, models and parsers into repeatable workflows.

- Sequential Chain: output of one step becomes input of the next
- Parallel Chain: run multiple branches at the same time and merge results
- Router / Conditional Chain: choose a path based on the input (for example, route a query to the right expert prompt)
- Custom Chain: build your own reusable chain logic with custom functions

---

(Continued: Memory, Runnables and RAG sections follow)