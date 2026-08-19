---
layout: default
title: Inicio
---
<div class="landing">
  <h1>{{ site.title }}</h1>
  <p class="tagline">elige tu aventura</p>
  <div class="choices">
    <a class="choice choice-research" href="{{ '/research/' | relative_url }}">
      <span class="choice-title">Research</span>
      <span class="choice-sub">papers, notas, cosas serias</span>
    </a>
    <a class="choice choice-fun" href="{{ '/fun/' | relative_url }}">
      <span class="choice-title">Fun</span>
      <span class="choice-sub">libros, pelis, vibes</span>
    </a>
  </div>
</div>
