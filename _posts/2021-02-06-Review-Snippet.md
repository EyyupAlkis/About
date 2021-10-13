---
layout: post
title: Getting better feedback on your blog posts
categories:
  - Web Development
  - Javascript
lang: en
---

A friend of mine asked me to review his new blog post.
I gave it a read and compiled some loose feedback, but I missed a way to easily annotate specific text passages!

<!--more-->

I did some digging online and found a tool called <a href="https://hypothes.is" target="_blank">Hypothesis</a>, which can be embedded into webpages.

This is now part of this website! It's activated by appending `#review` to a webpages URL.
Try it out, <a href="#review" target="_blank">open this page in review mode!</a>

If you like this, you can integrate it into your own site by including this snippet on your page:

```html
<script>
if (location.hash === "#review") {
  var el = document.createElement("script")
  el.src = "https://hypothes.is/embed.js"
  document.body.append(el)
}
</script>
```

You can now get even better feedback from your peers by sending them a review-mode link to your (unpublished) blog posts.
Yay! :D

> If this short tip was useful to you, <a href="https://twitter.com/skn0tt" target="_blank">let me know on Twitter</a>! ☺️

