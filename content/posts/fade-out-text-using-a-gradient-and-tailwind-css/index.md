---
title: "Fade out text using a gradient and Tailwind CSS"
date: 2023-03-13T21:40:00Z
categories:
  - "notebook"
tags:
  - "tailwindcss"
source: Notion
---

In many cases, we have reasons not to display the whole text of an article or page and we just want to show an excerpt. Sometimes we break off the text using ellipsis. But sometimes we might want to [fade out the text](https://css-tricks.com/text-fade-read-more/) . And sometimes we want to do that in Tailwind CSS. Is that possible? Yes it is.

## Tailwind CSS example

```html
<div class="mt-24 w-96 p-4 mx-auto rounded-lg shadow-lg overflow-hidden relative after:content-[''] after:absolute after:inset-x-0 after:bottom-0 after:h-16 after:bg-gradient-to-b after:from-transparent after:to-white">
	<h1 class="text-3xl mb-3 font-bold underline">
		Hello world!
	</h1>
	<p class="leading-snug">
		Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
	</p>
</div>
```

See also [this gist on Github](https://gist.github.com/drikusroor/aa48937c59b3d6123ca43ae9c660661d) .
