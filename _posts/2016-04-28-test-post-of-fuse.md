---
published: true
---
fuse creates first post with jakyll.

![Image of Yaktocat](/images/stigmergy_dokk.png)

![fuse pattern for dokk](/images/test_pattern_fuse_1920x1080.png)


{% for post in site.posts %}	
    <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
    <p><small><strong>{{ post.date | date: "%B %e, %Y" }}</strong> . {{ post.category }} . <a href="http://erjjones.github.com{{ post.url }}#disqus_thread"></a></small></p>			
{% endfor %}	

### Title h3
## Title h2
