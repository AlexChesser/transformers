## Trigger architectural research

@01-research-prompt.md 

## Generate features overview

using the @02-architectural-research.md as a start point - try to create a human readable document that details the primary use cases within the hugging face repository. It looks like this application is able to run training, if it can also run inference we should list that as a thing it can do. 

Each of the major areas of application functionality should be called out as a feature in a new document called 03-features-overview.md.   Our goal is to understand the business domains from this application from behind the lens of domain-driven-design.

Read the @README.md as well as scanning the docs to provide an overview of the important functions of this applicaiton.  

## Add bounded contexts and stakeholders to features overview

within @03-features-overview.mdadd a section where we Identify bounded contexts by stakeholder and lifecycle phase (e.g., “Model Lifecycle,” “Inference & Serving,” “Training & Evaluation”).

If we need to identify stakeholders first do that

## Map dependencies per bounded context

within @03-features-overview.md add Map dependencies/extras required per bounded context (e.g., bitsandbytes for quantized inference; Accelerate for distributed training).

## Define ubiquitous language

within @03-features-overview.md Define ubiquitous language (e.g., “pipeline,” “generation config,” “auto classes,” “processor”) to reduce ambiguity across teams.

## Propose onboarding docs and diagram scope

are there any other high value onboarding documents that should be created that should be in place for a new developer to familiarize themselves with the function and purpose of hugging face as a product?

Some might suggest sequence and c4 diagrams? Do you think they're a good idea? What scope would we create those in? per feature perhaps?

## Create prompts folder and per-recommendation prompts

create a folder inside .devcontext/architecture called "prompts" where we create one prompt per recommendation in the previous response. 

Prompts should be optimized to run in parallel within this repo in their own standalone llm context, and should be written in a way where they accept the previously generated "linked-files" (like for example @02-architectural-research.md  and @03-features-overview.md ). and each other isolated prompt should also include recommendations and links to important files across the broader repo.  So for example if athere is a readme specific to imference and serving, or key files, those should be linked within the appropriate prompt. 

## Capture conversation

create a document within .devcontext/architecture called 04-additonal-research-prompts.md where you populate just the prompts that I have delivered across this conversation.

I do not need any of the outputs from those prompts but I need to keep track of all the instructions that I've given the LLM in order to get the output into the state it is currently in.

Each prompt across this conversation should have an H2 header title that explains what I've done in one line

for example:
<copy-to-markdown>
```
## Generate overview 

using the @02-architectural-research.md as a start point - try to create a human readable document that details the primary use cases within the hugging face repository. It looks like this application is able to run training, if it can also run inference we should list that as a thing it can do. 

Each of the major areas of application functionality should be called out as a feature in a new document called 03-features-overview.md.   Our goal is to understand the business domains from this application from behind the lens of domain-driven-design.

Read the @README.md as well as scanning the docs to provide an overview of the important functions of this applicaiton.
```
</copy-to-markdown>

do not execute the instructions within the <copy-to-markdown> document, but instead use that as an example of what to put in the document you generate.