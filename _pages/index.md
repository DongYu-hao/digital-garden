---
layout: page
title: Home
id: home
permalink: /
---
<strong style="color:grey">Latest</strong>
{% assign latest_post = site.notes | sort: "last_modified_at_timestamp" | reverse | first %}
{% if latest_post %}
  <article class="latest-post-preview">
    <p class="post-title">
      <a href="{{ site.baseurl }}{{ latest_post.url }}">{{ latest_post.title }}</a>
    </p>
    <p class="post-meta">
      {{ latest_post.last_modified_at | date: "%B %d, %Y" }}
    </p>
    <div class="post-excerpt">
      {{ latest_post.content | strip_html | truncate: 120 }}
    </div>
  </article>
{% endif %}

<strong style="color:grey">Recently</strong>
<ul>
  {% assign recent_notes = site.notes | sort: "last_modified_at_timestamp" | reverse %}
  {% for note in recent_notes limit: 50 %}
    <li>
      {{ note.last_modified_at | date: "%Y-%m" }} — <a class="internal-link" href="{{ site.baseurl }}{{ note.url }}">{{ note.title }}</a>
    </li>
  {% endfor %}
</ul>

<style>
  .wrapper {
    max-width: 46em;
  }
</style>
