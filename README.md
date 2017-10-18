# HELLO!

{{ post.title }} ({{post.date | date: '%Y-%m-%d' }}) - ({{post.content | number_of_words}} words)
{% endfor %}
