---
title: '{{ replace .File.ContentBaseName `-` ` ` | title }}'
date: '{{ .Date }}'
publishDate: '{{ .Date }}'
# Description is used as a page subtitle & search index.
description: 'Some description, huh?'

categories:
  - TODO
tags:
  - TODO

draft: true # hide from production deployment
hidden: false # hide from lists if true
rssFullContent: true

# Specify an external canonical URL.
# If both are present, will be linked from footer of post.
# canonical: http://example.com
# canonicalTitle: Example Article Title

context: | 
  Exploring biases.
---

## Image Gallery

From the post bundle directory.

```markdown
![Image 1](1.jpg) ![Image 2](2.jpg)
```

## Shortcodes

### Callouts

{{< callout info >}}Hello world{{</callout>}}
{{< callout important >}}Important{{</callout>}}
{{< callout error >}}Error{{</callout>}}

### Quote

{{< quote author="A famous person" source="The book they wrote" url="https://en.wikipedia.org/wiki/Book">}}
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
{{< /quote >}}

### Youtube

{{< youtube "0qwALOOvUik" >}}

### Gist

{{< gist CaiJimmy e2751a943de10b2a5b3a8a6c2120cb86 >}}

## Markdown Reference

### Blockquote

> Don't communicate by sharing memory, share memory by communicating.<br>
> — <cite>Rob Pike[^1]</cite>

[^1]: The above quote is excerpted from Rob Pike's [talk](https://www.youtube.com/watch?v=PAAkCSZUG1c) during Gopherfest, November 18, 2015.

### Foonote

Rob Pike[^1]

[^1]: The above quote is excerpted from Rob Pike's [talk](https://www.youtube.com/watch?v=PAAkCSZUG1c) during Gopherfest, November 18, 2015.

### Tables

| Italics   | Bold     | Code   |
| --------  | -------- | ------ |
| *italics* | **bold** | `code` |

### Other Stuff

<abbr title="Graphics Interchange Format">GIF</abbr> is a bitmap image format.

H<sub>2</sub>O

X<sup>n</sup> + Y<sup>n</sup> = Z<sup>n</sup>

Press <kbd>CTRL</kbd> + <kbd>ALT</kbd> + <kbd>Delete</kbd> to end the session.

## Math

Stack has built-in support for math typesetting using [KaTeX](https://katex.org/).

**It's not enabled by default side-wide,** but you can enable it for individual posts by adding `math: true` to the front matter. Or you can enable it side-wide by adding `math = true` to the `params.article` section in `config.toml`.

### Inline math

This is an inline mathematical expression: $\varphi = \dfrac{1+\sqrt5}{2}= 1.6180339887…$

```markdown
$\varphi = \dfrac{1+\sqrt5}{2}= 1.6180339887…$
```

### Block math

$$
    \varphi = 1+\frac{1} {1+\frac{1} {1+\frac{1} {1+\cdots} } } 
$$

```markdown
$$
    \varphi = 1+\frac{1} {1+\frac{1} {1+\frac{1} {1+\cdots} } } 
$$
```

$$
    f(x) = \int_{-\infty}^\infty\hat f(\xi)\,e^{2 \pi i \xi x}\,d\xi
$$

```markdown
$$
    f(x) = \int_{-\infty}^\infty\hat f(\xi)\,e^{2 \pi i \xi x}\,d\xi
$$
```