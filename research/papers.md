---
layout: research
title: Papers
section: research
permalink: /research/papers.html
---
Papers que he leído, con reseña completa en su propia página. La metadata (`tags`, `year`, `date`) queda lista para filtrar/ordenar por tema más adelante.

<div class="papers-list">
{%- assign papers = site.papers | sort: 'date' | reverse -%}
{%- for paper in papers -%}
  <article class="paper-entry">
    <h3><a href="{{ paper.url | relative_url }}">{{ paper.title }}</a></h3>
    <p class="paper-meta">
      {{ paper.authors }} &middot; {{ paper.year }}
      {%- if paper.tags %} &middot;
        {%- for tag in paper.tags %} <span class="tag">{{ tag }}</span>{%- endfor -%}
      {%- endif %}
    </p>
    <p class="paper-review">{{ paper.excerpt | strip_html | truncatewords: 35 }}</p>
    <a class="read-more" href="{{ paper.url | relative_url }}">leer reseña completa &rarr;</a>
  </article>
{%- endfor -%}
</div>
