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

**Hi, I'm James!**--I'm a data scientist and neural engineer with 11 years of experience in building experiments, algorithms, and analytics for large data sets. As a doctoral and post-doctoral researcher, I innovated imaging and computer vision techniques to understand how medical implants integrate into a patient's body. As a data scientist in the software industry, I am developing deep learning systems for sensor, text, and imagery data to support decision making for federal customers.  

<br />
The best AI/ML tools work *with* people to augment their tasks. My motivation in data science is to understand this interface between humans and algorithms, and to create tools that maximize the human benefit from these artificial intelligence.

<br />
<br />


<div class="grid__wrapper">
  {% for post in site.posts limit:4 %}
    {% include archive-single.html type="grid" %}
  {% endfor %}
</div>
