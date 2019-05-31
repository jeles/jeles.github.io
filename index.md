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

**Welcome to The Oxbow**--a blog dedicated to data science, coding, and making insights beneath the surface. My name is James Eles. I'm a post-doctoral associate in Bioengineering at the University of Pittsburgh. My research is focused on understanding how medical brain implants like brain-computer interfaces and stimulation electrodes interact with brain tissue, but I am more generally passionate about using data to do good. 
<br />
<br />



Check out the [blog](http://jameseles.com/DataScience/) for the full post archive. Here's my most recent posts:
<br />
<br />


<div class="grid__wrapper">
  {% for post in site.posts limit:4 %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
