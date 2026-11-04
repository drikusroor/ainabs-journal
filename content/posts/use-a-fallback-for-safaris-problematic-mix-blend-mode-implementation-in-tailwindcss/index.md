---
title: "Use a fallback for Safari’s problematic mix-blend-mode implementation, in TailwindCSS"
date: 2022-11-19T11:38:00Z
categories:
  - "notebook"
tags:
  - "css"
  - "tailwindcss"
source: Notion
featured_image: "https://www.notion.so/images/page-cover/met_john_singer_sargent_morocco.jpg"
---

Recently I had been working on a website that uses `mix-blend-mode: color`. I just found out it didn’t work on Safari. Apparently `color` is [not supported](https://caniuse.com/?search=mix-blend-mode) . I had to find a way to maintain this mix blend mode for non-Safari browsers, in which it worked fine, but override it in Safari.

Then I found this [overly verbose answer](https://stackoverflow.com/a/25975282/4496102) on Stackoverflow.com. There are 10+ examples to apply css to only specific Safari versions, but the one I picked was the following:

```css
/* Safari 7.1+ */

_::-webkit-full-page-media, _:future, :root .safari_only {

  color:#0000FF; 
  background-color:#CCCCCC; 

}
```

I only had to adjust it to my own situation. Of course I’m using TailwindCSS because Tailwind evangelist [Laurens](https://laurens.io/) told me to. In this case I’m using `soft-light`, which does seem to work on Safari.

```css
/* Fix mix-blend-mode color bug on Safari 7.1+ */
_::-webkit-full-page-media, _:future, :root .mix-blend-color {
    mix-blend-mode: soft-light !important;
}
```
