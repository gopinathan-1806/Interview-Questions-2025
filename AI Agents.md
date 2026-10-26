
# AI Agents concepts

## LangGraph vs CrewAI vs AutoGen

LangGraph - If you want sequential step of execution in graph way, use LangGraph in that case, it provides easy for human approval
CrewAI - Used for mostly if agent needs special role to perform an action, then crewai should be used
AutoGen - If agent to agent communication is needed, use AutoGen in that case.

<img width="1536" height="1024" alt="LangGraph, CrewAI and AutoGen Compared" src="https://github.com/user-attachments/assets/c3f95b42-19e8-4774-a74a-cafd3ac4c4ac" />


## Components present on Agent Framework

<img width="1536" height="1024" alt="Frameworks Compared_ LangGraph, CrewAI   AutoGen" src="https://github.com/user-attachments/assets/197445fb-8d46-4a8b-9a7c-aac9b5c110c6" />

## Production Grade AI Agent setup looks like

<img width="1536" height="1024" alt="Production-Grade AI Agent Platform Architecture" src="https://github.com/user-attachments/assets/2b222856-e3a6-4bc1-88e6-86a90d64296e" />

## Converting the entire flow into micro service on IKS / EKS

<img width="1536" height="1024" alt="Production AI Agent Platform on Kubernetes" src="https://github.com/user-attachments/assets/88743761-2b15-47a1-8c06-b5a61b1f2e32" />

## Realtime Banking AI Agent deployed in production 
https://www.youtube.com/watch?v=ZIAzZtKWmbI

<img width="1041" height="651" alt="image" src="https://github.com/user-attachments/assets/97694030-a54d-4555-9bfa-d0f27acc8f24" />

## How to handle if user is passing some sensitive information on prompt 
When user is giving the input request, by mistakenly he added mobile number and credit card on that chat, if that value goes to LLM, that's not good, we we'll be validating the user question and mask the sensitive details before sending it to LLM.

<img width="1774" height="887" alt="Secure PII Redaction Flow Infographic" src="https://github.com/user-attachments/assets/c07fa43f-5001-449f-8584-58beac7b1f1d" />

## Diff b/w Structure based vs Character based chunking

Character based will split the 500 or 600 words per chunck
Structure chunk means it will create the chunk for every paragraph / heading, so its easy for getting the proper response.

<img width="3200" height="2634" alt="chunking-character-vs-structure" src="https://github.com/user-attachments/assets/c37b5679-7504-4fed-b5b1-a44cadd56990" />


<img width="1536" height="1024" alt="ChatGPT Image 6 Oct 2026, 22_47_52" src="https://github.com/user-attachments/assets/c4a42cc6-61a4-4b5f-a315-f54b67138931" />

## Production Grade Langchain and RAG concepts (very important) Learn about data ingestion on how new data is being loaded

<img width="1536" height="1024" alt="ChatGPT Image 6 Oct 2026, 23_13_53" src="https://github.com/user-attachments/assets/f255e0b5-7350-41cb-96a2-80fe2c1cc4d8" />


## Model Management and Fallback

In Production use cases, don't trust single model, always have a backup model

                   Model Gateway
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
       GPT-4.1      Azure OpenAI    Bedrock



## Production and Fallback Model Configuration
 
```yaml
production:
provider: azure-openai
model: gpt-4.1
temperature: 0.1
 
fallback:
provider: openai
model: gpt-4.1-mini
```


## CICD Flow of GenAI apps

How the prompts are validated during CI Checks

```yaml
New Prompt
    ↓
Golden Dataset
    ↓
┌──────────────────────────────┐
│ Evaluation                   │
│                              │
│ Answer relevance   ≥ 85%     │
│ Groundedness       ≥ 95%     │
│ Hallucination      ≤ 1%      │
│ PII leakage        = 0       │
│ Safety violations  = 0       │
│ Retrieval quality  ≥ target  │
└──────────────┬───────────────┘
               ↓
        ALL gates pass?
          /          \
        YES           NO
         ↓             ↓
      Deploy        Reject PR
```

CI should check for this score, if anyone of the parameter is failing, build should fail

```yaml
91% satisfaction     → PASS
95% groundedness     → maybe PASS
5% hallucination     → FAIL
```

## How new data generated every day will be passed into Langchain

```yaml
                    S3
             Policy Documents
                    │
             Object Created
                    │
                    ▼
              S3 Event
                    │
                    ▼
                  SQS
             ┌──────┴──────┐
             │             │
          Retry          DLQ
             │             │
             ▼             ▼
       Ingestion Worker   Alert
       Lambda / ECS       Support
             │
             ▼
        Read document
             │
             ▼
        Parse / Extract
             │
             ▼
      Structure Chunking
             │
             ▼
         Embeddings
             │
             ▼
        OpenSearch
       Vector + BM25
```

## How to manage old data vs new data in RAG system

If the old data is getting updated, the status should be marked as 'inactive' and new dataset should be 'active', so during retrievel there won't be an issue.


```yaml
| policy_id | version | effective_date | status |
|---|---:|---|---|
| P123 | v1 | 2023-01-01 | inactive |
| P123 | v2 | 2025-01-01 | inactive |
| P123 | v3 | 2026-01-01 | **active** |
```

## Agent Memory Architecture

Memory Architecture

A production agent might look like:

```yaml
                 Agent
                   │
        ┌──────────┼──────────┐
        ↓          ↓          ↓
 Working       Semantic    Episodic
 Memory        Memory      Memory
        │          │          │
        ↓          ↓          ↓
   Context      Vector DB   Event Store
```

Important:
Memory is not simply "put everything into a vector database."

That's a common misconception.
Different memory types serve different purposes.

## Agent state / memory during the execution

If we're asking an agent to investigate the PDO failure, it will be running multiple steps, each step will recorded and maintained

<img width="1536" height="1024" alt="ChatGPT Image 7 Oct 2026, 22_51_41" src="https://github.com/user-attachments/assets/a4efc60f-b170-4a1d-90b5-6d7b6faa0805" />

## Statement management during workflow execution

<img width="1536" height="1024" alt="ChatGPT Image 8 Oct 2026, 12_54_38" src="https://github.com/user-attachments/assets/86dc4804-1238-4baa-a9fb-d9abcae12ac3" />


## Validating the answer before sending it to the user's

Mostly for FinTech, if you're creating chatbox for the user's, answer's should be validated by various checks because the data is very sensitive.

```yaml

                    ANSWER
                      │
                      ▼
               Claim Extraction
                      │
          ┌───────────┼────────────┐
          ▼           ▼            ▼
       Claim 1      Claim 2      Claim 3
          │           │            │
          ▼           ▼            ▼
      Evidence     Evidence     Evidence
          │           │            │
          ▼           ▼            ▼
      Source       Source       Source
      Quality      Quality      Quality
          │           │            │
          └───────────┼────────────┘
                      ▼
                 Temporal Check
                      │
                      ▼
                Contradiction Check
                      │
                      ▼
                 Grounding Check
                      │
                      ▼
              Confidence / Decision
                 /            \
             PASS              FAIL
               │                │
               ▼                ▼
             User             Rework
```


### Semantic Caching with Redis

Imagine we've built an Amazon customer support chatbot, and hundreds of customers are using it. If many of them ask the same question, each request will be sent to the LLM, and we'll be charged, for example, $0.005 per response. If 100 customers ask the same question, we're unnecessarily paying for 100 identical LLM calls.

To optimize costs and improve response times, we can implement Semantic Caching. Before sending a query to the LLM, the application checks a cache (such as Redis) for a semantically similar question. If a matching answer is found, it is returned directly from the cache without invoking the LLM. If no match exists, the query is sent to the LLM, and the generated response is then stored in Redis for future reuse.

This approach significantly reduces LLM costs, lowers latency, and improves the overall scalability of the chatbot.


<img width="1215" height="690" alt="image" src="https://github.com/user-attachments/assets/019402d8-5b2f-4404-ae4c-d9257a24271f" />

### Vector is cross checked in redis not a string

<img width="1536" height="1024" alt="Healthcare RAG Flow with Redis Cache" src="https://github.com/user-attachments/assets/98aa9fbc-b73e-48ea-963f-333c9e988b49" />


### AI Agent Architecture 

<img width="1154" height="610" alt="image" src="https://github.com/user-attachments/assets/c809da1a-1169-422b-8b5b-22777b2726ad" />

Internet --> LB --> Agent Orchestator

<img width="967" height="629" alt="image" src="https://github.com/user-attachments/assets/cf2a03a1-3e0a-4fb8-b568-47c4d8ffc353" />

## AI Gateway

An AI Gateway is a centralized entry point between your applications and AI models that manages security, routing, rate limiting, usage, and observability for LLM requests.
Think of it as an API Gateway specifically designed for AI workloads.

For example, suppose you build an AI-powered invoice processing application. Your application uses:
- GPT models through Azure OpenAI for invoice extraction.
- Claude through AWS Bedrock for complex reasoning.
- Llama hosted on your infrastructure for sensitive data.
- An embedding model for RAG.
  
Instead of integrating every application directly with every model provider, you route requests through an AI Gateway.

             Users / Clients
                    |
                    v
        +------------------------+
        |   React.js Frontend    |
        +------------------------+
                    |
                    v
        +------------------------+
        |   Backend Application  |
        |   FastAPI / Node.js    |
        +------------------------+
                    |
                    v
        +--------------------------------+
        |          AI GATEWAY            |
        |--------------------------------|
        |  1. Authentication             |
        |  2. Authorization              |
        |  3. Rate Limiting              |
        |  4. Token & Cost Tracking      |
        |  5. Model Routing              |
        |  6. PII / Data Protection      |
        |  7. Logging & Observability     |
        |  8. Retry / Failover           |
        +--------------------------------+
                    |
          +---------+---------+
          |         |         |
          v         v         v
      Azure       AWS       Self-hosted
      OpenAI     Bedrock      Llama
          |         |         |
          +---------+---------+
                    |
                    v
               AI Response


<img width="1536" height="1024" alt="End-to-End new AI Platform Architecture" src="https://github.com/user-attachments/assets/feb32cca-1b3a-4ac0-9cf2-7560c192c9d9" />


### Popular AI Gateway solutions

1. Lite LLM Proxy
2. CloudFare AI Gateway
3. Kong AI Gateway

## Recall@K, Precision@K, MRR, and NDCG@K

These four metrics are extremely important for RAG evaluation

<img width="1536" height="1024" alt="RAG Retrieval Metrics Evaluation Infographic" src="https://github.com/user-attachments/assets/c3cb6fb9-0936-45e4-ba1d-ac51646fd9ca" />

| Metric | Purpose | Formula | Example | Interpretation |
|---|---|---|---|---|
| **Recall@K** | Measures how many relevant documents are retrieved from all relevant documents available. | Relevant documents in top K ÷ Total relevant documents | 4 relevant documents exist. Your retriever finds 3 in the top 5. Recall@5 = 3/4 = **75%** | Higher recall means fewer relevant documents are missed. |
| **Precision@K** | Measures how many retrieved documents are actually relevant. | Relevant documents in top K ÷ K | You retrieve 5 documents, of which 4 are relevant. Precision@5 = 4/5 = **80%** | Higher precision means less irrelevant information enters the LLM context. |
| **MRR** (Mean Reciprocal Rank) | Measures how highly the first relevant document appears in the results. | Average of 1 ÷ Rank of first relevant result across queries | The first relevant document appears at rank 2. Reciprocal rank = 1/2 = **0.50** | Higher MRR means users tend to find the first relevant result sooner. |
| **NDCG@K** (Normalized Discounted Cumulative Gain) | Measures ranking quality, considering both document relevance and position. | DCG@K ÷ Ideal DCG@K | Relevant documents have scores 3, 2, and 1. NDCG compares their actual ranking against the ideal ranking. | Higher NDCG means more relevant documents tend to appear near the top. |

## Faithfulness in RAG

Faithfulness in RAG means checking whether the LLM's answer is supported by the information retrieved from the knowledge base, rather than making up facts.

## PROD setup RAG system

Embeddings are stored in Pinecone and BM25.
Companies typically maintain two searchable indexes for the same document chunks: a vector index in Pinecone and a keyword index in Elasticsearch/OpenSearch or another BM25-capable engine.

             AWS Documentation / PDFs
                       |
                       v
                Ingestion Pipeline
                       |
                       v
              Parse, Clean, Chunk
                       |
                       v
            Canonical Document Chunks
                       |
              +--------+--------+
              |                 |
              v                 v
       Generate Embedding    Extract Text
              |                 |
              v                 v
         PINECONE          BM25 INDEX
       (Vector Index)    (OpenSearch / ES)
              |                 |
              v                 v
       Semantic Search     Keyword Search
              |                 |
              +--------+--------+
                       |
                       v
                Result Fusion (RRF)
                       |
                       v
                    Reranker
                       |
                       v
                       LLM
                       |
                       v
                  Final Answer

<img width="1536" height="1024" alt="Production-Grade RAG Architecture Infographic" src="https://github.com/user-attachments/assets/f6abf479-4fda-4349-8e1d-b9a0eefdceb9" />

## Redis cache

New data is uploaded into Pinecone and Elastic search, but still redis is running with old cache, if user is asking the question it will point to the old cache, how to fix it ?

Answer:- 
Delete the old Redis entry when the runbook is updated.
When the next user asks the question, fetch the latest data from Pinecone/Elasticsearch.
Store the new answer in Redis for future requests.


<img width="749" height="331" alt="image" src="https://github.com/user-attachments/assets/afc1a9b7-3bc3-461f-845c-db981e8a8ced" />

## Agent to Agent Communication

Agent2Agent (A2A) protocol is an open protocol designed to let AI agents built using different frameworks or technologies discover each other, exchange tasks, and communicate their status and results.

<img width="1536" height="1024" alt="Google A2A Travel Planning Infographic" src="https://github.com/user-attachments/assets/e48e791b-9b12-47f4-b7e5-ecfa9ca33a40" />

6 important components on A2A

<img width="758" height="717" alt="image" src="https://github.com/user-attachments/assets/616e49a9-85a5-47e5-81f0-4df314b4ea01" />

## A2A Authentication 

<img width="1536" height="1024" alt="Google A2A Travel Authentication Flow" src="https://github.com/user-attachments/assets/9262bbcc-7658-46eb-b17c-687100800d9c" />

## Production Grade Multi agent 

<img width="1536" height="1024" alt="Production-Grade Multi-Agent Architecture Diagram" src="https://github.com/user-attachments/assets/4d3cf46e-6beb-4d16-acc9-90315c3d2195" />

## Creating Guardrails through script

```yaml
guardrails:

  prompt_injection:
    enabled: true
    block_patterns:
      - "ignore previous instructions"
      - "reveal system prompt"
      - "bypass security"
      - "show hidden data"

  content_safety:
    enabled: true
    block_categories:
      - hate
      - violence
      - self_harm
      - malware

  pii_detection:
    enabled: true
    entities:
      - PERSON
      - EMAIL_ADDRESS
      - PHONE_NUMBER
      - SSN
      - CREDIT_CARD
      - PASSPORT

  hipaa:
    enabled: true
    protected_entities:
      - PATIENT_NAME
      - MRN
      - DOB
      - INSURANCE_ID
      - MEDICAL_RECORD

  output_filter:
    enabled: true
    redact_sensitive_data: true

  rate_limit:
    requests_per_minute: 100
```

## Multi agent setup in AWS

<img width="1536" height="1024" alt="AWS Multi-Agent RAG Architecture" src="https://github.com/user-attachments/assets/2b30ebba-96b5-4928-addc-640231b38c32" />





