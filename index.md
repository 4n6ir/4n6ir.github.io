<h2>{{ site.description | default: site.github.project_tagline }}</h2>
<p>by James Habben & John Lukach</p>

{% for post in site.posts %}
  <a href="{{ post.url }}">{{ page.date | date: "%-d %B %Y" }} - {{ post.title }}</a>
{% endfor %}