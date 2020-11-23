---
title: "Hello"
layout: splash
permalink: /
hidden: true
header:
  overlay_color: "#5e616c"
  overlay_image: /images/wall.jpg
  actions:
    - label: "About me"
      url: "/about/"
excerpt: "Thanks for stopping by. Here you can learn more about me and what I'm up to. To get in touch, click the link below."
intro: 
  - excerpt: 'I use data to build strategies and solve problems. I use this page to share the new tools, languages, and methodologies I am continually trying to learn.'
feature_row:
  - image_path: /images/nyc_header_th.jpg
    alt: "customizable"
    title: "About Me"
    excerpt: "Here you'll find info about me and how to contact me."
    url: "/about/"
    btn_class: "btn--primary"
    btn_label: "About me"
  - image_path: /images/viz_th.png
    alt: "fully responsive"
    title: "View My Projects"
    excerpt: "Here I post my data analysis and visualization projects."
    url: "/project-posts/"
    btn_class: "btn--primary"
    btn_label: "Projects"
  - image_path: /images/strikezone_th.png
    alt: "Finding the Strike Zone"
    title: "Finding the Strike Zone"
    excerpt: "Check out my most recent post. I use machine learning to re-think the Strike Zone."
    url: "/strikezone/"
    btn_class: "btn--primary"
    btn_label: "Read More"
feature_row2:
  - image_path: /images/strikezone_th.png
    alt: "placeholder image 2"
    title: "Finding the Strike Zone"
    excerpt: 'Check out my most recent post about using machine learning to re-think the strikezone.'
    url: "/strikezone/"
    btn_class: "btn--primary"
    btn_label: "Read More"
---

{% include feature_row id="intro" type="center" %}

{% include feature_row %}

{% include feature_row id="feature_row2" type="left" %}
