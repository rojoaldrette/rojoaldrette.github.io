---
layout: fun
title: Libros
section: fun
permalink: /fun/books.html
---
<div class="items-list">
{%- assign books = site.books | sort: 'date' | reverse -%}
{%- for book in books -%}
  <a class="item-card" href="{{ book.url | relative_url }}">
    <h3>{{ book.title }}</h3>
    <p class="item-meta">{{ book.author }} &middot; {{ book.year }}</p>
    {%- if book.rating %}<p class="stars">{% assign r = book.rating %}{% for i in (1..r) %}&#9733;{% endfor %}</p>{%- endif %}
    <p class="item-review">{{ book.excerpt | strip_html | truncatewords: 20 }}</p>
  </a>
{%- endfor -%}
</div>
