---
layout: blog
slug: purgecss-purging-ignore-comment
title: What to do if purgeCSS is purging code inside an ignore block
subtitle: A simple answer as to why your purgeCSS code is still purging you CSS/Tailwind code even though it is still inside an ignore block
date: 2020-02-13T7:00:00.000+00:00
category: tailwindCSS
---

After hours of wondering why my CSS was still being purged even though it was inside <mark>/* purge start ignore */</mark> and <mark>/* purgecss end ignore /*</mark>, I discovered a simple <a href="https://github.com/FullHuman/purgecss/issues/90#issuecomment-559647253" target="_blank">answer</a> on Github that I wanted to share.

Add a <mark>!</mark> so your ignore comments look like this

```css
/*! purgecss start ignore */
/*! purgecss end ignore */
```

This removes any comments in and around rules, selectors & declarations. But comments marked with <mark>!</mark> are kept by default.

Enjoy purging!
