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

**Welcome to jameseles.com!**--I'm an NLP-focused data scientist and neural engineer working to explore the world of data. I'm using this blog as a place to try out new ideas and (hopefully) find some interesting applications for data science tools.
<br />
Outside of that, I am a potato chip enthusiast and fan of all things to do with the City of Pittsburgh.

<br />
<br />


<div class="grid__wrapper">
  {% for post in site.posts limit:4 %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
