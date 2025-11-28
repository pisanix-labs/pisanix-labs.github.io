---
layout: home
title: Início
permalink: /
---

<!-- Carrega estilos personalizados -->
<link rel="stylesheet" href="/assets/css/custom.css">

<header class="hero" id="top">
  <div class="container">
    <div class="intro">
      <h1 class="brand">Olá, eu sou o Pisani 👋</h1>
      <p>
        Sou Tech lead e Especialista em Backend e Frontend, com experiência em 
        <strong>construção de sistemas web, observabilidade, cloud-native e práticas de engenharia confiáveis</strong>.
      </p>
      <p>
        Tenho paixão por compartilhar conhecimento e já publiquei artigos técnicos no 
        <a href="https://medium.com/@pisanix" target="_blank" rel="noopener">Medium</a>, 
        além de manter projetos exemplo no 
        <a href="https://github.com/pisanix-labs" target="_blank" rel="noopener">GitHub</a>.
      </p>
      <p>🚀 Meus principais focos hoje:</p>
      <ul>
        <li>Backend em Go e Node.js</li>
        <li>Kubernetes, Observabilidade e Cloud</li>
        <li>Desenvolvimento de sistemas distribuídos</li>
        <li>Engenharia de Confiabilidade de Sites (SRE)</li>
      </ul>
      <p>
        Aqui você encontra meus projetos, artigos e experiências que refletem meu trabalho e minha forma de pensar engenharia de software.
      </p>
    </div>
    <nav class="nav" aria-label="Seções do portfólio">
      <a class="nav-link" href="#artigos">Artigos</a>
      <a class="nav-link" href="#projetos">Projetos</a>
      <a class="nav-link" href="#palestras">Palestras e workshops</a>
      <a class="nav-link" href="#prompts">Prompts</a>
    </nav>
  </div>
  <div class="hero-bg"></div>
  <div class="hero-gradient"></div>
  <button class="skip" data-target="#artigos" aria-label="Pular para conteúdo">↓</button>
  <div class="hero-shadow"></div>
  </header>

<main id="conteudo">
  <!-- Artigos -->
  <section id="artigos" class="section reveal">
    <div class="container">
      <header class="section-head">
        <h2>Artigos</h2>
        <p>Conteúdo técnico em profundidade sobre Go, SRE e sistemas distribuídos.</p>
      </header>
      <div class="grid cards">
        {% for a in site.data.articles %}
        <article class="card">
          <div class="card-body">
            <h3 class="card-title"><a href="{{ a.url }}" target="_blank" rel="noopener">{{ a.title }}</a></h3>
            <p class="card-meta">{{ a.date | date: "%d %b %Y" }}</p>
          </div>
          <div class="card-actions">
            <a class="btn" href="{{ a.url }}" target="_blank" rel="noopener">Ler artigo</a>
          </div>
        </article>
        {% endfor %}
      </div>
    </div>
  </section>

  <!-- Projetos -->
  <section id="projetos" class="section alt reveal">
    <div class="container">
      <header class="section-head">
        <h2>Projetos</h2>
        <p>Repositórios e provas de conceito focados em confiabilidade e escala.</p>
      </header>
      <div class="grid cards">
        {% for p in site.data.projects %}
        <article class="card">
          <div class="card-body">
            <h3 class="card-title"><a href="{{ p.url }}" target="_blank" rel="noopener">{{ p.name }}</a></h3>
            <p class="card-text">{{ p.description }}</p>
          </div>
          <div class="card-actions">
            <a class="btn" href="{{ p.url }}" target="_blank" rel="noopener">Abrir no GitHub</a>
          </div>
        </article>
        {% endfor %}
      </div>
    </div>
  </section>

  <!-- Palestras -->
  <section id="palestras" class="section reveal">
    <div class="container">
      <header class="section-head">
        <h2>Palestras e workshops</h2>
      </header>
      <div class="grid videos">
        {% for t in site.data.talks %}
        <article class="video-card" data-url="{{ t.url }}">
          <div class="video-thumb" role="button" aria-label="Reproduzir: {{ t.name }}">
            <span class="badge provider">Video</span>
            <span class="play" aria-hidden="true"></span>
          </div>
          <div class="video-info">
            <h3 class="video-title">{{ t.name }}</h3>
            <p class="video-text">{{ t.description }}</p>
          </div>
        </article>
        {% endfor %}
      </div>
    </div>
  </section>

  <!-- Prompts -->
  <section id="prompts" class="section alt reveal">
    <div class="container">
      <header class="section-head">
        <h2>Prompts</h2>
        <p>Prompts úteis que utilizo no meu dia a dia.</p>
      </header>
      {% assign prompt_files = site.static_files | sort: "relative_path" %}
      {% capture has_prompts %}
        {% for file in prompt_files %}
          {% if file.relative_path contains '/prompts/' and file.extname == '.md' %}1{% break %}{% endif %}
        {% endfor %}
      {% endcapture %}
      {% if has_prompts contains '1' %}
      <div class="grid cards">
        <article class="card prompt-card">
          <div class="card-body">
            <p class="prompt-chip">Prompts</p>
            <h3 class="card-title">Coleções</h3>
            <p class="card-text">Abra o prompt em uma nova aba para copiar ou usar direto no seu fluxo.</p>
            <div class="list">
              {% for file in prompt_files %}
              {% if file.relative_path contains '/prompts/' and file.extname == '.md' %}
              {% assign prompt_title = file.name | split: '.' | first | replace: '_', ' ' | replace: '-', ' ' | capitalize %}
              {% assign parts = file.relative_path | split: '/' %}
              {% assign folder = parts[2] | default: 'prompts' %}
              <a class="list-item" href="{{ file.url }}" target="_blank" rel="noopener">
                <span class="prompt-chip">{{ folder }}</span>
                <span class="list-title">{{ prompt_title }}</span>
                <span class="list-cta">Abrir</span>
                <span class="list-text">{{ file.name }}</span>
              </a>
              {% endif %}
              {% endfor %}
            </div>
          </div>
        </article>
      </div>
      {% else %}
      <p class="card-text">Nenhum prompt encontrado em <code>/prompts</code> ainda.</p>
      {% endif %}
    </div>
  </section>
</main>

<!-- Modal de vídeo -->
<div class="modal" id="video-modal" aria-hidden="true">
  <div class="modal-backdrop" data-close></div>
  <div class="modal-dialog" role="dialog" aria-modal="true" aria-label="Reprodutor de vídeo">
    <button class="modal-close" data-close aria-label="Fechar">×</button>
    <div class="modal-content"></div>
  </div>
  <div class="modal-gradient"></div>
</div>

<script>
  // Smooth scroll for internal links
  const links = document.querySelectorAll('a[href^="#"], .skip');
  links.forEach((el) => el.addEventListener('click', (e) => {
    const target = el.getAttribute('data-target') || el.getAttribute('href');
    if (!target || target === '#') return;
    const to = document.querySelector(target);
    if (!to) return;
    e.preventDefault();
    to.scrollIntoView({ behavior: 'smooth', block: 'start' });
    history.replaceState(null, '', target);
  }));

  // Reveal on scroll
  const io = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) entry.target.classList.add('visible');
    });
  }, { threshold: 0.12 });
  document.querySelectorAll('.reveal').forEach((el) => io.observe(el));

  // Active nav highlight
  const sections = Array.from(document.querySelectorAll('section[id]'));
  const nav = document.querySelectorAll('.nav-link');
  const navById = Object.fromEntries(Array.from(nav).map(a => [a.getAttribute('href').slice(1), a]));
  const activeObserver = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      const id = entry.target.id;
      if (entry.isIntersecting) {
        nav.forEach(a => a.classList.remove('active'));
        if (navById[id]) navById[id].classList.add('active');
      }
    })
  }, { rootMargin: '-40% 0px -55% 0px', threshold: 0.01 });
  sections.forEach((s) => activeObserver.observe(s));

  // Video previews (YouTube + fallback for LinkedIn)
  function ytIdFromUrl(u) {
    try {
      const url = new URL(u);
      if (url.hostname.includes('youtu')) {
        if (url.searchParams.get('v')) return url.searchParams.get('v');
        const parts = url.pathname.split('/').filter(Boolean);
        if (parts[0] === 'embed' || url.hostname === 'youtu.be') return parts.pop();
      }
    } catch (_) {}
    return null;
  }

  // Modal helpers
  const modal = document.getElementById('video-modal');
  const modalContent = modal.querySelector('.modal-content');
  const closeModal = () => {
    modal.setAttribute('aria-hidden', 'true');
    modal.classList.remove('open');
    document.body.classList.remove('modal-open');
    modalContent.innerHTML = '';
  };
  const openModal = (node) => {
    modalContent.innerHTML = '';
    modalContent.appendChild(node);
    modal.setAttribute('aria-hidden', 'false');
    modal.classList.add('open');
    document.body.classList.add('modal-open');
    modal.querySelector('.modal-close').focus({ preventScroll: true });
  };
  modal.addEventListener('click', (e) => { if (e.target.hasAttribute('data-close')) closeModal(); });
  document.addEventListener('keydown', (e) => { if (e.key === 'Escape') closeModal(); });

  // Video cards behavior
  document.querySelectorAll('.video-card').forEach((card) => {
    const href = card.getAttribute('data-url');
    let provider = 'link';
    let id = ytIdFromUrl(href);
    const thumb = card.querySelector('.video-thumb');
    const badge = thumb.querySelector('.badge');

    try {
      const url = new URL(href);
      if (url.hostname.includes('youtu')) provider = 'youtube';
      else if (url.hostname.includes('linkedin')) provider = 'linkedin';
      else provider = url.hostname.replace('www.', '');
    } catch (_) {}

    if (provider === 'youtube' && id) {
      thumb.style.backgroundImage = `url(https://i.ytimg.com/vi/${id}/hqdefault.jpg)`;
      badge.textContent = 'YouTube';
      thumb.addEventListener('click', () => {
        const wrap = document.createElement('div');
        wrap.className = 'modal-video';
        const iframe = document.createElement('iframe');
        iframe.src = `https://www.youtube.com/embed/${id}?autoplay=1`;
        iframe.title = 'YouTube video player';
        iframe.setAttribute('allowfullscreen', '');
        iframe.setAttribute('allow', 'accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share');
        wrap.appendChild(iframe);
        openModal(wrap);
      });
    } else if (provider === 'linkedin') {
      badge.textContent = 'LinkedIn';
      thumb.classList.add('linkedin');
      thumb.addEventListener('click', () => {
        const box = document.createElement('div');
        box.className = 'modal-fallback linkedin';
        box.innerHTML = `
          <p>Este conteúdo é exibido no LinkedIn.</p>
          <a class="btn" href="${href}" target="_blank" rel="noopener">Abrir no LinkedIn</a>
        `;
        openModal(box);
      });
    } else {
      badge.textContent = provider;
      thumb.addEventListener('click', () => window.open(href, '_blank'));
    }
  });
</script>
