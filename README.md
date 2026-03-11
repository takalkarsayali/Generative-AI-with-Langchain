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
- **Cosine similarity** = Most common metric for vector similarity search
- **RLHF** = What separates a raw LLM from a safe, helpful assistant
- **Chain-of-Thought** = Best for complex multi-step reasoning tasks
- **Caching + shorter prompts** = Most effective LLM cost optimization

---

## ✅ Q10 — What is RLHF?

**Interview-Ready Answer:**

RLHF stands for Reinforcement Learning from Human Feedback. It's a training technique used to align LLMs with human values and expectations.

It works in 3 steps:
1. **Supervised Fine-tuning** — The model is first trained on high quality human-written examples
2. **Reward Model Training** — Human evaluators rank multiple model responses. A separate reward model learns to predict what humans prefer
3. **Reinforcement Learning** — The LLM is optimized to generate responses that score high on the reward model

A simple analogy — it's like training a child. When they say something wrong, parents correct them. Over time the child learns what's acceptable. Similarly RLHF teaches LLMs to be helpful, harmless and honest.

This is how models like ChatGPT and Claude are aligned to give safe, accurate and helpful responses.

> 💡 **Key insight:** *"RLHF is what separates a raw LLM from a helpful, safe assistant."*

---

## ✅ Q12 — Zero-shot vs Few-shot Prompting

**Interview-Ready Answer:**

**Zero-shot prompting** is when we give the LLM a task directly with no examples. The model relies entirely on its training to respond.
- Example: *"Summarize this report in 3 bullet points."*

**Few-shot prompting** is when we provide 2-3 examples along with the task so the model understands the expected pattern and format.
- Example: Showing a sample table with Task | Status | Remark columns before asking it to generate one.

| | Zero-shot | Few-shot |
|---|---|---|
| Examples given | None | 2-3 examples |
| Best for | Quick, simple, general tasks | Complex tasks needing specific format |
| Control over output | Low | High |

> 💡 **Key insight:** *"More examples = more control over output format and style."*

---

## ✅ Q13 — What is Chain-of-Thought Prompting?

**Interview-Ready Answer:**

Chain-of-Thought prompting is a technique where we instruct the LLM to think through a problem step by step before giving the final answer. Instead of jumping directly to a conclusion, the model reasons through intermediate steps.

This works because it forces the model to slow down and reason logically, which significantly reduces errors on complex tasks.

**When to use it:**
- Math or logic problems
- Multi-step reasoning tasks
- DSA problem solving
- Any task where the process matters as much as the answer

Example prompt: *"Solve this step by step: If a train travels 60km/h for 2.5 hours, how far does it travel?"*
Instead of just saying 150km, the model shows: 60 × 2.5 = 150km — making it verifiable and trustworthy.

> 💡 **Key insight:** *"CoT trades speed for accuracy — great for complex reasoning tasks."*

---

## ✅ Q16 — What is a Vector Database & Similarity Search?

**Interview-Ready Answer:**

A vector database stores embeddings — numerical representations of text, images or documents. Unlike traditional databases that search by exact keyword match, vector databases search by semantic meaning.

**How similarity search works:**
When a user enters a query, it is converted into an embedding vector. The vector database then compares this query vector against all stored vectors using a similarity metric like **cosine similarity** — which measures the angle between two vectors. Vectors pointing in a similar direction = similar meaning. The closest matching vectors are returned as results.

**Popular vector databases:**
- **FAISS** — Facebook AI Similarity Search (fast, local, used in PolicySphere)
- **Pinecone** — cloud-based, production ready
- **Chroma** — lightweight, great for prototyping

In PolicySphere, I used FAISS to store policy document embeddings and retrieve the most relevant chunks when a user asked a question.

> 💡 **Key insight:** *"Traditional DB = exact match. Vector DB = meaning match."*

---

## ✅ Q17 — RAG vs Fine-tuning

**Interview-Ready Answer:**

RAG and Fine-tuning are two different approaches to customizing LLM behavior for specific use cases.

**RAG** retrieves relevant information from an external knowledge base at query time and passes it as context to the LLM. The model itself doesn't change — only what it knows changes.

**Fine-tuning** further trains the model on domain-specific data, changing its weights. The model itself changes — its tone, style and behavior adapt.

| Factor | RAG | Fine-tuning |
|--------|-----|-------------|
| Data changes frequently | ✅ Best choice | ❌ Needs retraining |
| Private/confidential data | ✅ Secure | ⚠️ Risk |
| Change model tone/style | ❌ Can't do this | ✅ Best choice |
| Cost & speed | ✅ Cheap & fast | ❌ Expensive & slow |
| Source citation needed | ✅ Easy | ❌ Difficult |

I used RAG in PolicySphere because company policies change frequently and needed to be kept private.

> 💡 **Key insight:** *"RAG changes what the model knows. Fine-tuning changes how the model behaves."*

---

## ✅ Q18 — How to Build a Domain-Specific Chatbot?

**Interview-Ready Answer:**

Before building, I'd ask clarifying questions:
- What is the domain and where will it be used?
- Do we have the data ready or do we need to prepare it?
- How frequently does the data get updated?

Based on the answers — if data is private and frequently updated I'd use RAG. If tone/style needs to change I'd consider fine-tuning.

**My approach:**
1. **Data Prep** — Collect & clean documents
2. **Chunking** — Split into smaller pieces using Text Splitter
3. **Embeddings** — Generate vectors and store in FAISS or Pinecone
4. **RAG Pipeline** — Connect Retriever → LLM using LangChain
5. **Prompt Engineering** — Control tone, format, reduce hallucinations
6. **Deployment** — Host on Streamlit or FastAPI

I've done exactly this in my PolicySphere project — a RAG-based policy assistant using FAISS, LangChain and Streamlit.

> 💡 **Key interview tip:** *Asking clarifying questions before answering design questions shows seniority — always do this!*

---

## ✅ Q19 — How to Deploy an LLM in Production?

**Interview-Ready Answer:**

For prototyping I've used Streamlit Community Cloud — push code to GitHub, store credentials securely in secrets, and deploy instantly.

For production, key considerations are:

1. **Hosting** — AWS/GCP using Docker containers for scalability
2. **Security** — Store API keys in environment variables or secret managers (never hardcode)
3. **Monitoring** — Log inputs & outputs, monitor response quality and costs
4. **Cost Optimization** — Cache frequent responses, choose right model size
5. **Scalability** — Load balancing for multiple users, handle rate limits gracefully
6. **Error Handling** — Graceful fallbacks when API calls fail

> 💡 **Key insight:** *"Prototype on Streamlit. Scale on cloud."*

---

## ✅ Q20 — How to Optimize Slow & Expensive LLM Responses?

**Interview-Ready Answer:**

If my LLM application is slow and expensive, I'd optimize in 4 ways:

1. **Model Selection** — Downgrade to a smaller, cheaper model where GPT-4 level intelligence isn't needed
2. **Caching** — Cache responses for frequently asked questions so the LLM isn't called repeatedly for the same query
3. **Prompt Optimization** — Shorten prompts to reduce token count — every token costs money
4. **Retrieval Optimization** — Retrieve fewer, more precise chunks from the vector database to reduce context size passed to the LLM

In PolicySphere, I optimized by retrieving only the top 3 most relevant policy chunks instead of passing entire documents to the LLM — this reduced both cost and response time significantly.

> 💡 **Key insight:** *"Optimization = right model + right prompt + right retrieval + smart caching."*

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
