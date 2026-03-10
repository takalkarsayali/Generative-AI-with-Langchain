# 🤖 Gen AI Developer — Interview Preparation Notes

> A personal study guide built through mock interview practice.
> Updated progressively as new questions are covered.

---

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

## ✅ Q7 — What is Temperature in Text Generation?

**Interview-Ready Answer:**

Temperature is a hyperparameter ranging from 0 to 2 that controls the randomness and creativity of LLM output by scaling the probability distribution of the next token.

| Range | Type | Best For |
|-------|------|----------|
| **< 0.5 (Low)** | Focused, deterministic, factual | Q&A, code generation, data extraction |
| **0.5–1.0 (Moderate)** | Balanced creativity & accuracy | Chatbots, summarization |
| **> 1.0 (High)** | Creative, random, less coherent | Creative writing, brainstorming |

For example, asking "What is the capital of India?" at temperature 0 will always return "New Delhi", but at temperature 1.5 it might give unexpected responses. In production RAG systems like PolicySphere, I keep temperature low to ensure accurate, grounded answers.

> 💡 **Key insight:** *"Temperature 0 = focused & safe. Temperature 2 = creative & risky."*

---

## ✅ Q8 — What is Top-K and Top-P (Nucleus) Sampling?

**Interview-Ready Answer:**

Top-K and Top-P are sampling techniques that control which tokens the LLM can choose from when generating the next word.

**Top-K** picks from the K most probable tokens only. If K=5 and vocabulary has 50,000 words, the model only considers the top 5 highest probability tokens. It's a rigid, fixed-size approach.

**Top-P (Nucleus Sampling)** picks from tokens whose cumulative probability adds up to P. For example with P=0.9: laptop(40%) + mouse(30%) + keyboard(20%) = 90% ✅ — search stops here, remaining tokens are eliminated.

| | Top-K | Top-P |
|---|---|---|
| Method | Fixed number of tokens | Tokens until probability fills up |
| Flexible? | No | Yes |
| Best when | Vocabulary is consistent | Probabilities vary a lot |

> 💡 **Key insight:** *"Top-K = fixed candidates. Top-P = flexible candidates based on confidence."*

---

## ✅ Q9 — What is Fine-Tuning & When to Use It Over RAG?

**Interview-Ready Answer:**

Fine-tuning is the process of further training a pre-trained model on domain-specific data so it specializes in a particular area. Like an MBBS doctor doing a specialization in gynaecology — the base knowledge stays, but now they're an expert in one domain. It requires labelled data and is cheaper than training from scratch.

| Situation | Use |
|-----------|-----|
| Need to change model's tone, style or behavior | Fine-tuning |
| Domain knowledge is static & won't change often | Fine-tuning |
| Data is private, sensitive or changes frequently | RAG |
| Need to cite sources | RAG |
| Limited compute & budget | RAG |

> 💡 **Key insight:** *"Fine-tuning changes how the model behaves. RAG changes what the model knows."*

---

## 🦜 LangChain — Key Concepts

| Component | Purpose |
|-----------|---------|
| **Document Loader** | Loads data from PDFs, CSVs, websites etc. into the pipeline |
| **Text Splitter** | Chunks documents into smaller pieces for embedding |
| **Vector Store** | Stores embeddings (e.g. FAISS, Chroma, Pinecone) |
| **Retriever** | Searches vector store for relevant chunks based on query |
| **Chain** | A sequence of calls to LLMs and components to complete a task |
| **Agent** | An LLM that can decide which tools to use dynamically |

**Correct RAG Pipeline Order in LangChain:**
```
📄 Document Loader → ✂️ Text Splitter → 🗄️ Vector Store → 🔍 Retriever → 🤖 LLM → 💬 Response
```

> 💡 LangChain is the glue that connects all GenAI components — ready-made building blocks so you don't build from scratch.

---

## 🧠 MCQ Concepts to Remember

- **BERT** = Encoder-only = Classification, sentiment, embeddings (NOT generation)
- **GPT, Claude, LLaMA** = Decoder-only = Text generation, chatbots
- **T5, BART** = Encoder-Decoder = Translation, summarization
- **K=1 or Temperature=0** = Deterministic — same input always gives same output
- **Chunking** = Breaking documents into smaller pieces for embedding & retrieval
- **RAG** = Best for frequently updated or private data
- **Fine-tuning** = Best for changing model tone/style/behavior
- **Top-P** = Preferred over Top-K in most modern LLMs

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

*📅 Last Updated: Session 2 — Q7, Q8, Q9 + LangChain concepts added*
