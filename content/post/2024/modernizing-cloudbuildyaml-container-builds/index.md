---
title: 'Modernizing cloudbuild.yaml Container Builds'
date: '2024-03-21T21:05:17Z'
publishDate: '2024-03-21T21:05:17Z'
# Description is used as a page subtitle & search index.
description: 'Use automapSubstitutions to make scripting in Cloud Build steps more concise.'

categories:
  - notes
tags:
  - googlecloud
  - cloudbuild
  - artifactregistry
  - ci/cd
  - containers
  - yaml

draft: false # hide from production deployment
hidden: false # hide from lists if true
rssFullContent: true

# Specify an external canonical URL.
# If both are present, will be linked from footer of post.
canonical: https://dev.to/googlecloud/modernizing-cloudbuildyaml-for-container-builds-1je0
canonicalTitle: DEV

context: | 
  Wrote this to share a discovery made while updating the Google Cloud Run quickstart image pipeline, then got carried away.

---
{{< callout info >}}tl;dr: [Running bash scripts](https://cloud.google.com/build/docs/configuring-builds/run-bash-scripts) in the Cloud Build documentation tells you use the `script` property with the `automapSubstitutions` option and I give this recommendation a 💯.
{{</ callout >}}

One of my favorite things to do in writing YAML is minimizing the square brackets and hyphens. The frequency of these things really impacts readability for me, especially when I'm trying to glance at a whole block of markup and assess what it's trying to do.

Over the last couple years, Cloud Build has made improvements in reducing square brackets and enabling more concise configuration for running shell scripts.

I'm going to step through a couple "generations" of simplification I walked through today in overhauling a container image build pipeline.

This is what a lot of minimal Cloud Build configurations look like:

```yaml
steps:
- id: 'Build Container Image'
  name: 'gcr.io/cloud-builders/docker:latest'
  args: ['build', '--tag', 'gcr.io/${PROJECT_ID}/${_IMAGE}:latest', '.']

- id: 'Push Container Image to Container Registry'
  name: 'gcr.io/cloud-builders/docker:latest'
  args: ['push', 'gcr.io/${PROJECT_ID}/${_IMAGE}:latest']

substitutions:
  _IMAGE: service
```

* **Starting character count:** 335

## Step 1: Remove square-bracketed [distractions]

Use the `script` property instead of `args`.

```yaml
steps:
- id: 'Build Container Image'
  name: 'gcr.io/cloud-builders/docker:latest'
  script: docker build --tag gcr.io/${PROJECT_ID}/${_IMAGE}:latest .

- id: 'Push Container Image to Container Registry'
  name: 'gcr.io/cloud-builders/docker:latest'
  script: docker push gcr.io/${PROJECT_ID}/${_IMAGE}:latest

substitutions:
  _IMAGE: service
```

🛑 If you try to use this it won't work.

It turns out switching from `args` to `script` means the code **no longer has substitutions, only environment variables**.

A year ago, the next step would be to manually map the substitutions to environment variables:

```yaml
steps:
- id: 'Build Container Image'
  name: 'gcr.io/cloud-builders/docker:latest'
  script: docker build --tag gcr.io/${PROJECT_ID}/${_IMAGE}:latest .

- id: 'Push Container Image to Container Registry'
  name: 'gcr.io/cloud-builders/docker:latest'
  script: docker push gcr.io/${PROJECT_ID}/${_IMAGE}:latest

options:
  env:
  - PROJECT_ID=$PROJECT
  - _IMAGE=$_IMAGE
  
substitutions:
  _IMAGE: service
```

**Updated character count**: 390 (sometimes shorter isn't clearer)

## Step 2: Remove excess hyphenation

Use the `automapSubstitutions` global option and the substitutions are injected as environment variables to all step scripts.

```yaml
steps:
- id: 'Build Container Image'
  name: 'gcr.io/cloud-builders/docker:latest'
  script: docker build . --tag gcr.io/${PROJECT_ID}/${_IMAGE}:latest

- id: 'Push Container Image to Container Registry'
  name: 'gcr.io/cloud-builders/docker:latest'
  script: docker push gcr.io/${PROJECT_ID}/${_IMAGE}:latest

options:
  automapSubstitutions: true
  
substitutions:
  _IMAGE: service
```

**Updated character count**: 373 (progress, but I'd be more satisfied if we got back to 335 somehow)

## Step 3: Simplify the Image Push

Since this Cloud Build configuration doesn't have any follow-up steps that need to use the image outside the build, I can simplify further, by replacing the step *Push Container Image to Container Registry* with the `images` property. (Read more about that in [Remember images in your cloudbuild.yaml!](https://dev.to/googlecloud/remember-images-in-your-cloudbuild-yaml-o3l) by @glasnt).

```yaml
steps:
- id: 'Build Container Image'
  name: 'gcr.io/cloud-builders/docker:latest'
  script: docker build . --tag gcr.io/${PROJECT_ID}/${_IMAGE}:latest

images:
- gcr.io/${PROJECT_ID}/${_IMAGE}:latest

options:
  automapSubstitutions: true
  
substitutions:
  _IMAGE: service
```

This doesn't reduce hyphens any further, but it does reduce two lines of configuration.

**Updated character count**: 263 (Success!)

---

## Bonus Round: Migrating to Artifact Registry

You may have noticed this configuration ships the container image to a Google Container Registry URL! There's a good chance if you are modernizing your Cloud Build YAML this way you've got some GCR in place, if so, you need to migrate soon. 

> Check out [Transition from Container Registry](https://cloud.google.com/artifact-registry/docs/transition/transition-from-gcr). There are options to migrate while keeping the gcr.io URL.

Say I've already created a new Artifact Registry instance for my container, what does that YAML look like?

(Let's say it's a multi-regional repository called `container` in the `us` location.)

```yaml
steps:
- id: 'Build Container Image'
  name: 'gcr.io/cloud-builders/docker:latest'
  script: docker build . --tag "us-docker.pkg.dev/${PROJECT_ID}/container/${_IMAGE}:latest"

images:
- "us-docker.pkg.dev/${PROJECT_ID}/container/${_IMAGE}:latest"

options:
  automapSubstitutions: true
  
substitutions:
  _IMAGE: service
```

Since an Artifact Repository is a Cloud resource, a project might have more than one. This encourages me to parameterize the location and repository name, so that development & testing are easier.

```yaml
steps:
- id: 'Build Container Image'
  name: 'gcr.io/cloud-builders/docker:latest'
  script: docker build . --tag "${_LOCATION}-docker.pkg.dev/${PROJECT_ID}/${_REPO}/${_IMAGE}:latest"

images:
- "${_LOCATION}-docker.pkg.dev/${PROJECT_ID}/${_REPO}/${_IMAGE}:latest"

options:
  automapSubstitutions: true

substitutions:
  _IMAGE: service
  _LOCATION: us
  _REPO: container
```

**Updated character count**: 358 (A little longer than 335, but Artifact Registry is more verbose than Container Registry, not much we can do about that.)

In a more complicated YAML block, `automapSubstitutions` would make a much bigger difference. I go out of my way to avoid using `args`, and this option helps me do that without needing to add a lot of boilerplate config.

{{< callout info >}}Aside Info: Reader, maybe you'll suggest I delete all those pesky curly braces around my variables, getting me a win at 342 characters. This [stackoverflow answer](https://stackoverflow.com/a/8748880) explains the value of the curly braces, and I'm one of the people that makes curly braced variables part of my "shell variables in strings" practice.
{{</ callout >}}

> Check out a continuation of this post by [Katie McLaughlin](https://dev.to/glasnt) at [Codegolf: Build a container in Cloud Build](https://dev.to/googlecloud/codegolf-build-a-container-in-cloud-build-4okn).
