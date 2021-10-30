---
date: 2021-10-30
title: differently 1.0.0
tags:
  - dinject
  - python
---

Python has baked-in support for describing the differences between bodies of text, but I wanted:

- Colourful visualisations of differences, and:
- Meaningful visualisations of the differences between JSON and YAML too

So, here's `differently`!

<!--more-->

`differently` is a CLI application and Python package that visualises the differences between strings, multi-line text bodies, and any objects that can be serialised to JSON or YAML.

## CLI

For a quick CLI example, first install `differently`:

```bash
pip install differently
```

To visualise the differences between two files, just pass them in:

```bash
differently a.txt b.txt
```

That'll print:

<!-- markdownlint-disable no-inline-html -->

<!--dinject as=html host=terminal range=start-->

<style type="text/css">.thtml { --green: #0C0; --yellow: #CC0; --red: #C00; } .foreground-green { border-color: var(--green); color: var(--green); } .foreground-yellow { border-color: var(--yellow); color: var(--yellow); } .foreground-red { border-color: var(--red); color: var(--red); }</style><pre class="nohighlight thtml"><code class="thtml-code"><span class="foreground-green">Hello!</span>                 <span class="foreground-green">=</span>  <span class="foreground-green">Hello!</span><br /><span class="foreground-yellow">This is just a test.</span>   <span class="foreground-yellow">~</span>  <span class="foreground-yellow">This is a test.</span><br /><span class="foreground-red">TODO: Delete this.</span>     <span class="foreground-red">x</span><br /><span class="foreground-green">Reckon that's enough?</span>  <span class="foreground-green">=</span>  <span class="foreground-green">Reckon that's enough?</span><br />                       <span class="foreground-yellow">&gt;</span>  <span class="foreground-yellow">Yep.</span><br /></code></pre>

<!--dinject range=end-->

`a.txt` is printed on the left and `b.txt` is on the right. The symbols between them indicate:

- `=`: no change
- `~`: change
- `x`: deletion
- `>`: insertion

Likewise:

- Lines without changes are coloured green
- Changes and insertions are yellow
- Deletions are red

What about JSON and YAML?

## Comparing data

`differently` treats files as plain text by default, but you can force them to be treated as JSON or YAML by specifying `--in-format json` or `--in-format yaml`.

When you specify a data format, `differently` does a little bit of preparation before scanning for differences:

- Dictionary keys are sorted alphabetically
- Consistent formatting (i.e. brace styles, indenting, etc) is applied to both files

This makes the visualisation meaningful regardless of the ordering and code styles of each file:

```bash
differently a.json b.json --in-format json
```

<!--dinject as=html host=terminal range=start-->

<style type="text/css">.thtml { --green: #0C0; --yellow: #CC0; --red: #C00; } .foreground-green { border-color: var(--green); color: var(--green); } .foreground-yellow { border-color: var(--yellow); color: var(--yellow); } .foreground-red { border-color: var(--red); color: var(--red); }</style><pre class="nohighlight thtml"><code class="thtml-code"><span class="foreground-green">{</span>                    <span class="foreground-green">=</span>  <span class="foreground-green">{</span><br /><span class="foreground-green">  "key_0": "alpha",</span>  <span class="foreground-green">=</span>  <span class="foreground-green">  "key_0": "alpha",</span><br /><span class="foreground-yellow">  "key_1": "beat",</span>   <span class="foreground-yellow">~</span>  <span class="foreground-yellow">  "key_1": "beta"</span><br /><span class="foreground-red">  "key_2": "gamma"</span>   <span class="foreground-red">x</span><br /><span class="foreground-green">}</span>                    <span class="foreground-green">=</span>  <span class="foreground-green">}</span><br /></code></pre>

<!--dinject range=end-->

You're not limited to visualising the differences as the same format as the input files. If you wanted to compare JSON files but visualise as YAML, for example, then add the `--out-format` argument:

```bash
differently a.json b.json \
  --in-format  json       \
  --out-format yaml
```

<!--dinject as=html host=terminal range=start-->

<style type="text/css">.thtml { --green: #0C0; --yellow: #CC0; --red: #C00; } .foreground-green { border-color: var(--green); color: var(--green); } .foreground-yellow { border-color: var(--yellow); color: var(--yellow); } .foreground-red { border-color: var(--red); color: var(--red); }</style><pre class="nohighlight thtml"><code class="thtml-code"><span class="foreground-green">key_0: alpha</span>  <span class="foreground-green">=</span>  <span class="foreground-green">key_0: alpha</span><br /><span class="foreground-yellow">key_1: beat</span>   <span class="foreground-yellow">~</span>  <span class="foreground-yellow">key_1: beta</span><br /><span class="foreground-red">key_2: gamma</span>  <span class="foreground-red">x</span><br /></code></pre>

<!--dinject range=end-->

You're not limited to comparing like-for-like, either. If you want to compare a JSON file to a YAML file, for example, then comma-separate the `--in-format` value to specify the format of each file:

```bash
differently a.json b.yaml \
  --in-format  json,yaml  \
  --out-format yaml
```

<!--dinject as=html host=terminal range=start-->

<style type="text/css">.thtml { --green: #0C0; --yellow: #CC0; --red: #C00; } .foreground-green { border-color: var(--green); color: var(--green); } .foreground-yellow { border-color: var(--yellow); color: var(--yellow); } .foreground-red { border-color: var(--red); color: var(--red); }</style><pre class="nohighlight thtml"><code class="thtml-code"><span class="foreground-green">key_0: alpha</span>  <span class="foreground-green">=</span>  <span class="foreground-green">key_0: alpha</span><br /><span class="foreground-yellow">key_1: beat</span>   <span class="foreground-yellow">~</span>  <span class="foreground-yellow">key_1: beta</span><br /><span class="foreground-red">key_2: gamma</span>  <span class="foreground-red">x</span><br /></code></pre>

<!--dinject range=end-->

## Python

The `differently` package provides a library of classes for handling different types of comparison:

- `StringDifferently` visualises the type of difference between strings
- `TextDifferently` visualises the differences between multi-line text bodies
- `ListDifferently` visualises the differences between lists of strings
- `JsonDifferently` visualises the differences between objects as JSON
- `YamlDifferently` visualises the differences between objects as YAML

For example, to visualise the differences between two objects as JSON:

```python
from differently import JsonDifferently

a = {
  "name": "Bobby Pringle",
  "books": [
    "Goose Attacks",
    "No More Cheese",
  ],
}

b = {
  "name": "Bobby Pringle",
  "books": [
    "Goose Attacks",
    "All My Cheeses",
    "No More Cheese",
  ],
}

diff = JsonDifferently(a, b)
print(diff)
```

<!--dinject as=html host=terminal range=start-->

<style type="text/css">.thtml { --green: #0C0; --yellow: #CC0; } .foreground-green { border-color: var(--green); color: var(--green); } .foreground-yellow { border-color: var(--yellow); color: var(--yellow); }</style><pre class="nohighlight thtml"><code class="thtml-code"><span class="foreground-green">{</span>                          <span class="foreground-green">=</span>  <span class="foreground-green">{</span><br /><span class="foreground-green">  "books": [</span>               <span class="foreground-green">=</span>  <span class="foreground-green">  "books": [</span><br /><span class="foreground-green">    "Goose Attacks",</span>       <span class="foreground-green">=</span>  <span class="foreground-green">    "Goose Attacks",</span><br />                           <span class="foreground-yellow">&gt;</span>  <span class="foreground-yellow">    "All My Cheeses",</span><br /><span class="foreground-green">    "No More Cheese"</span>       <span class="foreground-green">=</span>  <span class="foreground-green">    "No More Cheese"</span><br /><span class="foreground-green">  ],</span>                       <span class="foreground-green">=</span>  <span class="foreground-green">  ],</span><br /><span class="foreground-green">  "name": "Bobby Pringle"</span>  <span class="foreground-green">=</span>  <span class="foreground-green">  "name": "Bobby Pringle"</span><br /><span class="foreground-green">}</span>                          <span class="foreground-green">=</span>  <span class="foreground-green">}</span><br /></code></pre>

<!--dinject range=end-->

You can render the visualisation by just treating the instance as a string, but there's a `render()` function for writing directly to streams too.

## 1.0.0

The project is online at [github.com/cariad/differently](https://github.com/cariad/differently) under the MIT licence. Full documentation is available at [cariad.github.io/differently](https://cariad.github.io/differently/).

For the nerds amongst us, you might be interested to know that all the code samples in this post were executed and injected by `dinject`[^dinject] to ensure the code actually works and the example responses are accurate. The code for this article and the example input files are all in GitHub[^source].

[^dinject]: [Articles about dinject, unbuild.blog](https://unbuild.blog/tags/dinject)
[^source]: [2021-10-30-differently-1.0.0, github.com](https://github.com/cariad/unbuild.blog/blob/main/content/2021-10-30-differently-1.0.0)

Have fun!
