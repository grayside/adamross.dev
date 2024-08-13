---
title: 'Where to Host My Static Tech Blog'
date: '2024-07-31T19:26:11Z'
publishDate: '2024-08-13T12:00:00Z'
# Description is used as a page subtitle & search index.
description: 'Decision-making process to use Firebase Hosting with my Hugo site.'

categories:
  - Decision
tags:
  - hugo
  - meta
  - firebase hosting
  - web hosting
  - arc.0

draft: false # hide from production deployment
hidden: false # hide from lists if true
rssFullContent: true

# Specify an external canonical URL.
# If both are present, will be linked from footer of post.
# canonical: http://example.com
# canonicalTitle: Example Article Title

image: cover.jpg
imageCredit: |
  Photo by <a href="https://unsplash.com/@mikeenerio?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Mike Enerio</a> on <a href="https://unsplash.com/photos/aerial-view-of-grass-H58bnmnedTc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
  

context: | 
  I'm a decision record enthusiast and will absolutely write more about this in the future. This post format is inspired by [Architectural Decision Records](https://adr.github.io/) and is meant to show how I got to Firebase Hosting, not how to use it.
---

* **Status:** [~~thinking~~ | ~~nope~~ | **current** | ~~superceded by <record>~~]
* **Last Updated:** Mar 9, 2024
* **Objective:** Choose a web host for my static site

### Context & Problem Statement

To have a website, there must be a web host. A website is the visual artifact that everyone sees, the pages, images and videos. The web host is a service provides the compute, storage, networking, and all the bits that make it usable. For my personal website, I need to choose a web host that adheres to the following priorities and constraints.

### Priorities & Constraints <!-- optional -->

Every project has requirements, here are a few of mine:

* **Be low cost:** My personal website is a personal expense I'd rather keep minimal. My goal is to fit within a free tier, but not to exceed a "dollars per month" cost.
* **Be focused on publishing over development:** My time is immensely valuable. I want to publish content with some flexibility to tailor for my way of working. However, I do not want to generate (too many) new side projects in web development instead of writing.
* **Be low attention:** I want to ignore this site for months at a time without incurring security risks or a backlog of infrastructure projects.
* **Minimal Service Providers:** I prefer to use the fewest number of service providers for my personal IT. I have GitHub, DNS, and Google Cloud already in the mix. Each additional company is another credit card profile to update and set of corporate customer policies to track.
* **Learn from Everything:** Balance existing skills with learning opportunities
* **Sourcing from GitHub:** I've decided to keep my site source and content in a public GitHub repository. I want to see merges to my `main` branch deploy to a live site.
* **Constraint:** I've previously chosen Hugo and Go with code hosted on GitHub as tools for building the site. Hugo is a static site generator flexible enough to be used across many (all?) hosting services.

### Considered Options

* **Option 1:** Use [Firebase Hosting](https://firebase.google.com/docs/hosting)
* **Option 2:** Use [GitHub Pages](https://pages.github.com/)
* **Option 3:** Use [Google Cloud Run](https://cloud.google.com/run/docs/)
* **Option 4:** Use [Netlify](https://www.netlify.com/)

## Decision

Chosen option **Option 1: Firebase Hosting**

Firebase Hosting is a specialized tool for static site hosting, providing ease of deployment, a global CDN, and a good free tier. I have a lot of familiarity with Google Cloud, but less familiarity with the Firebase products. This provides an opportunity to broaden my knowledge.

{{< callout info >}}
Working for Google means my day job pays me to be familiar with Google Cloud, which may lead me to use Google Cloud for more personal projects. This gives Firebase Hosting some advantage in the decision.
{{</callout>}}

### Alternatives Considered

I have experience hosting sites on **Option 2: GitHub Pages**. However, I have been frustrated by the lack of performant redirects (redirects via `HTML meta refresh` lack wildcard configuration options and can't be cached compared to server-side options). Other solutions have server-side redirect features ([firebase hosting](https://firebase.google.com/docs/hosting/full-config#redirects), [netlify](https://docs.netlify.com/routing/redirects/))

I've found GitHub Pages to be a bit complex for PR Previews, with various advice around multiple repositories, random & niche GitHub Actions, or building your own [custom preview solution](https://github.com/grayside/gh-page-previews).

**Option 3: Use Google Cloud Run.** With 6 years of experience working on the developer experience of Google Cloud Run, using it would be straightforward. I don't need (or want) the control over the serving details. The other options align more with my preference to minimize maintenance. 

{{< callout info >}}💡 My teammate Brian Dorsey has a great post on [hosting a static website using Google Cloud Run](https://til.cafe/blog/2024/hosting-static-web-site-using-google-cloud-run/) if you'd like a recipe.{{</callout>}}

**Option 4: Use Netlify.** Several folks recommended Netlify as their preferred static site host, with strong GitHub integration. Since I am already using Google Cloud for other projects, Netlify has a disadvantage as a new service provider. If Firebase Hosting doesn't work out, Netlify would be my next choice. I don't want to pick up a new infrastructure hobby.

### Expected Consequences

Any of these options will meet my goal of serving a few megabytes of data. I expect problems to come from pet peeves or curiousity about the alternatives. Either way, this is a **_low stakes decision not worth a deep analysis_**. If I had to change my decision, I expect the switching cost to move to another static site host to be an evening of work.

## Revisiting this Decision

I've scheduled a reminder for March 2025 to reflect on this decision, and decide if I'd like to change hosting. With the low expected switching cost, it might be worth trying another host just for the learning experience.
