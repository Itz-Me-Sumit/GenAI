# GenAI

A complete, practical guide to building LLM applications with LangChain. Every concept is implemented as a small, focused, runnable Python file, so you can learn one idea at a time and see exactly how it works.

Part of the AI Engineer series: https://github.com/Itz-Me-Sumit/AI-Engineer

---

## Repository Structure

```
GenAI/
├── data/
│   ├── input/                  # Sample files (txt, csv, pdf, md) used by loaders and RAG demos
│   └── output/                 # Generated outputs
├── src/
│   ├── 01_llms/                # Using different LLM providers
│   ├── 02_prompts/             # Prompt engineering patterns
│   ├── 03_output_parsers/      # Structured and validated outputs
│   ├── 04_chains/              # Multi-step workflows
│   ├── 05_memory/              # Conversation memory strategies
│   ├── 06_runnables/           # LCEL runnable primitives
│   ├── 07_document_loaders/    # RAG: loading data
│   ├── 08_text_splitters/      # RAG: chunking
│   ├── 09_embeddings_vectorstores/  # RAG: embeddings and vector databases
│   ├── 10_retrievers/          # RAG: retrieval strategies
│   ├── 11_tool_calling/        # Tools and tool binding
│   └── 12_projects/            # End-to-end projects
├── utils/                      # Logger, file loader, helpers
├── llm_client.py               # Central LLM client
├── config.json                 # Model and project configuration
├── requirements.txt
└── README.md
```

---

## Getting Started

1. Clone the repository
2. Create and activate a virtual environment
3. Install dependencies: `pip install -r requirements.txt`
4. Copy `.env.example` to `.env` and add your API keys
5. Run any file directly, for example: `python src/02_prompts/few_shot_prompt.py`

---

## 1. LLMs  (`src/01_llms`)

How to connect to and use different language models through LangChain's unified chat model interface. The same application code can be pointed at a different provider with minimal changes.

- `openai_chat_model.py`: GPT models through OpenAI, including basic invocation and response handling
- `anthropic_chat_model.py`: Claude models through Anthropic
- `gemini_chat_model.py`: Google Gemini models
- `local_models.py`: running open-source and local models (Hugging Face / local inference) without a paid API
- `model_parameters.py`: how temperature, max tokens, top-p and stop sequences change model behavior, with side-by-side comparisons

Key takeaways:
- Chat models vs. completion models
- Streaming, batching and async calls
- Choosing a provider by cost, latency and quality

---

## 2. Prompts  (`src/02_prompts`)

Prompt design is the control layer of every LLM application. This section covers each major prompt pattern in its own file.

- `prompt_template.py`: Parameterized text prompts with input variables. The foundation of reusable prompts.
- `chat_prompt_template.py`: Role-based prompts (system, human, AI messages) for chat models.
- `one_shot_prompt.py`: One example inside the prompt to lock the output format and style.
- `few_shot_prompt.py`: Multiple examples to get consistent behavior on classification, formatting and tone tasks.
- `chain_of_thought.py`: Step-by-step reasoning prompts for math, logic and multi-step problems.
- `partial_prompt.py`: Pre-filling some variables (date, user role, language) and supplying the rest at runtime.
- `messages_placeholder.py`: Injecting chat history or any dynamic list of messages into a prompt.

Additional prompt practices covered or referenced:
- System prompt design: role, tone, constraints and output rules
- Prompt composition and reuse across chains
- Prompt injection awareness and guardrail instructions

---

## 3. Output Parsers  (`src/03_output_parsers`)

LLMs return text. Output parsers convert that text into data your code can trust.

- `structured_output.py`: Define the response fields with schemas and get predictable, named outputs.
- `json_output_parser.py`: Get valid JSON back from the model and load it directly as Python dictionaries.
- `csv_output_parser.py`: Comma-separated model output converted into Python lists.
- `pydantic_parser.py`: Strongly typed, validated output using Pydantic models. Invalid data is caught early.
- `output_fixing_parser.py`: Automatically repairs malformed output by sending the error back to the model for correction.

Key takeaways:
- Always inject format instructions into the prompt
- Use Pydantic when downstream code depends on exact types
- Use fixing/retry parsers for production reliability

---

## 4. Chains  (`src/04_chains`)

Chains connect prompts, models and parsers into repeatable workflows.

- `sequential_chain.py`: The output of one step becomes the input of the next (for example: generate a topic, then write an explanation, then summarize it).
- `parallel_chain.py`: Run multiple branches at the same time on the same input and merge the results (for example: notes and quiz generated together).
- `router_chain.py`: Conditional routing. The input is classified first and then sent to the best-suited prompt or model.
- `custom_chain.py`: Build your own chain logic using custom functions and components.

---

## 5. Memory  (`src/05_memory`)

LLMs are stateless. Memory is how a conversation keeps context across turns. Each file implements a different strategy with a different trade-off between accuracy, cost and context-window usage.

- `chat_history.py`: Stores raw message history (human and AI messages) and passes it back into the prompt. The base building block for every other strategy.
- `conversation_buffer_memory.py`: Keeps the complete conversation. Best accuracy, but token usage grows with every turn.
- `conversation_buffer_window.py`: Keeps only the last K interactions. Fixed and predictable cost, but older context is forgotten.
- `conversation_summary_memory.py`: Uses the LLM to summarize older turns into a running summary. Good for long conversations at the cost of extra LLM calls and some detail loss.
- `conversation_token_buffer.py`: Keeps as many recent messages as fit inside a token limit. Gives direct control over the context budget.

How to choose:
- Short chats and demos: buffer
- Strict cost control: window or token buffer
- Long-running assistants: summary

---

## 6. Runnables (LCEL)  (`src/06_runnables`)

Runnables are the building blocks of the LangChain Expression Language. Every prompt, model, parser and retriever is a runnable, and they can all be composed with the same interface (`invoke`, `batch`, `stream`).

- `runnable_sequence.py`: Run steps one after another. This is what the `|` pipe operator builds.
- `runnable_parallel.py`: Run multiple runnables on the same input and return a dictionary of results.
- `runnable_passthrough.py`: Pass the input through unchanged, or add extra keys alongside it. Essential in RAG chains to carry the question next to the retrieved context.
- `runnable_lambda.py`: Wrap any Python function as a runnable so it can sit inside a chain (cleaning, formatting, validation).
- `runnable_branch.py`: If/else logic inside a chain. Different runnables execute based on conditions.

---

## 7. RAG: Document Loaders  (`src/07_document_loaders`)

Retrieval-Augmented Generation starts with getting data in. Loaders convert different sources into LangChain `Document` objects (content plus metadata).

- `text_loader.py`: Load plain text files with encoding handling.
- `csv_loader.py`: Load CSV files where each row becomes a document. Useful for structured records like the employee dataset.
- `pdf_loader.py`: Load PDFs page by page, with page numbers preserved in metadata for source citation.
- `directory_loader.py`: Load an entire folder of mixed files in one call using glob patterns.
- `web_loader.py`: Scrape and load content from web pages.

---

## 8. RAG: Text Splitters  (`src/08_text_splitters`)

LLMs have limited context and embeddings work best on focused text. Splitters break documents into chunks. Chunk size and overlap directly affect retrieval quality.

- `character_splitter.py`: Splits on a single separator with a fixed chunk size and overlap. Simple and fast, but ignores meaning.
- `recursive_splitter.py`: The recommended default. Tries paragraph, then line, then sentence, then word boundaries, so chunks stay semantically coherent while respecting the size limit.
- `markdown_splitter.py`: Splits by headings (`#`, `##`, `###`) and stores the heading hierarchy as metadata. Ideal for documentation and notes.
- `token_splitter.py`: Splits by token count instead of characters. Guarantees chunks fit the model's token limits exactly.

Rule of thumb: start with the recursive splitter, use the markdown splitter for structured docs, and use the token splitter when token budgets are strict.

---

## 9. RAG: Embeddings and Vector Stores  (`src/09_embeddings_vectorstores`)

Chunks are converted to vectors so that meaning can be searched numerically.

- `embeddings.py`: Generating embeddings with different embedding models.
- `faiss_vectorstore.py`: FAISS, a fast local vector index for in-memory and on-disk search.
- `chroma_vectorstore.py`: Chroma, a persistent vector database with metadata filtering.
- `similarity_search.py`: Similarity search, scores and metadata filters on top of the stores above.

---

## 10. RAG: Retrievers  (`src/10_retrievers`)

Retrievers decide which chunks reach the LLM. Better retrieval means better answers.

- `vector_retriever.py`: The baseline. Returns the top-k chunks by embedding similarity.
- `mmr_retriever.py`: Maximal Marginal Relevance. Balances relevance with diversity to avoid near-duplicate chunks.
- `multi_query_retriever.py`: The LLM rewrites the question into several variations, retrieves for each and merges results. Improves recall on vague questions.
- `contextual_compression.py`: Retrieves first, then compresses each chunk down to only the part relevant to the question. Reduces noise and token cost.
- `parent_document_retriever.py`: Searches on small chunks for precision but returns the larger parent document for context.

---

## 11. Tool Calling  (`src/11_tool_calling`)

How LLMs call external functions, which is the foundation for agents.

- `basic_tool.py`: Defining a simple tool and calling it manually.
- `tool_decorator.py`: Creating tools quickly with the `@tool` decorator, with the docstring used as the tool description.
- `structured_tool.py`: Tools with strict input schemas (Pydantic) for reliable arguments.
- `tool_binding.py`: Binding tools to a model so the LLM can decide when and how to call them.

---

## 12. Projects  (`src/12_projects`)

End-to-end applications that combine the concepts above. Projects will be added here.

---

## What Comes Next

- Advanced RAG: hybrid search, re-ranking, evaluation  https://github.com/Itz-Me-Sumit/Advanced-RAG
- Agentic AI: LangGraph, MCP, multi-agent systems  https://github.com/Itz-Me-Sumit/Agentic-AI

---

## Author

Sumit
- GitHub: https://github.com/Itz-Me-Sumit

- LinkedIn: https://www.linkedin.com/in/sumit-kumar-809687360/

If this repository helps you, consider giving it a star.