---
layout: page
title: "Hi, I'm Dean"
subtitle: R-Shiny Expert / Software Tool Builder / Extreme Traveller
css: "/css/index.css"
meta-title: "Dean Attali - R-Shiny consultant"
meta-description: "R-Shiny developer and consultant with a MSc in Bioinformatics and a Bachelor of Computer Science. Previously a software engineer at Google, IBM, and Wish.com."
bigimg:
  - "/img/big-imgs/costa-rica-house.jpeg" : "Montezuma, Costa Rica (2011)"
  - "/img/big-imgs/grouse-grind.jpeg" : "Vancouver, Canada (2014)"
  - "/img/big-imgs/hawaii1.jpeg" : "Kauai, HI, USA (2014)"
  - "/img/big-imgs/hawaii2.jpeg" : "Kauai, HI, USA (2014)"
  - "/img/big-imgs/hongkong-cliff-dive.jpeg" : "Hong Kong (2014)"
  - "/img/big-imgs/hongkong-infinity-pool.jpeg" : "Hong Kong (2014)"
  - "/img/big-imgs/israel-dive1.jpeg" : "Eilat, Israel (2013)"
  - "/img/big-imgs/israel-dive2.jpeg" : "Eilat, Israel (2013)"
  - "/img/big-imgs/israel-dive3.jpeg" : "Eilat, Israel (2013)"
  - "/img/big-imgs/laos-pond2.jpeg" : "Luang Prabang, Laos (2014)"
  - "/img/big-imgs/laos-pond1.jpeg" : "Luang Prabang, Laos (2014)"
  - "/img/big-imgs/laos-pond3.jpeg" : "Luang Prabang, Laos (2014)"
  - "/img/big-imgs/st-martin.jpeg" : "St Maarten (2014)"
  - "/img/big-imgs/tanzania.jpeg" : "Mt Kilimanjaro, Tanzania (2012)"
  - "/img/big-imgs/vietnam-beach.jpg" : "Mui Ne, Vietnam (2013)"
  - "/img/big-imgs/vietnam-climb.jpeg" : "Cat Ba, Vietnam (2013)"
  - "/img/big-imgs/vietnam-climb2.jpeg" : "Cat Ba, Vietnam (2013)" 
  - "/img/big-imgs/vietnam-dunes.jpeg" : "Mui Ne, Vietnam (2013)"
  - "/img/big-imgs/vietnam-dunes2.jpeg" : "Mui Ne, Vietnam (2013)"
  - "/img/big-imgs/vietnam-fruits.jpeg" : "Nha Trang, Vietnam (2013)"
  - "/img/big-imgs/vietnam-hat.jpeg" : "Hoi An, Vietnam (2013)"
  - "/img/big-imgs/vietnam-jump.jpeg" : "Mui Ne, Vietnam (2013)"
  - "/img/big-imgs/vietnam-ricefield.jpeg" : "Sapa, Vietnam (2013)"
  - "/img/big-imgs/vietnam-scooter.jpeg" : "Da Nang, Vietnam (2013)"
  - "/img/big-imgs/vietnam-walk.jpeg" : "Sapa, Vietnam (2013)"
  - "/img/big-imgs/california-skydive.jpeg" : "Davis, CA, USA (2008)"
  - "/img/big-imgs/california-surf.jpeg" : "Los Angeles, CA, USA (2008)"
  - "/img/big-imgs/california-surf2.jpeg" : "Los Angeles, CA, USA (2008)" 
  - "/img/big-imgs/california-surf3.jpeg" : "Santa Cruz, CA, USA (2009)"
  - "/img/big-imgs/costa-rica.jpeg" : "Arenal, Costa Rica (2011)"
  - "/img/big-imgs/rio-hanggliding.JPG" : "Rio de Janeiro, Brazil (2015)"  
  - "/img/big-imgs/hawaii-turtles.jpg" : "Oahu, HI, USA (2016)"  
---

<div class="list-filters">
  <a href="/" class="list-filter">All posts</a>
  <a href="/popular" class="list-filter">Most Popular</a>
  <span class="list-filter filter-selected">Tutorials</span>
  <a href="/tags" class="list-filter">Index</a>
</div>

<div class="posts-list">
  {% for post in site.tags.tutorial %}
  <article>
    <a class="post-preview" href="{{ post.url | prepend: site.baseurl }}">
	    <h2 class="post-title">{{ post.title }}</h2>
	
	    {% if post.subtitle %}
	    <h3 class="post-subtitle">
	      {{ post.subtitle }}
	    </h3>
	    {% endif %}
      <p class="post-meta">
        Posted on {{ post.date | date: "%B %-d, %Y" }}
      </p>

      <div class="post-entry">
        {{ post.content | truncatewords: 50 | strip_html | xml_escape}}
        <span href="{{ post.url | prepend: site.baseurl }}" class="post-read-more">[Read&nbsp;More]</span>
      </div>
    </a>  
   </article>
  {% endfor %}
</div>
