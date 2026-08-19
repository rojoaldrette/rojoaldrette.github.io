---
layout: fun
title: Pelis
section: fun
permalink: /fun/movies.html
---
<div class="items-list">
{%- assign movies = site.movies | sort: 'date' | reverse -%}
{%- for movie in movies -%}
  <a class="item-card" href="{{ movie.url | relative_url }}">
    <h3>{{ movie.title }}</h3>
    <p class="item-meta">{{ movie.director }} &middot; {{ movie.year }}</p>
    {%- if movie.rating %}<p class="stars">{% assign r = movie.rating %}{% for i in (1..r) %}&#9733;{% endfor %}</p>{%- endif %}
    <p class="item-review">{{ movie.excerpt | strip_html | truncatewords: 20 }}</p>
  </a>
{%- endfor -%}
</div>
