# ECSS Compliance RAG Engine: Hybrid Graph-Vector Architecture
*Transitioning from Flat Text Search to Topologically-Aware AI for Aerospace Standards*

## Professional Disclaimer (NDA)
*To comply with Non-Disclosure Agreements (NDAs) and copyright restrictions regarding the official European Cooperation for Space Standardization (ECSS) corpus, the source code in this repository runs on a **Mock Dataset**.*

*The proprietary industrial datasets and the full compiled databases are strictly confidential and are not included in this repository. However, the architectural logic, the knowledge graph extraction pipeline, and the metrics discussed in this documentation reflect the real-world deployment of the system tested on the complete normative corpus.*

---

## The Cognitive Angle: From Flat Text to Normative Networks

> **Author's Note:** My background in Cognitive Psychology and Neuroscience influenced the design of the retrieval architecture by emphasizing relational rather than purely lexical representations of knowledge. This allowed me to treat complex regulatory frameworks not as isolated paragraphs, but as interconnected normative networks. Just as neural pathways define cognitive functions through their connections, an aerospace standard defines compliance through its structural cross-references. By implementing a Normative Document Graph alongside a Vector Database, I apply a rigorous structural mindset to information retrieval, treating every ECSS requirement as a node in a broader "normative behavior" profile.

## Project overview

This project focuses on resolving the critical limitations of standard Vector-based RAG (Retrieval-Augmented Generation) when applied to rigid aerospace engineering standards (ECSS).

### The industrial challenge:
*   **Semantic ambiguity:** standard vector searches fail when a requirement states *"Testing shall be performed as per §5.3"*, because the actual testing parameters are structurally linked, not semantically similar.
*   **Deontic rigidity:** aerospace compliance relies on the strict categorization of modal verbs (SHALL for mandatory, SHOULD for recommended, MAY for optional). Vector spaces do not inherently differentiate these constraints.
*   **Cross-referencing:** navigating a real-world scenario requires understanding multi-hop dependencies across different engineering domains (e.g., Software Engineering vs. Quality Assurance).

## System architecture

The hybrid pipeline is designed to merge semantic similarity with structural awareness.

### The hybrid retrieval algorithm
1.  **Top-K semantic search (vector space):** querying ChromaDB using Hugging Face Embeddings (`all-mpnet-base-v2`) to retrieve lexically similar chunks.
2.  **Node anchoring (seed nodes):** using the retrieved chunk metadata (Standard/Section) as entry points into the NetworkX graph.
3.  **Topological traversal (graph expansion):** navigating the local ego-graph strictly along `CONTAINS` and `REFERENCES` edges, intentionally filtering out standard-level supernodes to prevent "fan-out" explosions.
4.  **Context assembly:** merging the lexical text with the discovered multi-hop requirements.
5.  **Grounded generation:** passing the enriched context to the `Qwen/Qwen2.5-7B-Instruct` LLM with explicit instructions to trace assertions back to their topological or lexical origin.

## Real-world production metrics

While the live demo uses a mocked subset, the architecture was originally tested and validated on a full-scale ECSS corpus. Below are the actual topology telemetry stats from the production environment:

### Normative graph stats
*   **Total nodes:** 1935
*   **Total edges:** 2098
*   **SHALL requirements:** 1088
*   **SHOULD requirements:** 39

### Node types distribution
*   **Requirement:** 1162
*   **Section:** 743
*   **Term:** 27
*   **Standard:** 3

### Advanced network metrics
*   **Graph density:** `0.0006` *(Indicating a highly specific, sparsely connected normative topology)*
*   **Top normative hubs (Most referenced standards):**
    *   `std:ecss-q-st-80c` (Centrality Score: 0.18)
    *   `std:ecss-e-st-40c` (Centrality Score: 0.16)
    *   `std:ecsse-st-40c_§2.1` (Centrality Score: 0.06)

## Interactive ECSS Graph-RAG demo

Experience the Hybrid Retrieval pipeline in real-time through a dedicated Streamlit application. This dashboard simulates a live compliance verification session, visualizing how both vector and graph contexts are extracted.

### Hybrid decoding and graph traversal simulation
[**Access the Streamlit dashboard**](https://graphrag-5dtyywqq7znkyxvp8vgccd.streamlit.app/)
*   **Real-time simulation:** observe the generation process as the agent processes your query across two branches (Vector + Graph) and synthesizes a compliance report distinguishing SHALL and SHOULD requirements.
*   **Interactive subgraph visualization:** view the local ego-graph generated for your query using the Pyvis visualizer to verify how requirements cross-reference each other.
*   **Explainability trace (Vector vs Graph):** inspect the dedicated comparison tab to see exactly which "Seed Nodes" were triggered by the vector search, and which hidden normative dependencies were successfully "Discovered" via topological expansion.

## Key achievements
*   **Dual-Layer retrieval engine:** merged lexical search with topological traversal to capture hidden normative cross-references entirely missed by flat vector spaces.
*   **Structural metadata injection:** engineered an ingestion pipeline that injects section IDs directly into vector chunks, completely preventing the "orphaned requirements" issue during graph building.
*   **Automated graph extraction:** built a deterministic parser (Regex + NetworkX) capable of extracting nodes and edges directly from vector chunks without manual annotation.
*   **Explainability and grounded retrieval:** replaced absolute "zero hallucination" concepts with strict Grounded Retrieval mechanisms. Integrated UI components to trace every LLM assertion back to the exact ECSS standard, distinguishing between semantic hits and topological discoveries.
*   **Built-in LLMOps:** implemented a lightweight telemetry logger to track latency, retrieved graph nodes, and user queries for continuous system evaluation.

 ## Future roadmap and extensions

To further scale the architecture and enhance its capabilities for enterprise aerospace environments, the following extensions are considered for future releases:

*   **Scalable graph infrastructure:** migration from the in-memory `NetworkX` topology to a dedicated, persistent Graph Database (e.g., **Neo4j** or **Memgraph**). This will allow the ingestion of cross-agency standards (e.g., NASA, MIL-STD, ISO, DO-178C) pushing the node count beyond 100k+ while maintaining low-latency traversal.
*   **Multi-Modal RAG (vision and tables):** aerospace standards heavily rely on decision trees, flowcharts, and compliance tables. Integrating Vision-Language Models (VLMs) and advanced document parsers (like `LlamaParse` or `Unstructured`) will allow the graph to map visual and tabular dependencies alongside textual requirements.
*   **Agentic compliance workflows:** transitioning from a single-prompt hybrid retrieval to a multi-agent architecture (using **LangGraph** or **CrewAI**). A specialized *Retrieval Agent* could navigate the topology, while a *Critique Agent* cross-checks a user's provided System Design Document against the retrieved ECSS baseline to flag compliance gaps.
*   **Automated compliance matrix export:** implementing a downstream pipeline to automatically generate, populate, and export industry-standard Verification Control Documents (VCD) or Requirements Traceability Matrices (RTM) in `.xlsx` or `.csv` formats directly from the chat interface.
*   **Advanced entity extraction (NER):** augmenting the current deterministic Regex parser with a hybrid extraction pipeline using lightweight NLP models (e.g., `spaCy`, `GLiNER`). This will build a richer semantic ontology, allowing the graph to map abstract requirements to specific physical entities (e.g., linking a *SHALL* constraint directly to a *Hardware Component* or *Software Interface*).

## Tech stack
*   **Data engine:** Python, NetworkX, ChromaDB
*   **Machine learning / LLM:** LangChain, Hugging Face Endpoint (`Qwen2.5-7B`), Sentence-Transformers
*   **Visualization and UI:** Streamlit, Pyvis (Interactive Network Graphs)
