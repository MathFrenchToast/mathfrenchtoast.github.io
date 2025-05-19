# **Feedback on Hugging Face Agent Course**

I have completed the [Hugging Face AI Agents Course](https://huggingface.co/learn/agents-course/unit0/introduction). The subtitle was promising: "This free course will take you on a journey, **from beginner to expert**, in understanding, using, and building AI agents." The course was announced and began delivering the first modules in February 2025, at a relatively manageable pace: one new module every two weeks, for a total of four, including the final project that leads to certification.

***TL;DR:** Even though the course materials are interesting, you won't become an expert simply by completing the four units, even if you go through all the bonus material. Nevertheless, even if not in-depth, comprehensive, or consistently challenging, the information presented in the course is valuable, especially if you already use some Gen AI API but have never tried an AI Agents framework. The course is very practical, and you do build AI agents along the way.*  
*For me, the bonus modules and the final project, required for earning certification, were the most enjoyable, demanding, and valuable parts of this course. More on the final unit later.*

## **Course Description**

The course is divided into four units:

* Unit 1 is a general introduction to agents.  
* Unit 2 is composed of three submodules, each presenting an agent framework and providing hands-on exercises. The frameworks selected are SmolAgent (from HF), LlamaIndex, and LangGraph (from LangChain).  
* Unit 3 focuses on agentic RAG use cases.  
* Unit 4 is the final project, where you need to create an agent that performs well on the GAIA benchmark.

In addition, there is bonus material that I believe should be completed, as it addresses real project needs:

* How to fine-tune a model for function calling.  
* How to instrument your agent code for good observability and evaluation.

Each unit ends with a test to validate your newly gained knowledge.

### **Module 1**

This introduction to agents is straightforward and can be summarized as: "An agent is an LLM with **tools**."  
Or, to be a little more comprehensive: "An agent is an LLM that creates a plan and then uses its knowledge and tools to answer your request by executing that plan."  
The **ReAct** approach is discussed. ReAct stands for Reasoning-Acting, which combines Chain-of-Thought (to create a plan) and tool usage. After calling a tool, the output is parsed and checked to iterate and/or continue to the next step of the plan. So, the ReAct framework is a plan-then-act cycle with a feedback loop. It is at the core of nearly all agent frameworks.  
If you want to have more than one agent to divide the responsibility and code, you can create a **multi-agent system**, where a top agent (the manager) can delegate some of the steps to more specialized agents. This is the classical separation of concerns that developers know well. In the case of agents, there is an additional motivation for such code separation: to avoid consuming too many tokens by having only one large memory of all the steps and outputs, and to avoid creating an agent with too many tools, as that is counterproductive.

### 

### **Module 2 & 3**

In Module 2, you will discover three frameworks: SmolAgent (from HF), LlamaIndex, and LangGraph (from LangChain). For each, you will be introduced to the key features and most suitable use cases.

* SmolAgent for small and prototyping code-oriented agents.  
* LlamaIndex for tool reuse and hand-made workflows.  
* LangGraph for leveraging the power of the LangChain ecosystem.

In Module 3, you will focus on merging RAG with AI agents, both through tools and dedicated agents, leveraging one of the frameworks discovered previously.

### 

### **Bonus Units**

Bonus Unit 1 covers how to fine-tune a model for function calling. This is interesting and explains how, by using dedicated tokens, one can take a model without tool support and make it tool-ready using the LoRA (Low-Rank Adaptation of Large Language Models) technique. Here, as often in this course, after introducing the concepts, you'll have a running notebook at the end of the module. If you take the time to examine the materials provided, you'll discover a cool technique: how to create a more complete training dataset by using a stronger model and how to change the default tokenizer.

Bonus Unit 2 addresses the problem of agent observability. This is a real in-production issue, and I am glad it was covered here. Offline and online analysis is covered, as well as various metrics: cost, latency, and topics like evaluation with user feedback and LLM-as-judge. I discovered open-source tools for agent telemetry (Langfuse and, later on in the SmolAgent documentation, Phoenix from Arize AI).

## **The Final Exam**

Module 4 is the final exam, required if you want to earn the "Certificate of Excellence." It is a funny (in my opinion) assignment.

It is based on a subset of level 1 [GAIA Benchmark](https://huggingface.co/papers/2311.12983) questions. This benchmark was created to test AI reasoning capabilities by posing questions that are easy (or at least doable) for humans but considered hard for AI, such as double negation, listening to and understanding short video or audio content, or requiring more than one tool.

HF has isolated 20 questions from level 1 (there are three levels of difficulty), and you must code an AI agent and perhaps tools to correctly answer at least six of them (i.e., a 30% rank).  
That was a fun and challenging coding experience that made you use your newly gained knowledge. I added some more constraints for myself, such as using local tools and consuming as few tokens as possible. I will discuss this in a second article.

## **Time to Complete**

This was the first session of this course, and the modules were published roughly every two weeks, which gave you enough time to complete each of them by spending one to four hours on each, depending on your proficiency and coding experience.  
A Discord server, managed by people from HF, was active during the whole session (which was planned to last from February to the end of May) and should remain open for newcomers, as should [the course GitHub repository](https://github.com/huggingface/agents-course).

## **Do I Recommend It?**

**Yes**, even if you should be prepared to do some extra work on external resources to truly master your understanding of the subject, this is more than just a "brief introduction."  
The HF team has done a good job laying out all the modules and materials, and thanks to the free HF resources available, it provides a truly hands-on experience.

I would have liked a broader view of the framework landscape and would like to see the material extended in the future to include topics like MCP and A2A, but no doubt this is something that will evolve with the field.

Congrats to all the certified learners and to the HF team for this course and all the other freely available courses. Go check them out: [https://huggingface.co/learn](https://huggingface.co/learn)