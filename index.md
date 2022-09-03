---
layout: home
description: Recent Posts
limit: 5
---
{%- if site.posts.size > page.limit -%}
<div class="lead text-end">
  <a class="nav-link" href="{% link archive.md %}">More<span aria-hidden="true">&nbsp;&#x2192;&#xfe0e;</span></a>
</div>
{%- endif -%}
