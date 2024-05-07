---
title: 'Dependency Updates: More to Do Than Review'
date: '2024-05-01T05:31:53Z'
publishDate: '2024-05-18T05:31:53Z'
# Description is used as a page subtitle & search index.
description: 'Dependency update tooling is fantastic but you still own code stability.'

categories:
  - Post
tags:
  - code reviews
  - dependencies
  - supply chain
  - renovate
  - testing

draft: false # hide from production deployment
hidden: false # hide from lists if true
rssFullContent: true

image: cover.png

imageCredit: |
  Cover photo by <a href="https://unsplash.com/@chiklad?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Ochir-Erdene Oyunmedeg</a> on <a href="https://unsplash.com/photos/close-photo-of-green-grass-LmyPLbbUWhA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
  

# Specify an external canonical URL.
# If both are present, will be linked from footer of post.
# canonical: http://example.com
# canonicalTitle: Example Article Title

context: | 
  > **Perspective:** I've been using Renovate (a lot) and Dependabot (some) for years, in dozens of repositories, with everything from near defaults to elaborate configurations. I believe some maintenance challenges have come from not examining automated dependency updates closely enough before merge.

---

I enjoy tools that automate dependency updates in my projects (looking at you, Renovate and Dependabot). They really save my team a lot of time. With a quick glance and the push of a couple buttons, I can update dozens of dependencies in a repository. Even better, the work came to me, I didn't need to go looking for release schedules or subscribe to a newsletter. This is helpful, powerful even, and not the most time consuming part of maintaining a project's 3rd party dependencies.

{{< callout info >}}👋 This post isn't about saving time by scheduling and grouping dependency updates, though those are great things to consider.{{</ callout >}}

## Let's get on the same page, what do dependency automation tools do?

They help in the following ways:

* Notify project maintainers that a dependency has an update
* Modify the project's package manifest to reference the updated code
* Open a Pull Request and inline some context on the change
* and Trigger your automated tests

## The tools did all the work, I can review & merge, right?

No–the tools can set me up to confirm a best case scenario or dig deeper. Even recognizing the best case is tricky. Let's start with some basic questions:

1. Did the automated tests pass?
2. Do I recall if there's a problem with the library's previous updates? Maybe I should manually test the change.
3. Does the package follow [semantic versioning](https://semver.org/) and can I tell whether this is a major update? Maybe there's a compatibility break to consider.

With answers "Yes", "No Problem", and "No", I could just merge...

🛑 WAIT. I've left something out: _I know that I don't have 100% test coverage_. Does that mean the passing tests are because the change is good, or because this dependency update sits in a test coverage gap and I have no idea what will happen next? 

The question _"Did the automated tests pass?"_ becomes _"Is there automated test coverage in my project and does it cover the use of this dependency? Did those tests pass?"_

### Let's see an example of untested dependencies slipping through

```js
// Important business logic that takes a callback/function parameter.
doGoodThings(maybeAMockedRequest) {
  maybeAMockedRequest()
}
```

#### No Test

```js
// Test_doGoodThings()

✔️ Success // No tests were run.
```

#### Mocked Test

```js
MockRequest() {
  return "OK"
}

Test_doGoodThings(MockRequest) 

✔️ Success // What does that mean?
```

#### End-to-End Test

```js
RealRequest() {
  return http_request(
    'https://example.com',   
    ThirdPartyAuth.coolLibraryAuthV2()
  )
}
Test_doGoodThings(Request)

✅ Success // Can we flake towards a false positive?
```

In the mocked test, the dependency <strong>3rdPartyAuth </strong>isn't tested. In the end-to-end test, the library and my use of <code>coolLibraryAuthV2</code> is verified, so I'll notice a breaking change. Of course, the "No Test" case tells me nothing but I still might need to dig into the test results to remember the gap.

Suddenly the value of the ✅ green checkmark of a passing test is limited by my knowledge of the project. Not only me, since my team handles this kind of maintenance on a rotating basis. The value of that green check really depends on how well all my teammates understand the mapping of test coverage to 3rd party code.

## Functionality isn't broken, but the update isn't complete

Just because a library is still working doesn't mean I'm holding it right.

It turns out that if dependencies change over time, they are likely to have new methods, recommended patterns, and planned deprecations on old ways of working. Upgrading the version of a dependency incurs technical debt if the project continues using code slated for deprecation.

**Today's outdated practice is tomorrow's deprecated design.**

Given this, I might merge the update, but I should also evaluate if there are more changes to make, and whether those changes have meaningful value or go into a tech debt backlog. (I prefer anticipated debt to surprise debt, so that I can plan how to handle the work on my own terms.)

## Call to Action

Passing tests can be deceptive, and package manifests aren't the only spot in your codebase likely to need changes. There's more work than what the robots do for you.

In terms of Renovate config, maybe add some [extra configuration so those Pull Requests include a reminder](https://docs.renovatebot.com/configuration-options/#prbodynotes):

```json
{
  "prBodyNotes": "Do the automated tests cover this dependency? How big was this change? You may have more work to do."
}
```
