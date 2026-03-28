---
layout: default
title: Practical AI
---

<style>
  :root {
    --primary: #2563eb;
    --primary-dark: #1e40af;
    --bg: #ffffff;
    --text: #1f2937;
    --text-muted: #6b7280;
    --border: #e5e7eb;
    --card-bg: #f9fafb;
  }

  * {
    box-sizing: border-box;
  }

  .home-wrapper {
    max-width: 800px;
    margin: 0 auto;
    padding: 40px 20px;
  }

  .hero {
    text-align: center;
    padding: 60px 20px;
    margin-bottom: 50px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    border-radius: 16px;
    color: white;
  }

  .hero h1 {
    font-size: 2.5rem;
    margin: 0 0 10px 0;
    font-weight: 700;
  }

  .hero p {
    font-size: 1.1rem;
    margin: 0 0 25px 0;
    opacity: 0.9;
  }

  .social-links {
    display: flex;
    justify-content: center;
    gap: 15px;
  }

  .social-link {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 10px 20px;
    background: rgba(255, 255, 255, 0.15);
    border-radius: 8px;
    color: white;
    text-decoration: none;
    font-weight: 500;
    transition: background 0.2s;
  }

  .social-link:hover {
    background: rgba(255, 255, 255, 0.25);
  }

  .posts-section h2 {
    font-size: 1.5rem;
    margin-bottom: 25px;
    padding-bottom: 10px;
    border-bottom: 2px solid var(--primary);
    display: inline-block;
  }

  .post-list {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  .post-item {
    background: var(--card-bg);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 25px;
    margin-bottom: 20px;
    transition: all 0.2s;
  }

  .post-item:hover {
    border-color: var(--primary);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  }

  .post-item h3 {
    margin: 0 0 10px 0;
  }

  .post-item h3 a {
    color: var(--text);
    text-decoration: none;
    font-size: 1.2rem;
  }

  .post-item h3 a:hover {
    color: var(--primary);
  }

  .post-date {
    color: var(--text-muted);
    font-size: 0.9rem;
    margin-bottom: 10px;
  }

  .post-excerpt {
    color: var(--text-muted);
    margin: 0;
  }
</style>

<div class="home-wrapper">
  <div class="hero">
    <h1>Practical AI</h1>
    <p>Building production RAG systems, document intelligence, and AI solutions.</p>
    <div class="social-links">
      <a href="https://www.linkedin.com/in/yining-shi-25194b14b" target="_blank" class="social-link">
        <svg width="18" height="18" viewBox="0 0 24 24" fill="currentColor">
          <path d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433c-1.144 0-2.063-.926-2.063-2.065 0-1.138.92-2.063 2.063-2.063 1.14 0 2.064.925 2.064 2.063 0 1.139-.925 2.065-2.064 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z"/>
        </svg>
        LinkedIn
      </a>
    </div>
  </div>

  <div class="posts-section">
    <h2>Articles</h2>
    <ul class="post-list">
      {% for post in site.posts %}
      <li class="post-item">
        <h3><a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a></h3>
        <div class="post-date">{{ post.date | date: "%Y-%m-%d" }}</div>
        {% if post.excerpt %}
        <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
        {% endif %}
      </li>
      {% endfor %}
    </ul>
  </div>
</div>
