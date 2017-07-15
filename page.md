---
layout: page
title: "专栏"
description: "集结一切，出发！"
header-img: "/cdn.isheng.top/blue.jpg"
putout: true
magazine: ture
color: "#0000FF"
sitemap:
  priority: "0.5"
  changefreq:
  lastmod: 2016-07-20
---


{% for page in site.pages %}
{% if page.special %}
<div class="post-preview">
	<a href="{{ page.url | prepend: site.baseurl }}">
	<div class="post-header-img" style="background-image: url('{{ site.baseurl }}/{% if page.header-img %}{{ page.header-img }}{% else %}{{ site.header-img }}{% endif %}')"></div>
	<div class="post-summary">
	<h2 class="post-title">
	  {{ page.title }}
	</h2>
	<h3 class="post-subtitle">
	{{ page.subtitle }}
	</h3>
	<div class="post-content-preview">
	 {{ page.content | strip_html | truncate:150 }}
	</div>
	    <p class="post-meta">
	    <div class="author-avatar" style="background-image:url('{{ site.baseurl }}/{% if page.author-img %}{{ page.author-img }}{% else %}{{ page.header-img }}{% endif %}')"></div>
	    <span class="post-author">{% if page.author %}{{ page.author }}{% else %}{{ site.title }}{% endif %} · </span>
	    <span class="post-data">{{ page.date | date: "%Y年%m月%d日" }}</span>
	    </p>
	</div>
	</a>
	</div>

<hr>
{% endif %}
{% endfor %}
