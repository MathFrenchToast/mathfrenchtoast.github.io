## **My two days AI Agent Showdown with GAIA \- Earning the Hugging Face Agents course certification**

In the first part of this series, I gave you an overview of the Hugging Face AI Agents Course. Now, let's talk about the final project, which leads to certification.

### 

### **The GAIA Gauntlet**

The final exam involves a subset of questions from the [GAIA Benchmark](https://huggingface.co/papers/2311.12983).  

GAIA is designed to test an AI's reasoning abilities. It uses questions that are easy for humans but difficult for AI, such as those with double negatives, or those requiring understanding of short video or audio content. Some questions also need more than one tool to answer.

To give you an idea, here's what it looks like:  
![https://huggingface.co/datasets/agents-course/course-images/resolve/main/en/unit4/gaia\_levels.png](https://huggingface.co/datasets/agents-course/course-images/resolve/main/en/unit4/gaia_levels.png)  

Hugging Face selected 20 questions from GAIA's level 1 (there are three levels of difficulty). Our task was to build an agent (or a group of agents) to answer at least six of them, which is a 30% score.

### **Day 1: Multi-Agent Madness**

As someone with a developer background, I like to organize my code. So, I decided to use a multi-agent system, with a "manager" agent assigning tasks to more specialized sub-agents. I chose the SmolAgent framework because it is well-suited for prototyping, code-first development, and is easy to handle.

That's when things got interesting. I soon found out that the difference between an agent and a tool can be subtle. In my effort to create a structured system, I may have over-complicated things. I ended up with a complex setup of agents, each with its own set of tools.

Hopefully, I was able to handle debugging using a local observability stack with Phoenix and SmolAgent\[telemetry\].

### **Day 2: Local is Lovely**

I prefer to minimize token consumption (to reduce costs) and maximize privacy and efficiency. So, I used a five-year-old laptop (with an NVIDIA RTX 2060\) and pushed it to its (VRAM) limits. To be precise, this gaming laptop was used for remote hosting a VLLM instance, accessible through the network, to replace calls to larger LLM APIs when possible. 

Here's what I used:

* **VLLM for Local Inference:** I set up VLLM for local inference. It took some work to optimize, but the results were worth it.  
* **Local Speech-to-Text (in Docker):** I used go-whisper (wrapper around whisper.cpp ) for a local speech-to-text system. This allowed me to process audio without sending data to external services.  
* **Local Search Engine (in Docker):** I also deployed a local search engine using SearxNG in Docker to enable my agent to go search for additional information on the internet.  
* **Custom Tools:** I developed several custom tools, including one for extracting the main content from web pages and another for getting transcripts from YouTube videos. You can find more details in the [project's README](https://huggingface.co/spaces/MathFrenchToast/Final_Assignment_Template/blob/main/README.md) and the [code](https://huggingface.co/spaces/MathFrenchToast/Final_Assignment_Template/tree/main).


### **The Perils of Perfection**

In the end, I achieved a 55% score, which I was happy with. But I learned that there's a balance to be found with AI agents.  

Because AI agents are not deterministic, having tools that are *too* focused can be like overfitting. This means the agent might do well on specific questions but not on others. On the other hand, having *too many* tools can lead to suboptimal solutions, as the agent has trouble choosing the best one (depending on your LLM model, between 10 and 50 tools is a recommended maximum).

### 

### **Conclusion: Embrace the Agent Life**

Despite the challenges, and two 7-hour coding sessions, this project was very enjoyable. It allowed me to use what I had learned and explore the possibilities of AI agents. I'll definitely go for more AI agent coding in the future.

If you're thinking about taking the Hugging Face AI Agents Course, be prepared for a challenging but rewarding experience. Remember that the goal is not just to get the certification. It's also about the learning process and the satisfaction of seeing your agents work.

My HF space code: [https://huggingface.co/spaces/MathFrenchToast/Final\_Assignment\_Template/tree/main](https://huggingface.co/spaces/MathFrenchToast/Final_Assignment_Template/tree/main)