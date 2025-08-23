---
layout: post
title: "Building a Data Analytics Agent using LLMs"
image: /posts/llm-agent.jpg
tags: [Python, LLM, LangChain, PandasAI, Data Analytics]
---

## Project Purpose  

The purpose of this project is to build a **data analytics agent** powered by **LLMs** using **LangChain + Groq API**, **PandasAI**, and **Python**.  
This agent can analyze structured datasets, generate insights, and visualize results automatically from natural language queries.  

## GitHub Repository Link  

The source code for this project can be found at my [GitHub Repository](https://github.com/BaberFaisal/Building-a-Data-Analytics-Agent-using-LLMs).  

---

## Implementation  

In this project, we connect to the Groq LLM API, integrate it with PandasAI for dataframe analysis, and define a custom response handler to display charts.  

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

# Example 1: Ask a question about the dataset
agent.chat("What are the top 5 job titles in terms of average salary?")

# Example 2: Generate a chart
agent.chat("Plot the distribution of salaries by job role")

# Example 3: Count entries
agent.chat("How many unique companies are listed in the dataset?")
{% endhighlight %}
