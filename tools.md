---
layout: page
title: "工具"
description: "利刃 · 出鞘"
header-img: "//cdn.isheng.top/black.jpg"
putout: true
magazine: ture
tools: ture
color: #404040
sitemap:
  priority: "0.5"
  changefreq:
  lastmod: 2016-07-21
---

<div class="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1 team-members">
{% if site.tools %}
{% for tool in site.tools %}
<a class="team-member .col-lg-3 col-md-4 .col-sm-2" href="{{tool.href}}" target="_blank">
<div class="author-avatar" style="background-image: url({{tool.img}})"></div>
<h4>{{tool.name}}</h4>
<p>{{tool.description}}</p>
</a>
{% endfor %}
{% endif %}

<hr>

</div>
