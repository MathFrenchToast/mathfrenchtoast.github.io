# How LLMs Think Twice Before They Speak
subtitle: From LLM to LLM-Based Systems, trends for 2025

The end of 2024 has seen the rise of simulated reasoning (SR). From OpenAI o1 and then o3 to open-source versions based on llama and deepseek v3, SR models now have the highest score in "reasoning" benchmarks.

Hereafter some insight on how those new models work and what it tells us about LLMs in 2025.

## What are the "Thinking" Models
On the never-ending quest to make LLMs produce more accurate (and valid) answers, the traditional methods reached a plateau:
- More parameters or more/better training data leads to an increase in training costs.
- Prompt engineering techniques (like CoT or Few-Shot Examples) are not scalable and maintainable.

In the SR model, the strategy is to produce more than one answer, check the validity of each of the proposals, and only output the best one.

As this is comparable to a human "system 2" conscious reasoning, that is weighing the validity of an utterance before continuing, this was named Simulated Reasoning.

## Anatomy of a Simulated Reasoning Model
Various techniques have been proposed, and even if some are yet undisclosed (we still have no information on OpenAI o1 and o3 structure or training), we can say that they are based on a pipeline where the LLM is only one building block.

![anatomy of sr model](https://miro.medium.com/v2/resize:fit:4800/format:webp/1*6ACnFo5y76SC-V867LqWvQ.png) 
Schema from: [Hugging Face paper](https://huggingface.co/spaces/HuggingFaceH4/blogpost-scaling-test-time-compute).

What is noticeable is that the LLM is now only a sub-component of the "problem-to-answer" pipeline, along with the 'Search Strategy' and the 'PRM'. The PRM in the model stands for: Process Reward Model, it is the brick that will score the candidate answer.

By using this architecture, a team at Hugging Face managed, by selecting the right PRM and search strategy, to beat a 70B parameter model with a 3B parameter model only (note: the PRM used is itself an 8B LLM).

Among the rating and search strategies they tested, we can quickly list:
- majority voting: produce x proposals, and output the most common answer
- best-of-n: score each proposal and output the best
- weighted best-of-n: combine the two above by considering only the identical proposals

Finally, the best results were achieved by forcing a step-by-step reasoning and using the weighted best-of-n selection mechanism at each step of the "reasoning tree".
Doing so each step is checked and a better output is produced, even if it is at the price of more compute time.
## What it tells for the future of LLM in 2025

There are limitations in the current works, mainly based on the fact that scoring can be hard to produce. But in many scientific and technical cases, this can be done. Let's just think about better software development assistants and better math and science solvers. I foresee that this will be extended to other spaces.

**Pipelining** is not new in LLMs; one can think, for example, of pre- or post-processing safeguard steps as pipelining. But this combination of search exploration, with the agentic trend, means that LLMs will no longer be used alone, but inside an engineered pipeline.
This is also true with image processing, one can use a full workflow and control steps using [ComfyUI](https://www.comfy.org/) and [ControlLoRA](https://github.com/HighCWu/ControlLoRA) for example.

**Exploring a search space** is an old topic of Operational Research, think about the exploration of all chess combinations, and find the optimal next move. There are a lot of "traditional AI" techniques that can be tested for even better results. We may see in the future, a comeback of some Symbolic AI techniques like ["Truth Maintenance system"](http://dekleer.org/Publications/Problem%20Solving%20with%20the%20ATMS.pdf). This is part of the hybrid AI search field (mixing connectionist - NNet - and symbolic).

**SLM** or Small Language Model will rise in terms of usage. A 3B parameter model can run inside your browser or your smartphone. If it is as capable as a larger model, this implies more embedded and offline applications.

**Multiagent systems** is also an old subject and a "traditional AI" technique for problem solving, see for example Michael Wooldridge's 1995 paper: 'Intelligent agents: theory and practice'.

**In 2025, AI will migrate from model to model-based systems**, with an increase in the need for competencies in AI engineering. More complex tools are going to emerge, and the traditional ChatGPT interface will no longer be the most used interaction UI.

## Additional References

["Solving math word problems with process-and outcome-based feedback"](https://arxiv.org/pdf/2211.14275), DeepMind, 2022

["Control Lora and ComfyUI at Hugging Face"](https://huggingface.co/stabilityai/control-lora), Hugging Face

["OpenAI o3, arc-agi"](https://arcprize.org/blog/oai-o3-pub-breakthrough), François Chollet

about System 1 and System 2, ["Thinking, Fast and Slow"](https://en.wikipedia.org/wiki/Thinking,_Fast_and_Slow), Daniel Kahneman, 2011

Michael Wooldridge, An Introduction to Multiagent Systems, John Wiley & Sons Publisher, 2002
Michael Wooldridge 'Intelligent agents: theory and practice', Knowledge Engineering Review, 1995
