---
title: "Theme tips"
permalink: /docs/theme-tips/
excerpt: "Some tips about this theme - minimal-mistakes"
modified: 2017-02-07T10:34:06
---

Building github page using [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/), there are some tips I can use.

{% include toc %}

### Create table of contents

Add `% include toc %` into `{...}` at the beginning of the `.md` files.

### Run Jekyll

It's my first time to use Jekyll to build a webpage. I'm just a beginner. The following codes are some really simple but really important codes I need to remember.

```bash
$ bundle exec jekyll serve
```

### Change font size

Change page content font size: go to `/_sass/_page.scss`, and find the following codes. `p` refers to the content font size.
```css
  p, li, dl {
    font-size: 0.8em;
  }
```

Change code font size: go to `/_sass/_syntax.scss`, and find following codes.
```css
div.highlighter-rouge,
figure.highlight {
  font-size: 0.5em;
```
