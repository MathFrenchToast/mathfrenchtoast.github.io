# After code refactoring here comes Prompt refactoring
 
OpenAI new o1 model incorporates a multi-step "reasoning" part of its output generation what was previously done through chain of thought prompting, forcing your prompts to be rewritten. It is time to enforce good practices for prompt management in source code.

In search of efficiency, we are use to incorporate the latest tricks in our prompt, but with the release (in beta) of the  [latest OpenAI o1 model](https://openai.com/index/learning-to-reason-with-llms/), the overhaul will need to be massive.  

As per [OpenAI itself](https://platform.openai.com/docs/guides/reasoning/advice-on-prompting), it is, for this model, counter productive to add chain of thought instructions part of your prompt and maybe even examples:

> These models perform best with straightforward prompts. Some prompt engineering techniques, like few-shot prompting or instructing the model to "think step by step," may not enhance performance and can sometimes hinder it.

Both for future refactoring and for testing prompt quality, it is time to add some levels of abstraction to our prompts, this include:

- add some markup to separate: system, objectives, examples, etc., and the parsing layer comming along,
- use a templating layer, to be able to insert (or not !) the same chunk in many place but to manage it only once, conditionally insert a chunk (e.g. based on current model name or family),
- to make our prompt chunks a first class citizen in our code base, starting by storing them in a dedicated subfolder of `./ressource`.

Be prepared for testing time as *model x prompt* combination may explode.