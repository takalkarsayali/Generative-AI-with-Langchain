# Generative-AI-with-Langchain
# 🤖 Gen AI Developer — Interview Preparation Notes

> A personal study guide built through mock interview practice.
> Updated progressively as new questions are covered.

## ✅ Q1 — What is RAG (Retrieval-Augmented Generation)?

**Interview-Ready Answer:**

RAG stands for Retrieval-Augmented Generation. LLMs are trained on public data and have a knowledge cutoff, so they can't answer questions about private or recent information. Instead of retraining or fine-tuning the entire model — which is expensive and slow — RAG lets us inject relevant information at query time.

When a user asks a question, we first convert it into an embedding, search a vector database for the most semantically similar document chunks, and pass those chunks as context to the LLM. The LLM then generates a response grounded in that retrieved data.

I've implemented this using FAISS as the vector store in my PolicySphere project.

---

## ✅ Q2 — What are Hallucinations in LLMs?

**Interview-Ready Answer:**

Hallucinations are when an LLM generates incorrect or made-up information but presents it confidently as fact. This happens because LLMs don't actually "know" things — they predict the next most likely word based on patterns in training data. So when they lack information, they fill the gap with plausible-sounding but wrong content.

To reduce hallucinations I use several techniques:
- **RAG** — grounding the LLM's response in retrieved real documents
- **Prompt engineering** — explicitly instructing the model to say "I don't know" if unsure
- **Structured outputs** — constraining the format so the model stays focused
- **Source citation** — forcing the model to reference where its answer came from

In my PolicySphere project, I reduced hallucinations by injecting only relevant policy chunks as context and prompting the model to answer strictly from provided documents.

> 💡 **Key insight:** LLMs are *next-word predictors*, not *fact databases* — that's the root cause of hallucinations.

---

## ✅ Q3 — What is Prompt Engineering?

**Interview-Ready Answer:**

Prompt engineering is the practice of crafting instructions given to an LLM to get accurate, structured and relevant outputs. The quality of your prompt directly impacts the quality of the response.

**4 Key Techniques:**

| Technique | Description | Example |
|-----------|-------------|---------|
| **System Prompting** | Set the model's role & behavior upfront | *"You are a policy assistant. Answer only from provided documents."* |
| **Zero-shot Prompting** | Ask directly with no examples | *"Summarize this report in 3 bullet points."* |
| **Few-shot Prompting** | Provide 2-3 examples before asking | Provide sample Q&A pairs before the real question |
| **Chain-of-Thought** | Ask the model to think step by step | *"Solve this step by step..."* |

I used System Prompting in both my PolicySphere and Equity-X projects to control response structure and reduce hallucinations.

---

## ✅ Q4 — What is Tokenization & Embedding?

**Interview-Ready Answer:**

Tokenization and embedding are two fundamental and sequential steps in how LLMs process text — because LLMs can't read raw text, everything must be converted to numbers.

**Tokenization** splits text into smaller units called tokens — words, subwords or characters.
- Example: *"Unbelievable"* → *"un"*, *"believe"*, *"able"*
- This builds the model's vocabulary.

**Embedding** converts each token into a vector — a numerical representation that captures its semantic and contextual meaning.
- Example: The word *"apple"* in *"I ate an apple"* vs *"I work at Apple"* will have different vector values because the context is different — one is a fruit, one is a company.

> 💡 **Key insight:** Same word, different context = different embedding values.

---

## ✅ Q5 — Encoder-Only, Decoder-Only & Encoder-Decoder Models

**Interview-Ready Answer:**

| Type | Purpose | Used For | Example |
|------|---------|----------|---------|
| **Encoder-Only** | Understanding text, reads left & right context | Classification, embeddings, sentiment analysis | BERT |
| **Decoder-Only** | Generating text one token at a time | Chatbots, text generation, code generation | GPT, Claude, LLaMA |
| **Encoder-Decoder** | Understanding input + generating output | Translation, summarization, Q&A | T5, BART |

> 💡 **Simple analogy:** Encoder = Reader 📖 | Decoder = Writer ✍️ | Encoder-Decoder = Read then Write 📖✍️

---

## ✅ Q6 — What is Next-Token Prediction?

**Interview-Ready Answer:**

Next-token prediction is the core mechanism by which decoder models generate text. Given an input, the model assigns a probability to every possible next token in its vocabulary and picks the most likely one. This process repeats — each new token is predicted based on all previous tokens — until the full response is generated.

A simple example is email autocomplete — when I type *"Let's connect"* the system suggests *"when you are free"* because those words are most probable in that context. GPT and Claude work the same way, just at a much larger scale.

> 💡 **Key insight:** *"LLMs don't think — they predict. Every response is built one token at a time using probability."*

---

## 📚 Key Terms Glossary

| Term | Definition |
|------|------------|
| **Retraining** | Training a model completely from scratch on new data |
| **Fine-tuning** | Further training a pre-trained model on specific data to specialize it |
| **Embedding** | Converting text into numerical vectors that capture semantic meaning |
| **Vector Database** | A database that stores embeddings and enables similarity-based search |
| **Semantically** | Based on meaning, not exact word match |
| **FAISS** | Facebook AI Similarity Search — fast library for vector storage and search |
| **Tokenization** | Splitting text into smaller units (tokens) for LLM processing |
| **Hallucination** | When an LLM generates false information confidently |
| **RAG** | Retrieval-Augmented Generation — injecting external knowledge at query time |
| **Temperature** | Controls randomness in LLM output generation |

---

## 🔗 My Projects (Use in Interviews!)

| Project | Key GenAI Concepts Used |
|---------|------------------------|
| **PolicySphere-AI** | RAG, FAISS, embeddings, prompt engineering, hallucination reduction, source citation |
| **Equity-X** | LLM-powered summarization, structured outputs, prompt tuning, API integration |

---

*📅 Last Updated: In progress — updated after each mock interview session*
