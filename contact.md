---
layout: page
title: Contact
permalink: /contact/
---

<li>
  <a href="mailto:huynhok.uit@vnoss.org">
    <i class="fa fa-envelope color" style="color:grey"></i> VNOSS
  </a>
</li>


<li>
  <a href="mailto:severus@theslinux.org">
    <i class="fa fa-envelope" style="color:grey"></i> TheSLinux
  </a>
</li>

{% if site.github_username %}
  <li>
    <a target="_blank" href="https://github.com/{{ site.github_username }}">
      <i class="fa fa-github" style="color:grey"></i> NgoHuy
    </a>
  </li>
{% endif %}

{% if site.twitter_username %}
  <li>
    <a target="_blank" href="https://twitter.com/{{ site.twitter_username }}">
      <i class="fa fa-twitter" style="color:grey"></i> Ngo_Huy
    </a>
  </li>
{% endif %}

{% if site.linkedin_username %}
  <li>
    <a target="_blank" href="https://vn.linkedin.com/in/{{ site.linkedin_username }}">
      <i class="fa fa-linkedin" style="color:grey"></i> severus
    </a>
  </li>
{% endif %}
