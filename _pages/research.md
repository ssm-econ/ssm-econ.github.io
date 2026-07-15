---
layout: archive
title: "Research"
permalink: /research/
author_profile: true
---

{% if author.googlescholar %}
  You can also find my articles on <u><a href="{{author.googlescholar}}">my Google Scholar profile</a>.</u>
{% endif %}

{% include base_path %}

<div class="research-list">
{% assign sorted_research = site.research | sort: 'date' | reverse %}
{% for post in sorted_research %}
  <div class="research-entry">
    <h3 class="research-title"><a href="{{ post.url }}">{{ post.title }}</a></h3>
    {{ post.content }}
  </div>
{% endfor %}
</div>