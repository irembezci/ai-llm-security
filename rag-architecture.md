# The Architecture of LLM Systems: Understanding RAG Architecture (Part 1)

When modern AI systems are discussed, the focus is often placed solely on the model. Large language models such as GPT, Claude, Llama or Gemini are usually presented as the center of the system. However, LLM applications are not just a single model. For an AI system to produce reliable, up-to-date and useful answers, there must be a broader architecture operating around the model.

One of the most important components of this architecture is an approach called **RAG (Retrieval-Augmented Generation)**. RAG is an architectural pattern that allows a language model to retrieve information from external data sources while generating responses. This enables the model to access not only the knowledge it learned during training, but also up-to-date and domain-specific data.

In this article, we will explore how RAG architecture works in modern LLM systems and how it fits into the overall structure of an LLM application.

🇹🇷 Turkish version: [Read here](https://medium.com/@irembezci/llm-sistemlerinin-mimarisi-bölüm-1-rag-mimarisi-nedir-ve-nasıl-çalışır-c56f3153a625)


# The Fundamental Problem of LLMs

Large language models are trained on massive datasets, but they have two fundamental limitations.

First, the knowledge of the model is limited to its training data. Any information that appears after the training process is unknown to the model.

Second, models typically do not contain organization-specific knowledge. Internal company documentation, technical wikis, support tickets or proprietary data sources are usually not included in the model’s training dataset.

For this reason, many applications require the model to connect to an external knowledge source. **RAG architecture was developed to address this problem.**

# What is RAG?

RAG stands for **Retrieval-Augmented Generation**.

The core idea behind this approach is simple. When a user asks a question, the system first retrieves relevant documents and then provides those documents to the model as context before generating a response.

The process can be illustrated with a simple flow:

```
User Question
↓
Relevant Documents Retrieved
↓
Context Added to Prompt
↓
LLM Generates Answer
```

In this way, the model generates answers not only based on its internal parameters but also by using real documents as context.

![RAG Architecture](images/rag-architecture.png)

# What is the RAG Pipeline and How Does It Work?

The term **pipeline** refers to a sequence of processing steps through which data flows in order to produce a final result. In other words, it is a workflow where the output of one step becomes the input of the next.

In LLM applications, a user’s question is rarely answered in a single step. Instead, the system performs multiple operations such as preparing documents, retrieving relevant information and providing that information to the model as context.

This sequence of operations is known as the **RAG pipeline**.

A modern RAG system typically follows this pipeline:

```
Documents
↓
Chunking
↓
Embeddings
↓
Vector Database
↓
User Query
↓
Similarity Search
↓
Relevant Documents
↓
Prompt Construction
↓
LLM Response
```

Let’s examine each stage of this pipeline.

# 1. Document Ingestion

At the core of a RAG system is a **knowledge base** that contains documents from various sources.

Examples include:

* PDF documents
* Technical wiki pages
* Notion or Confluence content
* Slack messages
* Database records

These documents are not directly passed to the model. Instead, they first need to be processed and prepared.

# 2. Chunking

Because LLMs operate within a limited **context window**, large documents must be split into smaller pieces.

This process is called **chunking**.

For example, a large document may be divided as follows:

```
Document
↓
Paragraphs
↓
~500 token chunks
```

Each chunk becomes a separate unit that can later be retrieved during the search process.

# 3. Embeddings

Document chunks are then converted into **embeddings**, which are numerical representations of text.

An embedding is a vector that captures the semantic meaning of the text.

Example:

```
"reset VPN password"
↓
[0.23, -0.71, 0.42, ...]
```

These vectors allow the system to compare texts based on **semantic similarity** rather than simple keyword matching.

# 4. Vector Database

The generated embedding vectors are stored in a **vector database**.

Vector databases are designed to efficiently perform similarity searches among high-dimensional vectors. In other words, they are optimized to quickly find vectors that are semantically similar to a given query.

Common vector database systems include:

* Pinecone
* Weaviate
* Qdrant
* Chroma
* FAISS

These systems enable fast semantic searches across millions of document chunks.

# 5. User Query

When a user asks a question, the system performs the same embedding process on the query.

Example:

```
User Question
How do I reset VPN?
```

The query is converted into a vector representation.

# 6. Similarity Search

Using the query embedding, the system searches the vector database to find similar vectors.

This process is commonly known as **top-k similarity search**, where the system retrieves the most relevant results.

Example results:

```
1. VPN Password Reset Guide
2. Network Access Policy
3. VPN Troubleshooting Document
```

These retrieved document chunks will be used as context for the model.

# 7. Prompt Construction

The retrieved documents are incorporated into the prompt sent to the model.

Example prompt:

```
System:
You are a company assistant.

Context:
Document 1: VPN reset instructions
Document 2: Network security policy

User:
How do I reset VPN?
```

At this stage, the model has access to the relevant documents and can use them to generate an informed answer.

# 8. LLM Response

The constructed prompt is sent to the model, which generates a response based on the provided context.

Example:

```
To reset your VPN password, follow these steps...
```

In this way, the response is grounded in actual documents rather than purely probabilistic generation.

# Why RAG Architecture Matters

RAG architecture has become a fundamental component of many modern LLM applications. Company AI assistants, document search systems and research tools often rely on this architecture.

However, RAG is more than just a retrieval mechanism. It plays a critical role in shaping the architecture of modern AI systems. The retrieval layer acts as a bridge between external data sources and the language model.

Understanding RAG architecture is therefore one of the first steps toward understanding how modern LLM applications operate.

# Next in the Series

This article is part of a series exploring modern LLM system architectures.

Upcoming topics include:

* AI Agents and tool-using LLM systems
* LLM application architectures
* Attack surfaces in RAG systems
* Prompt injection and retrieval manipulation

# References

Lewis, P. et al. (2020)
Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks
https://arxiv.org/abs/2005.11401

Andreessen Horowitz (a16z)
Emerging Architectures for LLM Applications
https://a16z.com/emerging-architectures-for-llm-applications/

Pinecone
What is Retrieval-Augmented Generation
https://www.pinecone.io/learn/retrieval-augmented-generation/

LangChain Documentation
https://python.langchain.com/docs/use_cases/question_answering/

Qdrant Documentation
https://qdrant.tech/documentation/

Weaviate Documentation
https://weaviate.io/developers/weaviate
