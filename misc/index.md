---
layout: home
---

<div class="index-content">
    <div class="section">
        <ul class="artical-cate">
            <li style="text-align:left;"><a href="/"><span>SDN</span></a></li>
            <li style="text-align:center;" class="on"><a href="/misc"><span>Misc</span></a></li>
        </ul>

        <div class="cate-bar"><span id="cateBar"></span></div>

        <ul class="artical-list">
        {% for post in site.categories.misc %}
            <li>
                <h2>
                    <a href="{{ post.url }}">{{ post.title }}</a>
                </h2>
                <div class="title-desc">{{ post.description }}</div>
            </li>
        {% endfor %}
        </ul>
    </div>
    <div class="aside" style="background-image:url('/image/background/ford.jpg')"></div>
</div>
