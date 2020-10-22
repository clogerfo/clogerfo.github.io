---
title: "Welcome"
layout: splash
permalink: /
date: 2016-03-23T11:48:41-04:00
header:
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: /images/wall.jpg
  actions:
    - label: "About me"
      url: "/about/"
excerpt: "Thanks for stopping by. Here you can learn more about me and what I am up to. To get in touch, click the link below."
intro: 
  - excerpt: 'I enjoy using data to solve problems. and spend much of my time continuing to learn new tools, languages, and methodologies that will help me do so. Through my personal, academic, and professional projects I have learned that while working on a problem, you usually uncover more questions than answers.'
feature_row:
  - image_path: /images/nyc_header_th.jpg
    alt: "placeholder image 1"
    title: "About Me"
    excerpt: "Here you'll find info about me and how to contact me."
    url: "/about/"
    btn_label: "About me"
    btn_class: "btn--primary"
  - image_path: /images/viz_th.png
    alt: "placeholder image 2"
    title: "View My Projects"
    excerpt: "Here I post my data analysis and visualization projects."
    url: "/project-posts/"
    btn_label: "Read More"
    btn_class: "btn--primary"
  - image_path: /images/strikezone_th.png
    title: "Finding the Strikezone"
    excerpt: "Check out my most recent post. I use machine learning to re-think the strikezone."
    url: "/strikezone/"
    btn_label: "Read More"
    btn_class: "btn--primary"
feature_row2:
  - image_path: /images/strikezone_th.png
    alt: "placeholder image 2"
    title: "Placeholder Image Left Aligned"
    excerpt: 'Check out my most recent post about using machine learning to re-think the strikezone.'
    url: "/strikezone/"
    btn_label: "Read More"
    btn_class: "btn--primary"
feature_row3:
  - image_path: /images/viz_th.png
    alt: "placeholder image 2"
    title: "Visualizing Twitter's Reaction to a Hurricane"
    excerpt: 'Using Twitter data to visualize reactions to a hurricane.'
    url: "#test-link"
    btn_label: "Read More"
    btn_class: "btn--primary"
feature_row4:
  - image_path: /images/shiny_th.png
    alt: "placeholder image 2"
    title: "Analysis on the MLB Labor Market"
    excerpt: 'In this post I analyze Major League Baseball free agents and use different regression techniques to identify what factors explain the most of the contract a free agent player signs.'
    url: "#test-link"
    btn_label: "Read More"
    btn_class: "btn--primary"
---

{% include feature_row id="intro" type="center" %}

{% include feature_row %}

{% include feature_row id="feature_row2" type="left" %}

{% include feature_row id="feature_row3" type="left" %}

{% include feature_row id="feature_row4" type="left" %}
