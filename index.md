---
title: Latihan Azure OpenAI
permalink: index.html
layout: home
---

# Latihan Azure OpenAI

Latihan berikut dirancang untuk mendukung modul di [Microsoft Learn](https://learn.microsoft.com/training/browse/?terms=OpenAI).


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
