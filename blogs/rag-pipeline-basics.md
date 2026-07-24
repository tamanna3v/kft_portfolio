# Building RAG Pipelines with .NET

Retrieval-Augmented Generation (RAG) lets a large language model answer
questions grounded in **your** data instead of only what it memorised during
training. This post walks through a practical pipeline you can build with .NET.

![RAG architecture overview](https://images.unsplash.com/photo-1620712943543-bcc4688e7485?auto=format&fit=crop&w=1200&q=70)

## Why RAG?

Out of the box, an LLM has no knowledge of your internal documents, product
catalogue, or customer history. RAG closes that gap by:

1. **Retrieving** the most relevant chunks of your own data
2. **Augmenting** the prompt with those chunks
3. **Generating** an answer that cites real context

## The Pipeline

A minimal RAG pipeline has four moving parts:

| Stage | What it does |
| --- | --- |
| Chunking | Split documents into overlapping passages |
| Embedding | Turn each chunk into a vector |
| Indexing | Store vectors in a vector database |
| Retrieval | Find the top-k chunks for a query |

### 1. Chunking

Keep chunks small enough to stay focused but large enough to hold context —
around 300–500 tokens with a bit of overlap works well.

```csharp
public IEnumerable<string> Chunk(string text, int size = 400, int overlap = 50)
{
    var words = text.Split(' ');
    for (var i = 0; i < words.Length; i += size - overlap)
        yield return string.Join(' ', words.Skip(i).Take(size));
}
```

### 2. Embeddings & Vector Search

Each chunk becomes a vector via an embedding model, then lands in a vector
store such as FAISS, Postgres + pgvector, or a managed service.

> **Tip:** Normalise your vectors and use cosine similarity — it keeps
> retrieval stable across documents of very different lengths.

### 3. Grounded Generation

At query time you embed the question, pull the closest chunks, and stitch them
into the prompt:

```
System: Answer using ONLY the context below. If unsure, say so.
Context: {retrieved_chunks}
Question: {user_question}
```

## Takeaways

- RAG turns a general model into a domain expert **without fine-tuning**
- Good chunking and retrieval matter more than a fancy model
- Always ground the answer and let the model admit when it doesn't know

Happy building! 🚀
