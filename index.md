<ul class="post-list">
{% for post in site.posts %}
  <li><article>
    <span class="entry-date">
      <time datetime="{{ post.date | date_to_xmlschema }}">
        {{ post.date | date: "%Y-%m-%d" }}
      </time>
    </span>
    <a href="{{ site.url | append: post.url }}">{{ post.title }}</a>
  </article></li>
{% endfor %}
</ul>
