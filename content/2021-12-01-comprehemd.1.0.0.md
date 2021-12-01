---
date: 2021-12-01
title: CompreheMD 1.0.0
tags:
  - mdcode
  - python
---

Last month, I released `mdcode`; a Python package for parsing code blocks in Markdown documents.

Well, as my projects progressed, it turned out that I needed to parse more than just code blocks.

And so, I just replaced `mdcode` with **CompreheMD**: a Python package for parsing Markdown document blocks _period_.

<!--more-->

## Example

As a simple example, we can parse out all the blocks in my blog's [README.md](https://github.com/cariad/unbuild.blog/blob/main/README.md):

```python
from comprehemd import MarkdownParser

parser = MarkdownParser()

with open("README.md", "r") as fp:
    for block in parser.read(fp):
        print(block)
```

<!--dinject as=markdown fence=backticks host=shell range=start-->

```text
HeadingBlock (1): unbuild.blog
EmptyBlock
Block: Articles for [unbuild.blog](https://unbuild.blog).
EmptyBlock
HeadingBlock (2): Local authoring
EmptyBlock
CodeBlock (bash): hugo serve
```

<!--dinject range=end-->

If I don't care about empty lines then I can exclude them:

```python
from comprehemd import EmptyBlock, MarkdownParser

parser = MarkdownParser()

with open("README.md", "r") as fp:
    for block in parser.read(fp):
        if not isinstance(block, EmptyBlock):
            print(block)
```

<!--dinject as=markdown fence=backticks host=shell range=start-->

```text
HeadingBlock (1): unbuild.blog
Block: Articles for [unbuild.blog](https://unbuild.blog).
HeadingBlock (2): Local authoring
CodeBlock (bash): hugo serve
```

<!--dinject range=end-->

Likewise, perhaps I care only about headings:

```python
from comprehemd import HeadingBlock, MarkdownParser

parser = MarkdownParser()

with open("README.md", "r") as fp:
    for block in parser.read(fp):
        if isinstance(block, HeadingBlock):
            print(block)
```

<!--dinject as=markdown fence=backticks host=shell range=start-->

```text
HeadingBlock (1): unbuild.blog
HeadingBlock (2): Local authoring
```

<!--dinject range=end-->

Blocks have properties to expose their details:

```python
from comprehemd import HeadingBlock, MarkdownParser

parser = MarkdownParser()

with open("README.md", "r") as fp:
    for block in parser.read(fp):
        if isinstance(block, HeadingBlock):
            print(".level  ==", block.level)
            print(".source ==", block.source)
            print(".text   ==", block.text)
            print()
```

<!--dinject as=markdown fence=backticks host=shell range=start-->

```text
.level  == 1
.source == # unbuild.blog
.text   == unbuild.blog

.level  == 2
.source == ## Local authoring
.text   == Local authoring
```

<!--dinject range=end-->

## 1.0.0

CompreheMD can be installed via pip:

```bash
pip install comprehemd
```

The package is fully documented at [cariad.github.io/comprehemd](https://cariad.github.io/comprehemd) and released under the MIT licence at [github.com/cariad/comprehemd](https://github.com/cariad/comprehemd).

Have fun!
