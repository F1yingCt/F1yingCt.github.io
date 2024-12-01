---
layout: mypost
title: Links
---

If u want me to add some of ur links, please contact me and leave a comment like this:

```
title：{{ site.title }}
description：{{ site.description }}
url：{{ site.domainUrl }}{{ site.baseurl }}
icon：{{ site.domainUrl }}{{ site.baseurl }}/static/img/logo.jpg
```

<ul>
  {%- for link in site.links %}
  <li>
    <p><a href="{{ link.url }}" title="{{ link.desc }}" target="_blank" >{{ link.title }}</a></p>
  </li>
  {%- endfor %}
</ul>
