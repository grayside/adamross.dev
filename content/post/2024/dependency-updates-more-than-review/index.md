---
title: 'Dependency Updates: More to Do Than Review'
date: '2024-05-01T05:31:53Z'
publishDate: '2024-05-06T05:31:53Z'
# Description is used as a page subtitle & search index.
description: 'Dependency update tooling is fantastic but you still own code stability.'

categories:
  - Post
tags:
  - code reviews
  - dependencies
  - supply chain
  - renovatebot
  - test coverage
  - shared knowledge

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
  > **Perspective:** I've used Renovate (a lot) and Dependabot (some) for years, in dozens of repositories, with everything from near defaults to elaborate configurations. Not examining automated dependency updates closely enough before merge have created extra maintenance work.

---

I enjoy tools that automate dependency updates in my projects (looking at you, Renovate and Dependabot). They save my team a lot of time: with a quick glance and the push of a couple buttons, I can update dozens of dependencies in a repository. Even better, the work came to me, I didn't need to go looking for release schedules or subscribe to a newsletter. This is a big change to how I see teams handle 3rd party updates.

Merging an update just because the tests pass creates a mess.

## Context: What do these dependency automation tools do?

They help in the following ways:

* Notify project maintainers that a dependency has an update
* Modify the project's package manifest to reference the updated code
* Open a Pull Request and highlight some context on the change
* and because you have CI set up, they trigger your automated tests

When I start my day and look at a Pull Request created by a tool like this, I may see a lot of encouraging green checkmarks✅ declaring merge-worthiness.

## The tools did all the work, I can review & merge, right?

Seeing green checkmarks is necessary but not sufficient to merge. I trust automated checks to tell me where to prioritize my review time. As the human in the loop, I should verify:

1. Did the automated tests pass?
2. Does the library have a history of causing trouble? Maybe I should manually test the change.
3. Does the package follow [semantic versioning](https://semver.org/)? Is their a compatibility break from a major update? What if the break doesn't change the API specification, but critical details in the payload data?

With answers "Yes", "No Problem", and "No", I could just merge...

{{< callout important >}}
🛑 WAIT. I've left something out: _I know that I don't have 100% test coverage_. Are the tests passing because the change is good, or because there is a test coverage gap and I have no idea what will happen next?
{{< /callout >}}

The question _"Did the automated tests pass?"_ becomes _"Is there automated test coverage in my project and does it cover the use of this dependency? Did those tests pass?"_

## Let's see an example of untested dependencies slipping through

In this example, suppose a dependency called _ThirdPartyAuth_ is an important part of the business logic. Below I'll show some of the potential misunderstandings from different testing strategies.

```js
// Important business logic that takes a callback/function parameter.
doGoodThings(maybeAMockedRequestFunction) {
  maybeAMockedRequestFunction()
}

RealRequest() {
  return http_request(
    'https://example.com',
    ThirdPartyAuth.coolLibraryAuthV2()
  )
}

doGoodThings(RealRequest)
```

### Test Example #1: No Test

Test success reported with no tests run. Do I remember during code review?

```js
// A commented out test stub.
// Test_doGoodThings()

✔️ Success // No tests were run
```

### Test Example #2: Mocked Test

End-to-end testing can be expensive to write and much more expensive to maintain. Instead, we could mock the responses from external APIs and scope the tests to local business logic.

This test does not cover the use of the _ThirdPartyAuth_ library.

```js
MockRequest() {
  return "OK"
}

Test_doGoodThings(MockRequest)

✔️ Success // What does that mean?
```

### Test Example #3: End-to-End Test

The integration of `ThirdPartyAuth.coolLibraryAuthV2()` with the codebase is verified.

If the test fails it may be a breaking change in the library or perhaps `https://example.com` is down.

```js
Test_doGoodThings(RealRequest)

✅ Success // Probability of false negative > false positive
```

The value of the ✅ passing test is limited by the depth of test coverage and my knowledge of the limitation. No only me: since my team handles maintenance on a rotating basis, the value of the tests depends on how well each teammate understands those limitations.

## Functionality not broken, but update not complete

Just because a library works doesn't mean it is used effectively.

When a dependency has significant changes over time, it likely gains new methods, recommended usage patterns, and planned deprecations on old ways of working. Upgrading the dependency without understanding the new, happy paths sets up a future with a broken update.

**Today's outdated practice is tomorrow's deprecated design.**

## Call to Action

Passing tests can be deceptive, and package manifests aren't the only spot in your codebase to need changes when a big update happens.

{{< callout important >}}
**💡 The robots cannot do all the necessary work to update a dependency safely.** You _do not need_ deeper test coverage. You and your collaborators _do need_ a common understanding of the limits of what a passing test means, and what needs to happen to keep shipping quality software after automated testing has done what it could.
{{< /callout >}}

In terms of Renovate configuration, maybe add some [extra guidance to those Pull Requests](https://docs.renovatebot.com/configuration-options/#prbodynotes):

```json
{
  "prBodyNotes": "Do the tests cover this dependency? You may have more work to do."
}
```
