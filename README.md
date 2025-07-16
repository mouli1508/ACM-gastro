# Retrieval-Augmented Generation Pipeline Using Llama and Knowledge Graphs

This project implements a **Retrieval-Augmented Generation (RAG)** pipeline that combines Llama models with a domain-specific knowledge graph to answer medical queries, with a focus on gastroenterology and hepatology. Designed to run locally, it ensures data privacy and control, making it ideal for sensitive domains like healthcare. By leveraging structured knowledge and unstructured text, this pipeline delivers accurate and comprehensive answers using a smaller, efficient model.

## Table of Contents

- [Architecture and Methodology](#architecture-and-methodology)
- [Knowledge Graph Construction](#knowledge-graph-construction)
- [Algorithmic Design](#algorithmic-design)
- [System Design Considerations](#system-design-considerations)
- [Installation](#installation)
- [Usage](#usage)
- [Dependencies](#dependencies)
- [License](#license)
- [Contact](#contact)

## Architecture and Methodology

The pipeline is built around three core modules:

1. **Query Generation**: Transforms a user query (e.g., "What causes gastric ulcers?") into multiple sub-queries (e.g., "What triggers stomach ulcers?") using a local Llama model to broaden the scope of retrieval.
2. **Knowledge Retrieval**: Fetches information for each sub-query from:
   - A **knowledge graph** (Neo4j) for structured facts (e.g., "NSAIDs CAUSE Peptic_Ulcer").
   - A **vector store** for unstructured text snippets from medical documents.
3. **Answer Generation**: Compiles retrieved data into a single context and uses a local Llama model to synthesize a natural, evidence-based answer.

![Pipeline Architecture](image1.png)  
*Figure 1: Overview of the RAG pipeline architecture.*

## Knowledge Graph Construction

The knowledge graph is derived from over 500 pages of medical documentation provided by experts:

- **Data Processing**: PDFs are converted to text and split into ~667 chunks (~2000 characters each) with overlap to preserve context.
- **LLM-Driven Extraction**: Llama 2 - 70B extracts entities (e.g., "Disease", "Symptom") and relationships (e.g., "CAUSES") using a controlled schema via LangChain’s LLMGraphTransformer.
- **Graph Integration**: Triples are stored in a Neo4j database, enabling efficient querying with full-text indexes.

This structured knowledge base enhances retrieval precision and supports the smaller Llama model.

## Algorithmic Design

The pipeline’s algorithm is outlined as follows:

```plaintext
Input: User query (e.g., "What causes gastric ulcers?")
1. Generate N sub-queries using LLM (e.g., "What triggers stomach ulcers?")
2. For each sub-query:
   a. Query Neo4j knowledge graph for structured facts
   b. Perform vector similarity search on document corpus
3. Compile structured and unstructured results into a single context
4. Use Llama model to generate a consolidated answer
Output: Final answer based on retrieved context
```

This hybrid retrieval approach ensures both breadth and depth in responses.

## System Design Considerations

The system prioritizes local deployment for privacy:

- **Local Hosting**: Llama models and embeddings run on local GPUs.
- **Neo4j Database**: Combines knowledge graph and vector store locally (or on a secure server).
- **Privacy**: No data is sent to external services, ideal for healthcare compliance.
- **Trade-offs**: Uses a smaller model (e.g., Llama 3.2 3B) with rich retrieval to balance performance and resource use.

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/your-repo/rag-pipeline.git
   ```
2. Install dependencies (see [Dependencies](#dependencies)).
3. Set up Neo4j with the provided schema and data.
4. Configure local Llama models and embedding services.

## Usage

Run the pipeline with a query:

```bash
python main.py --query "What causes gastric ulcers?"
```

The system will generate sub-queries, retrieve relevant data, and output a consolidated answer.

## Dependencies

- Python 3.8+
- Neo4j (with vector indexing)
- Llama models (e.g., Llama 2 - 70B, Llama 3.2 3B)
- HuggingFace transformers (for embeddings)
- LangChain
- Local GPU support recommended
