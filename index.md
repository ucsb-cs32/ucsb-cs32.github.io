---
title: UCSB CS32
---

# {{site.course_title}}

<div id="about" data-role="collapsible" data-collapsed="true" markdown="1">
<h2>About this course</h2>

A course taught in the [Dept. of Computer Science](http://www.cs.ucsb.edu) at
[UC Santa Barbara](http://www.ucsb.edu).

This site is maintained in this github repo: <{{site.github_url}}>.   If you are a CS department faculty member or TA that should have access to this page, contact Phill Conrad to request permission.

* [Catalog Description]({{site.catalog_desc_url}}) including pre-requisites

# Previous iterations of this course

* [Fall 2015 (Conrad, at foo wiki)](https://foo.cs.ucsb.edu/32wiki) 
* [Spring 2015 (Conrad, at foo wiki)](https://foo.cs.ucsb.edu/32wiki/index.php/Spring_2015)
* [Fall 2018 (R.K. Wang)](https://ucsb-cs32-f18.github.io)

</div><!-- about -->


<div id="textbooks" data-role="collapsible" data-collapsed="false">
  <h2>Textbooks</h2>
    <ul>
      {% assign textbooks = site.textbooks | sort: 'custom_sort_order' %}
      {% for textbook in textbooks %}
         <li {% if topic.indent %} class="indent" {% endif %}><a href="{{textbook.url}}">{{ textbook.title }}</a>&mdash;{{textbook.desc}}</li>
      {% endfor %}
    </ul>
</div>

<div id="topics" data-role="collapsible" data-collapsed="false">
  <h2>Topics</h2>
  <ul>
   {% for topic in site.topics %}
     <li {% if topic.indent %} class="indent" {% endif %}><a href="{{topic.url}}">{{ topic.topic }}</a>&mdash;{{topic.desc}}</li>
   {% endfor %}
  </ul>
</div>


<div id="resources" data-role="collapsible" data-collapsed="false">
  <h2>Resources</h2>
  <ul>
   {% for topic in site.resources %}
     <li {% if topic.indent %} class="indent" {% endif %}><a href="{{topic.url}}">{{ topic.topic }}</a>&mdash;{{topic.desc}}</li>
   {% endfor %}
  </ul>
</div>

