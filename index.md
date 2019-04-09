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

**Welcome to The Pothole**--a blog dedicated to data science, coding, and making insights beneath the surface. My name is James Eles. I'm a post-doctoral associate in Bionengineering at the University of Pittsburgh. My research is focused on understanding how medical brain implants like brain-computer interfaces and stimulation electrodes interact with brain tissue.
<br />
<br />
The brain is 3 pounds of fat and salt water. It's the structure of the organ that makes us people. Similarly, most data collected in the world is just unloved server space. My goal is to make The Pothole a place where data can come alive and maybe help people out.


Check out the [blog](http://jameseles.com/DataScience/) for the full post archive. Here's my most recent posts:
<br />
<br />


<div class="grid__wrapper">
  {% for post in site.posts limit:4 %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
