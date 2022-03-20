<h2>{{ site.description | default: site.github.project_tagline }}</h2>
<p>by James Habben & John Lukach</p>

<ul>
  <li>
    {% for post in site.posts %}
      <a href="{{ post.url }}">{{ post.title }}</a>
    {% endfor %}
  </li>
</ul>