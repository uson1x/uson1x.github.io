---
layout: post
title: "INSANE: Building a PDF Size Reducer with One-Shot LLM Prompts"
description: "How I used o3-mini-high to build a web-based PDF file size reducer that solved my years-long pain point with macOS PDF compression"
date: 2025-02-07 09:30:00 +0300
categories: llm tools web-development
tags: pdf-tools claude llm web-app no-backend javascript
image: /assets/images/posts/pdf-converter-preview.png
---

ugh, this is INSANE

o3-mini (on high reasoning effort) just one-shotted my pain point I had for years:

Sometimes when I need to upload a PDF, my original PDF doesn't fit the maximum file size. macOS simply doesn't have a nice way to do it (Preview/Quick Actions don't work well for me).

So I asked o3-mini to do it:

1. Make it work in a web page, no backend needed (it did it flawlessly)
2. Add page editor on the left (did it perfectly)
3. Add a preview (here I iterated on the UX a bit)
4. Add a file size preview (done)

Now I have an HTML page which can do all that available from anywhere: [PDF File Converter](/pdf-converter/)

That's it. A few prompts and my years-long annoyance with PDF file sizes is solved. The whole thing runs in the browser, no server needed, files never leave your device, and it just works.

Pretty wild what you can build with LLMs these days.

<div class="note">
<strong>Try it yourself:</strong> Visit the <a href="/pdf-converter/">PDF File Converter</a> to compress your own PDFs.
</div>

<meta name="keywords" content="PDF compression, file size reducer, Claude AI, LLM tools, web development, JavaScript, browser-based tools, no backend, PDF editor">
