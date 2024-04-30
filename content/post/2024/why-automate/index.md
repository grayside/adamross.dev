---
title: 'Why Automate?'
date: '2024-04-30T12:00:55Z'
publishDate: '2024-04-30T12:00:55Z'
# Description is used as a page subtitle & search index.
description: 'Thinking through the value of automation beyond saving yourself time.'

categories:
  - Post
tags:
  - software
  - automation
  - tradeoffs
  - xkcd

image: cover.webp
imageCredit: |
  Photo by <a href="https://unsplash.com/@britishlibrary?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">British Library</a> on <a href="https://unsplash.com/photos/gigantic-machine-sketch-Y1S0ApwC054?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>

draft: false # hide from production deployment
hidden: false # hide from lists if true
rssFullContent: true

# Specify an external canonical URL.
# If both are present, will be linked from footer of post.
# canonical: http://example.com
# canonicalTitle: Example Article Title

context: | 
  **Perspective:** I've worked on products that can be used to help automation projects, such as [Google Cloud Run](https://cloud.google.com/run/docs), [Scheduler](https://cloud.google.com/scheduler/docs), and [Workflows](https://cloud.google.com/workflows/docs). Within my organization, I'm currently arguing for the need to use more automation to scale our code review and policy enforcement practices.
---

Automation saves time and toil when performing a task, and we all appreciate that. This is one frame for justifying automation but it limits view of other opportunities.

I love how automation transforms the tedium of work into puzzle-solving. Early in my career, I learned that management would value puzzle time (automation work) by pointing out a greater benefit for the effort than the time spent. (Aim for minimal time investment without sacrificing opportunities or incurring hidden costs like a high maintenance burden.)

Folks enjoy citing [XKCD 1205](https://xkcd.com/1205/) in automation discussions because it does a great job illustrating the opportunity time cost of automation. The time spent to code a new solution for an already solved problem doesn't help solve business goals until after the investment pays off.

![A grid with the header 'How long can you work on making a routine task more efficient before you're spending more time than you save' the x-axis is the frequency of the task, the y-axis is the time it takes using prospective automation. It implies a narrow path where automating has value.](https://imgs.xkcd.com/comics/is_it_worth_the_time.png "[XKCD #1205: Is it worth the time?](https://xkcd.com/1205/)")

{{<callout info>}}What's automation anyway? I'd call it "_software that reduces the amount of effort humans need to put in to get a job done_". It replaces people work with CPU work.{{</callout>}}

As I continued my journey as a developer, I found that the cost/benefit time ratio around automation didn't line up with my thinking. Time trade-offs are essential, but there are more variables in the trade-off equation than time--other ways of **_recouping effort_**. A particular automation may have other goals than time-savings.

## Planning for a big push

Perhaps the usual routine doesn't deserve automation. What if your team plans a sprint next month, focusing everyone's effort on a big change? The trade-offs are different: Increasing the frequency of a task and the number of people to onboard to a task both change the math, raising the value of automation. A bit of scripting here also improves the chances the team meets a time-bound goal.




![(Cueball, a friend also drawn as Cueball, Danish, and Black Hat are standing together. Danish is looking at her phone.)
Cueball: ...And then after the one in 2024, there's another on August 12, 2045.
Friend: We're in! We can invite our kids, assuming we have any.
Danish: I'll create an event. Do you think we'll still be using Google Calendar in 2045?
Black Hat: Sorry, I'd love to make it, but I have a thing that day.
(Caption below the panel:)
It's weird making plans for eclipses.](https://imgs.xkcd.com/comics/2045.png "[XKCD 2685: 2045](https://xkcd.com/2685/)")

This kind of automation makes the most sense when swarming on a specific task, such as updating code to use a new logging library or migrating data between systems. This may represent a one-time need, so build with minimal flexibility and worry more about good error messages than over-all maintainability. (_This is a good use for a shell script shared, maybe not even committed to source control._)

## Hiding complexity

Folks need to do perform complex tasks but workflow automation can simplify the details. You don't need to ask everyone to carry the full cognitive load when tools can do more of the lifting. It also provides paths to onboard folks to the team in stages. How fast can folks be productive in your environment? For "new person job satisfaction", it is best to measure this in hours or days instead of weeks.

More persistent automation like this is an abstraction layer to hide a mess.

![A flow chart showing the tooling dependencies you need to be productive, with loops back showing the chaos and the way tooling becomes it's own goal](https://imgs.xkcd.com/comics/tech_loops.png "[XKCD 1579: Tech Loops](https://xkcd.com/1579/)")

Reducing cognitive load helps makes a complex system more approachable. More than a simplification in performing the task, it provides the vocabulary for non-experts to discuss the process. When folks have the ability to communicate about a task, new opportunities for collaboration open.

![Two people talking. Person 1 says "Silicate chemistry is second nature to us geochemists, so it's easy to forget that the average person probably only knows the formulas for olivine and one or two feldspars." Person 2 says "And quartz, of course." Person 1 says "Of course." Below the panel "Even when they're trying to compensate for it, experts in anything wildly overestimate the average person's familiarity with their field."](https://imgs.xkcd.com/comics/average_familiarity.png "[XKCD 2501: Average Familiarity](https://xkcd.com/2501/)")

## What do you do every day?

In the last month, I've given thought to the developer superpower of "defining the job you want". Automation is often thought of as a way to create more space to do the job you already have, but it is also a way to **redefine the job**. You can reinvest the time gained from automation on improving the automation, perhaps even expanding the scope of what the task achieves for your team. As a software developer, maintaining evolving software is more satisfying than turning a crank every 5 minutes!

![Top caption says "I spend a lot of time on this task. I should write a program automating it! Below are two graphs, the first showing the theoretical spike in workload to write code followed by a significant reduction in effort. The second shows the reality of a spike in workload followed by a runaway increase in work developing the new system instead of performing the original task at all](https://imgs.xkcd.com/comics/automation.png "[XKCD 1319: Automation](https://xkcd.com/1319/)")

## The Checklist

When I think of automation now, I consider these factors:

* Will it save time? When will the up-front investment pay off?
* Will it help others contribute in a complex process?
* Will it make discussing a big, complex system easier, leading to better high-level decisions?
* Will it create space for more interesting activities?
* Will it replace existing activities with a style of work I prefer?

**Don't limit your thinking about the time payoff of automating something, but do consider all the relevant trade-offs.**

Do you have a favorite XKCD comic that helps you think about automation? [Let's talk about it!](https://hachyderm.io/@grayside).

Additional Credits: Thanks to [explain xkcd](https://www.explainxkcd.com/) for XKCD alt text (not to be confused with XKCD hover text, which you get by visiting xkcd.com).
