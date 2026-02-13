---
# TODO: Override title for URL slug.
title: 'How My Team Aligns on Prompting for Production'
date: '2026-02-13T18:38:27Z'
publishDate: '2026-02-13T18:38:27Z'
# Description is used as a page subtitle & search index.
description: "Develop prompts embedded in your codebase with rigor. Don't go alone!"
slug: 'prompting-for-production'

categories:
  - POST
tags:
  - llm
  - engineering
  - collaboration
  - automation
  - code reviews

draft: false # hide from production deployment
hidden: false # hide from lists if true
rssFullContent: true

# Specify an external canonical URL.
# If both are present, will be linked from footer of post.
# canonical: http://example.com
# canonicalTitle: Example Article Title

#context: | 
#  TODO if I'm keeping the context block for this post.

image: cover.jpg

imageCredit: |
  Photo by <a href="https://unsplash.com/@jeton7?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Jeton Bajrami</a> on <a href="https://unsplash.com/photos/a-group-of-people-rowing-a-boat-in-the-water-d4e2mitxgsE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

  
---

My team at Google is [automating sample code generation and maintenance](https://cloud.google.com/blog/topics/developers-practitioners/7-technical-takeaways-from-using-gemini-to-generate-code-samples-at-scale). Part of that is using Generative AI to produce and assess instructional code. This introduces a challenge: How do we trust the system to meet our specific standards, when core components are non-deterministic?

Establishing trust requires isolating and understanding each [large language model (LLM)](https://cloud.google.com/ai/llms) request. We need to know exactly what goes into the model, and a guarantee of what comes out.

This challenge isn't different from other feature development. To succeed, we realized we had to stop treating prompting like chatting or guessing and start treating it like coding.

## Natural Language is a Fuzzy Programming Language

Prompting an LLM is effectively "[natural language programming](https://ai.google.dev/gemini-api/docs/prompting-strategies)": we are programming in English. The problem is that English is not the greatest language for programming. It is ambiguous. It is subjective. It is open to interpretation.

In C++, a missing semicolon breaks the build. In English, a missing comma changes the objective entirely: [*"I don't know, John"* becomes *"I don't know John"*](https://en.wikipedia.org/wiki/Vocative_case). In a programming language, syntax is binary; it works or it doesn't. In English, the difference between "Ensure variables are immutable" and "Make sure variables never change" might yield different results based on the model’s training data.

When you combine the fuzziness of human language with the "black box" probabilistic processing of an LLM, you face a difficult question: *What is the weather going to be today in the land of AI?*

To answer that, you have to make the intentions behind your prompts explicit.

## Be Efficient with Brains (Pair & Review)

Writing a prompt is an exploratory process of finding words that trigger the best response. However, a single writer is limited by their own understanding. This is risky with LLMs, which are suited for ambiguous problems but require strict guardrails.

Relying on one person to do this creates blind spots. We found that prompt quality benefits from [pairing](https://martinfowler.com/articles/on-pair-programming.html). A diversity of thought helps create a more complete definition of any problem. What one engineer considers a clear instruction, another might see as a loophole. Pairing covers the gaps that a single brain might miss.

Furthermore, you should also review every prompt. This isn't just checking for typos; it’s a logic check. Does this prompt align with business requirements? **We’ve found that prompt reviews often uncover disagreements about the requirements themselves, forcing us to align as a team before we ship.**

## Document "The Why" and manage change

Because English is fuzzy, the intent behind a specific word choice isn't always obvious. Why did we use the passive voice here? Why did we specify "immutable"?

Even well-structured prompts can eventually obscure the original business requirements. As optimizations blur into the general text, we must take every opportunity to document "the why":

* **Documentation:** Avoid relying on prompts as canonical business requirements. Our LLM requests are a combination of system instructions, user input, context, and deterministic post-processing; the prompt is not complete for onboarding a developer.  

* **Comments:** Comment on complex prompts just as you would complex code. Spotlight specific constraints or even punctuation to explain the problem they solve. The model is a moving target, so any unintentional changes can make troubleshooting hard.  

* **Commit Messages:** Use commit messages as an opportunity to explain what was wrong with the prompt. (for example, `fixed: Missing comma lost John`)

## Separation of Concerns (Use Dedicated Files)

Writing code and writing prompts require distinct mindsets. One focuses on syntax and execution flow; the other on semantics and intent. [Embedding long English instructions inside code creates a distraction](https://medium.com/@rexnino/what-is-separation-of-concerns-in-coding-and-why-is-it-important-731aa8cfa898).

We keep prompts in dedicated files to disentangle application logic from the LLM interaction configuration, which requires frequent tuning.

By treating the prompt as a standalone component, we can prototype and iterate on the LLM behavior independent of the application's control flow. [Tools like dotprompt](https://google.github.io/dotprompt/) allow us to treat these files as first-class artifacts containing text, model parameters, and schema definitions. This highlights that invoking a model isn't just a function call; it’s an integration with a distinct system that requires its own configuration.

## Use Structured Output

To build a reliable tool, you need a bridge between unpredictable LLM output and deterministic computers.

We rely on [structured output](https://genkit.dev/docs/models/#structured-output)[^1] to guide the model to emit JSON according to a schema. Even if we only need a single field, defining a schema provides a guardrail that helps the model output conform to a shape we can validate programmatically. This is critical for code generation, where models often add unwanted preambles, conversational filler, or inconsistent markdown fences.

If the output doesn't match the schema, we fail fast or retry. This allows us to integrate the LLM output into our process with the same confidence we have in detecting a bad API response.

## From Magic to Engineering

Moving from one successful prompting to a reliable system requires acknowledging that prompts are code. You need to manage, review, and test them with the same rigor applied to the rest of your stack. While we are still working on better ways to [benchmark quality](https://www.statsig.com/perspectives/what-are-non-deterministic-ai-outputs-), treating our prompts as first-class codebase assets is our first step towards building confidence in our AI assisted automation.

## Join the conversation

I'm curious how you are handling the fuzzy nature of LLMs in production.

* How does your team review and test prompts?  
* Do you treat prompts as configuration, code, or something else entirely?  
* Share your workflow in the comments.

## Read More

* [The lumberjack paradox: From theory to practice](https://dev.to/googlecloud/the-lumberjack-paradox-from-theory-to-practice-2lb5) by Jennifer Davis

* [Google Sandwich Manager, and the hallucinated SDK](https://medium.com/google-cloud/google-sandwich-manager-and-the-hallucinated-sdk-6bed653e6318) by Katie McLaughlin and Brian Dorsey

Thanks to [Jennifer Davis](https://dev.to/sigje) & [Shawn Jones](https://shawnmjones.org/) for review and contributions.

[^1]:  This links to how structured output works in the Genkit framework, which my team is using. A succinct example.
