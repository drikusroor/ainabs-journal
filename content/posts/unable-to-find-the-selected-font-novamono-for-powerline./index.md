---
title: "Unable to find the selected font \"NovaMono for Powerline\"."
date: 2023-01-11T09:49:00Z
categories:
  - "notebook"
tags:
  - "terminal"
  - "windows"
source: Notion
---

On one of my computers I use Windows Terminal with Git Bash and PowerShell as my default terminals. For these terminals I have customized the appearance in Windows Terminal, which is a pretty nice feature. For both terminals, I had configured the `NovaMono for Powerline` font, which I had previously installed to Windows.

On a certain day I suddenly started getting these messages while either one of the terminals was initializing:

`Unable to find the selected font "NovaMono for Powerline".`

I checked my Windows fonts in both the `Settings > Personalization > Fonts` and through `Control Panel\Appearance and Personalization\Fonts`. It appeared that the font was correctly installed.

Then I stumbled upon [a Github issue](https://github.com/microsoft/terminal/issues/9375) on which people seemed to be having the same problem. I got [the advice](https://github.com/microsoft/terminal/issues/9375#issuecomment-797136185) of dropping the original font file (`.ttf`) to the `Drag and drop to install` area of the aforementioned `Settings > Personalization > Fonts` configuration menu - even though my font was already listed there as an installed font.

However, that seemed to do the trick and now I can use my beloved font again. 👌

## tl;dr

### Error

`Unable to find the selected font "NovaMono for Powerline".`

### Solution

Drag & drop original `.ttf` font file to `Settings > Personalization > Fonts` drag & drop area to (re-)install.
