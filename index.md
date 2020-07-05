---
title: "<br /> <br /> <br /> <br /> "
layout: splash
permalink: /
author_profile: false
comments: false
share: false
classes: narrow

header:
  #overlay_color: "#000"
  #overlay_filter: "0.2"
  #overlay_image: /assets/images/banner.png
  image: /assets/images/bannertrunc.png
  title: ''
  type: "center"

excerpt: " <br /> <br /> "
---
<a name="about"></a>

**Welcome to jameseles.com!**--I'm an NLP-focused data scientist and former neural engineer working on federal contracting problems. This [blog](http://jameseles.com/DataScience/) is a place to explore interesting data science problems out in the world. Enjoy!
<br />
<br />


<div class="grid__wrapper">
  {% for post in site.posts limit:4 %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
