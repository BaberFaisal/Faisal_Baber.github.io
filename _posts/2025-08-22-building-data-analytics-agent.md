---
layout: post
title: "Building a Data Analytics Agent using LLMs"
image: /posts/llm-agent.jpg
tags: [Python, LLM, LangChain, PandasAI, Data Analytics]
---

## Project Purpose  

The purpose of this project is to build a **data analytics agent** powered by **LLMs** using **LangChain and Groq API**, **PandasAI**, and **Python**.  
This agent can analyze structured datasets, generate insights, and visualize results automatically from natural language queries.  

## GitHub Repository Link  

The source code for this project can be found at my [GitHub Repository](https://github.com/BaberFaisal/Building-a-Data-Analytics-Agent-using-LLMs)

---

## Implementation  

In this project, I connect to the Groq LLM API, integrate it with PandasAI for dataframe analysis, and define a custom response handler to display charts.  

## Python Code

{% highlight python %}
# Install required libraries
pip install --upgrade numpy
pip install --upgrade pandas
pip install langchain_groq --quiet
pip install pandasai --quiet

# If needed, force reinstall
pip uninstall -y numpy pandas
pip install numpy pandas

# Load dataset
import pandas as pd
import warnings
warnings.filterwarnings("ignore", category=FutureWarning)

data = pd.read_csv("/content/ai_job_dataset1.csv")
data.head()

# Connect to Groq API
import os
from langchain_groq.chat_models import ChatGroq
from google.colab import userdata

os.environ["GROQ_API_KEY"] = userdata.get("key")
llm = ChatGroq(model_name="qwen/qwen3-32b")

# Custom Response Handler for PandasAI
from pandasai.responses.response_parser import ResponseParser
from IPython.display import Image, display

class PandasDataFrame(ResponseParser):
    def format_plot(self, result):
        import os, matplotlib.pyplot as plt
        os.makedirs("/content/export", exist_ok=True)
        filename = "/content/export/chart.png"
        try:
            if hasattr(result, "savefig"):
                result.savefig(filename)
                plt.close(result)
            else:
                plt.savefig(filename)
                plt.close()
            display(Image(filename))
        except Exception as e:
            print("Error generating plot:", e)

# Build the PandasAI agent
from pandasai import SmartDataframe

agent = SmartDataframe(
    data,
    config={
        "llm": llm,
        "response_parser": PandasDataFrame()
    }
)
{% endhighlight %}

### Example 1: Ask a question about average salary
{% highlight python %}
agent.chat("Show average salary grouped by education level as a table")
{% endhighlight %}
![by_education](/img/posts/by_education.png) 
### Example 2: List top 10 job
{% highlight python %}
agent.chat("List top 10 job titles that have the highest average salaries")
{% endhighlight %}
![top_10](/img/posts/top_10.png) 
### Example 3: Ask a question about best paying companies
{% highlight python %}
agent.chat("show the best paying companies in table form")
{% endhighlight %}
![best_paying](/img/posts/best_paying.png) 
### Example 4: List top 5 job titles
{% highlight python %}
agent.chat("What are the top 5 job titles with the highest salaries?")
{% endhighlight %}
![top_5](/img/posts/top_5.png) 

## Concept Overview  

### Large Language Model (LLM)  
A probabilistic sequence model trained on massive text corpora to predict tokens.  

**Key traits:**  
1. **Context window** – limits how much you can pass in a single turn.  
2. **Tokenization** – splits text into subword units.  
3. **In-context learning** – lets the model adapt from examples in the prompt without parameter updates.  
4. **Function/tool calling** – exposes a schema so the model can request external actions rather than free-form text.  



### Prompt Engineering  
The craft of steering LLM behavior through inputs.  

**Core pieces:**  
- **System prompts** (role + guardrails)  
- **Few-shot exemplars**  
- **Structured output instructions** (e.g., JSON schema)  
- **Delimiters** to prevent prompt injection  
- **Stop sequences** to control generation boundaries  



### Agents  
LLM-driven controllers that decide, plan, and act by calling tools iteratively.  

**Typical loop:**  
→ Perceive user goal  
→ Select tool (**planner**)  
→ Call tool (**executor**)  
→ Reflect on result  
→ Continue or stop  

**Patterns include:**  
- **ReAct** (reason + act)  
- **Plan-and-execute**  
- **Multi-agent roles** (planner, researcher, analyst, reviewer)  



### Tools (Function Calling)  
Structured, declarative interfaces the agent can invoke (e.g., `run_sql`, `load_dataframe`, `plot`).  

**Tool spec includes:**  
- Name  
- Description  
- Parameters (types/constraints)  

The LLM outputs a call with arguments; your runtime validates, executes, and returns results back into the loop.  



### Retrieval-Augmented Generation (RAG)  
A pattern to ground the LLM in your data:  
1. Chunk sources  
2. Create embeddings  
3. Store in a vector index  
4. Retrieve top-k by similarity/hybrid search  
5. Inject retrieved context into the prompt to reduce hallucinations and add citations  

**Variants:**  
- **Hybrid search** (BM25 + vectors)  
- **Reranking** (cross-encoders)  
- **Query re-writing**  



### Embeddings  
Fixed-length vector representations of text, tables, or metadata used for similarity search.  

**Considerations:**  
- Dimension  
- Metric (cosine, dot, L2)  
- Normalization  
- Domain drift (re-index when data or embedding model changes)  



### Vector Databases / Indexes  
Stores and searches embeddings with ANN structures like **HNSW**, **IVF**, or **PQ** to trade accuracy vs. latency.  

**Typical features:**  
- Filters over metadata  
- Namespaces  
- Upserts  
- TTL for freshness  



### Chunking & Context Packing  
Breaking documents into retrieval-friendly units.  

**Strategies:**  
- Fixed window + overlap  
- Recursive by structure (headings/paragraphs)  
- Semantic chunking  

**Goal:** maximize answer coverage while minimizing prompt bloat; pack with windowed overlaps and rank-aware merging.  



### Reranking  
Post-retrieval reordering using a stronger model (often a cross-encoder) to boost context precision.  

- Costs more latency  
- Raises the chance that injected passages truly support the answer  










































