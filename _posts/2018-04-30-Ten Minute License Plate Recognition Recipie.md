---
layout: post
title: Ten Minute License Plate Recognition Recipie
subtitle: Google is offering quite a lot with its Vision API.
gh-repo: AjinkyaGhadge/Google-Cloud-based-ALPR
gh-badge:
  - star
  - fork
  - follow
tags:
  - test
published: true
---
>How it struck me?
I had just completed a simple introductory course by Google on Coursera. After completing a B-Tech project in Image classification using deep learning, i was fascinated to see how powerful Google’s Vision API was. Of course, Microsoft, Amazon and some more companies (Clarifai) had their own versions of doing similar things, but I was broke and Google was generous.

During my time at @persistentsystems, in a hackathon, one of the projects was a license plate recognition system made in under a week, the team had used “tesseract-OCR”. They had successfully used it for Indian License Plates, which are quite different from European and North American plates.
>What I did?
Aha, I knew Google had OCR in its vision API. I knew I had $300 for my experiments, which I wanted to use before losing and most importantly I knew Regular Expressions.
Here is briefly what I did,
  [x] Got my API keys.
  [x] Wrote a Python script
  [x] Fine tuned the OCR output with Regular expressions to get License plate details.
I went on to deploy this also on a Raspberry Pi, and automated it to take snapshots using vehicle detection using OpenCV (Will post it with details). This was done in 10 days, but the first three steps, yes, you can do it in under 10 minutes.
>See it for yourself!
[Feel free to use it](https://github.com/AjinkyaGhadge/Google-Cloud-based-ALPR), I made a simple Wiki for it too.

Also, it would be totally awesome if more Regex can be added for other countries ANPR.

Liked it, cook something of your own with the free [Google Cloud Credit!](https://cloud.google.com/free/)
