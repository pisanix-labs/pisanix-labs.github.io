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
      <h1 class="brand">Pisani</h1>
      <p class="tagline">SRE, Backend e Go — confiabilidade, observabilidade e arquitetura de sistemas distribuídos.</p>
    </div>
    <nav class="nav" aria-label="Seções do portfólio">
      <a class="nav-link" href="#artigos">Artigos</a>
      <a class="nav-link" href="#projetos">Projetos</a>
      <a class="nav-link" href="#palestras">Palestras e workshops</a>
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
        <p>Tópicos práticos: SLIs/SLOs, observabilidade, Istio e arquitetura.</p>
      </header>
      <ul class="bullets">
        <li>SLIs, SLOs e Error Budgets na prática</li>
        <li>Observabilidade de sistemas (RED, USE, Golden Signals)</li>
        <li>Istio e malha de serviço</li>
      </ul>

      <div class="list">
        {% for t in site.data.talks %}
        <a class="list-item" href="{{ t.url }}" target="_blank" rel="noopener">
          <span class="list-title">{{ t.name }}</span>
          <span class="list-text">{{ t.description }}</span>
          <span class="list-cta">Ver</span>
        </a>
        {% endfor %}
      </div>
    </div>
  </section>
</main>

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
</script>
