---
layout: fun
title: Videos
section: fun
permalink: /fun/videos.html
---

<div class="videos-page" id="videos-app">
  <div class="videos-intro">
    <p>Videos que me gustan, organizados por temas. Puedes explorar libremente o filtrar por cualquier nivel de la taxonomía.</p>
  </div>

  <div class="videos-layout">
    <aside class="videos-sidebar">
      <div class="videos-filter-group">
        <h2>Filtrar</h2>
        <button type="button" class="video-filter-clear" id="video-clear">Mostrar todos</button>
      </div>
      <div class="videos-filter-group">
        <h3>Calidad</h3>
        <label class="video-check"><input type="checkbox" data-featured-filter><span>★ 10/10</span></label>
      </div>
      <div class="videos-filter-group"><h3>Temas</h3><div id="video-tag-filters"></div></div>
      <div class="videos-filter-group"><h3>Canales</h3><div id="video-channel-filters"></div></div>
    </aside>

    <section class="videos-results">
      <div class="videos-toolbar">
        <input id="video-search" type="search" placeholder="Buscar videos..." aria-label="Buscar videos">
        <span id="video-count" class="video-count"></span>
      </div>
      <div id="video-list" class="videos-list"></div>
      <p id="video-empty" class="video-empty" hidden>No hay videos que coincidan con estos filtros.</p>
    </section>
  </div>

  <section class="recommended-channels">
    <h2>Canales que recomiendo</h2>
    <div class="channel-list">
      {% for channel in site.data.channels %}
        {% if channel.recommended != false %}
          <a class="channel-card" href="{{ channel.url }}" target="_blank" rel="noopener noreferrer">
            <h3>{{ channel.name }}</h3>
            <p>{{ channel.description }}</p>
            <span>YouTube &rarr;</span>
          </a>
        {% endif %}
      {% endfor %}
    </div>
  </section>
</div>

<script>
window.videoCatalog = {
  videos: {{ site.data.videos | jsonify }},
  tags: {{ site.data.video_tags | jsonify }},
  channels: {{ site.data.channels | jsonify }}
};

(function () {
  const data = window.videoCatalog;
  const videos = data.videos || [], tags = data.tags || [], channels = data.channels || [];
  const tagById = Object.fromEntries(tags.map(t => [t.id, t]));
  const channelById = Object.fromEntries(channels.map(c => [c.id, c]));
  const selectedTags = new Set(), selectedChannels = new Set();
  let featuredOnly = false, search = '';

  function ancestors(id) {
    const out = [id]; let current = tagById[id];
    while (current && current.parent) { out.push(current.parent); current = tagById[current.parent]; }
    return out;
  }
  const tagAncestors = {};
  videos.forEach(v => (v.tags || []).forEach(t => tagAncestors[t] = ancestors(t)));

  const tagFilters = document.getElementById('video-tag-filters');
  const channelFilters = document.getElementById('video-channel-filters');
  const list = document.getElementById('video-list');
  const count = document.getElementById('video-count');
  const empty = document.getElementById('video-empty');

  function makeTagTree(parent, depth) {
    return tags.filter(t => (t.parent || null) === parent).map(tag => {
      const wrap = document.createElement('div');
      wrap.className = 'video-tag-node'; wrap.style.setProperty('--tag-depth', depth);
      const label = document.createElement('label'); label.className = 'video-check';
      const input = document.createElement('input'); input.type = 'checkbox'; input.dataset.tag = tag.id;
      input.addEventListener('change', () => { input.checked ? selectedTags.add(tag.id) : selectedTags.delete(tag.id); render(); });
      const text = document.createElement('span'); text.textContent = tag.name;
      label.append(input, text); wrap.append(label);
      makeTagTree(tag.id, depth + 1).forEach(child => wrap.appendChild(child));
      return wrap;
    });
  }
  makeTagTree(null, 0).forEach(node => tagFilters.appendChild(node));

  channels.forEach(channel => {
    const label = document.createElement('label'); label.className = 'video-check';
    const input = document.createElement('input'); input.type = 'checkbox'; input.dataset.channel = channel.id;
    input.addEventListener('change', () => { input.checked ? selectedChannels.add(channel.id) : selectedChannels.delete(channel.id); render(); });
    const text = document.createElement('span'); text.textContent = channel.name;
    label.append(input, text); channelFilters.appendChild(label);
  });

  function matches(v) {
    const text = [v.title, v.review, v.channel, ...(v.tags || [])].join(' ').toLowerCase();
    if (search && !text.includes(search)) return false;
    if (featuredOnly && !v.featured) return false;
    if (selectedChannels.size && !selectedChannels.has(v.channel)) return false;
    for (const selected of selectedTags) {
      if (!(v.tags || []).some(t => (tagAncestors[t] || [t]).includes(selected))) return false;
    }
    return true;
  }

  function esc(v) { return String(v || '').replace(/[&<>'"]/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;',"'":'&#39;','"':'&quot;'}[c])); }

  function card(v) {
    const article = document.createElement('article'); article.className = 'video-card' + (v.featured ? ' is-featured' : '');
    const channel = channelById[v.channel], channelName = channel ? channel.name : v.channel;
    const tagsHtml = (v.tags || []).map(id => tagById[id] ? '<span class="tag">' + esc(tagById[id].name) + '</span>' : '').join('');
    article.innerHTML = `
      <div class="video-card-top"><div><h2>${esc(v.title)}</h2><p class="item-meta">${esc(channelName)}</p></div>${v.featured ? '<span class="video-featured">★ 10/10</span>' : ''}</div>
      <div class="video-tags">${tagsHtml}</div>
      ${v.review ? '<p class="item-review">' + esc(v.review) + '</p>' : ''}
      <a class="video-watch" href="${esc(v.url)}" target="_blank" rel="noopener noreferrer">Ver en YouTube &rarr;</a>`;
    return article;
  }

  function render() {
    const filtered = videos.filter(matches); list.innerHTML = '';
    filtered.forEach(v => list.appendChild(card(v)));
    count.textContent = filtered.length + (filtered.length === 1 ? ' video' : ' videos');
    empty.hidden = filtered.length !== 0;
  }

  document.getElementById('video-search').addEventListener('input', e => { search = e.target.value.trim().toLowerCase(); render(); });
  document.querySelector('[data-featured-filter]').addEventListener('change', e => { featuredOnly = e.target.checked; render(); });
  document.getElementById('video-clear').addEventListener('click', () => {
    selectedTags.clear(); selectedChannels.clear(); featuredOnly = false; search = '';
    document.getElementById('video-search').value = '';
    document.querySelector('[data-featured-filter]').checked = false;
    document.querySelectorAll('#video-tag-filters input, #video-channel-filters input').forEach(i => i.checked = false);
    render();
  });
  render();
})();
</script>
