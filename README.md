# IMPROVE AGENTIC RAG USING LLAMA3

## Introduction

This project will implement the combination ideas of Adaptive RAG, Corrective RAG and Self-RAG into a RAG agent to improve the RAG workflow:

- **Routing:** Adaptive RAG ([paper](https://arxiv.org/abs/2403.14403)). Route questions to different retrieval approaches.
- **Fallback:** Corrective RAG ([paper](https://arxiv.org/pdf/2401.15884.pdf)). Fallback to web search if docs are not relevant to query.
- **Self-correction:** Self-RAG ([paper](https://arxiv.org/abs/2310.11511)). Fix answers w/ hallucinations or don’t address question.

The concept is the following diagram:

![The Concept](langgraph_adaptive_rag.png)

1. Retrieval & Grade Documents:

- For this project, i will just store urls as a document text.
- Then the text will be split into chunk using RecursiveCharacterTextSplitter.
- Then the chunks will be stored in a vector database. Here i will use Chroma for local vector database.
- Create a Retrieval Grader Agent with PromptTemplate to check and test if the question is related to the docs and return JSON score.

2. If the Grade Document score return "yes" (the question is related to the docs), then it will be passed to "Generate Answer" Agent. Else, it will go websearch

3. Generate Answer

- Using PromptTemplate tu create Generate Answer Agent

4. WebSearch

- Using Tavily to go websearch

5. Hallucination Grader

- Create an agent to check if the generated answer is grounded in / supported by a set of facts (hallucinate) or not and return as JSON score.
- If yes, then then generated answer will be passed to Answer Grader Agent.
- If no, then it will loop back to the Generate Answer Agent to generate a new answer.

6. Answer Grader

- Here the function will check if the answer is useful to resolve a question and return JSON score output
- If yes, then it will give the generated output as the final answer
- If no, it will look up at web search for the answer.

## Technology Used

The following modules are used in this project:

- Llama3
- LangChain
- Chroma
- LangGraph
- Tavily

## Getting started

To run this demo project, create an virtual environment and install the src package:

1. Clone the repository:

2. create .env files with the following secret keys:

```bash
LANGCHAIN_TRACING_V2='true'
LANGCHAIN_ENDPOINT='https://api.smith.langchain.com'
LANGCHAIN_API_KEY=<your-api-key>

FIRECRAWL_API_KEY=<your-api-key> [OPTINAL]

TAVILY_API_KEY=<your-api-key>
```

3. Install Dependencies

```bash
pip install -r requirements.txt
```

4. Run the various jupyter notebook

## Challenges

1. The return JSON score of each agent might not be consistent although the PromptTemplate already provide a clear instruction.
2. The time taken to generate final answer by local machine is too long. (note that i just use mac mac1 for this project)

## To Do

1. Convert the notebook into functional component and classes with standard python project structure with logging, config and others.
2. Accept for various other input data format such as pdf and sql.
