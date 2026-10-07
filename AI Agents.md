
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



