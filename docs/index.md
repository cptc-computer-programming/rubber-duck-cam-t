---
layout: default
title: Campus chatbot documentation
---

# Campus chatbot documentation

Setup and development guides for the campus chatbot.

{% assign repository_url = site.github.repository_url | default: 'https://github.com/cptc-computer-programming/rubber-duck-cam-t' %}

[Get started]({{ repository_url }}/blob/main/README.md) | [Browse the docs tree]({{ repository_url }}/tree/main/docs) | [Repository]({{ repository_url }})

## Documentation

Browse the guides below, listed by folder and filename.

{% assign documents = site.html_pages | sort: 'path' %}
<ul>
{% for document in documents %}
{% unless document.url == page.url or document.name == '404.html' %}
  <li>
    <a href="{{ document.url | relative_url | escape }}">{{ document.title | default: document.name | escape }}</a>
    <small>(<code>{{ document.path | escape }}</code>)</small>
  </li>
{% endunless %}
{% endfor %}
</ul>
