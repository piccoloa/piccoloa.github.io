---
layout: default
---

<div class="home">
  {% assign total = 0 %}
  {% for post in site.posts %}

    {% assign total = total | plus: 1 %}

  {% endfor %}
  <h1 class="page-heading">Recent Posts ({{ total }})</h1>

  <ul class="post-list">
    {% for post in site.posts %}
      <li>
        <h2>
          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
          <small>{{ post.date | date: "%B %e, %Y"}}</small>
        </h2>

        <h3>
        <div class="entry">
          {% if post.excerpt %}
          {{ post.excerpt }}
          {% else %}
          {{ post.content | truncatewords:30}}
          {% endif %}
        </div>        
        </h3>
        <a href="{{ site.baseurl }}{{ post.url }}" class="read-more">Read More</a>
        <!-- <script>
          img {
              display:block;
              margin:auto;
          }
        </script> -->
        {% if post.image %}<a href="{{ post.url | prepend: site.baseurl }}" style="display:block;margin:auto"><img src="{{ post.image }}" style="display:block;margin:auto" /></a>{% endif %}
      </li>
    {% endfor %}
  </ul>

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | prepend: site.baseurl }}">via RSS</a></p>

</div>
