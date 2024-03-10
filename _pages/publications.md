---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

{% if site.author.googlescholar %}
  <div class="wordwrap">You can also find my articles on my <a href="{{site.author.googlescholar}}">Google Scholar</a>.</div>
{% endif %}

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}

{% raw %}
**EMO-SUPERB: An In-depth Look at Speech Emotion Recognition**<br/>
    Haibin Wu, Huang-Cheng Chou, Kai-Wei Chang, Lucas Goncalves, <u>Jiawei Du<u>, Jyh-Shing Roger Jang, Chi-Chun Lee, Hung-Yi Lee<br/>
    *Preprint*<br/>
    [ [pdf](https://arxiv.org/abs/2402.13018) | [Webpage](https://emosuperb.github.io/) | [Github](https://github.com/EMOsuperb/EMO-SUPERB-submission)]
{% endraw %}
