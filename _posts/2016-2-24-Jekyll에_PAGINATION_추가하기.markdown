---
layout: post
comments: true
title:  "Jekyll에 Pagenation 추가 하기"
date: 2016-2-24
---

Jekyll 3.0부터는 pagenate를 제공한다.

<ol>
<li>
_config.xml을 열어 아래와 같이 추가한다.

{% highlight shell %}
paginate: 3
paginate_path: "/blog/page:num/"
gems: [jekyll-paginate]
{% endhighlight %}
</li>
<li>
index.html 파일에 아래 코드를 삽입한다.
{% highlight html %}
 <!-- This loops through the paginated posts -->
    {{"{% for post in paginator.posts "}}%}
    <h1>
        <a href="{{"{{ post.url "}}}}">
            {{"{{ post.title "}}}}
        </a>
    </h1>
    <p class="author">
        <span class="date">
            {{"{{ post.date "}}}}
        </span>
    </p>
    <div class="content">
        {{"{{ post.content "}}}}
    </div>
    {{"{% endfor "}}%}
    <!-- Pagination links -->
    <div class="pagination">
        {{"{% if paginator.previous_page "}}%}
        <a href="{{"{{ paginator.previous_page_path "}}}}" class="previous">
            Previous
        </a>
        {{"{% else "}}%}
        <span class="previous">
            Previous
        </span>
        {{"{% endif "}}%}
        <span class="page_number ">
            Page: {{"{{ paginator.page "}}}} of {{"{{ paginator.total_pages "}}}}
        </span>
        {{"{% if paginator.next_page "}}%}
        <a href="{{"{{ paginator.next_page_path "}}}}" class="next">
            Next
        </a>
        {{"{% else "}}%}
        <span class="next ">
            Next
        </span>
        {{"{% endif "}}%}
    </div>
    {{"{% if paginator.total_pages > 1 "}}%}
    <div class="pagination">
        {{"{% if paginator.previous_page "}}%}
        <a href="{{"{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' "}}}}">
            &laquo; Prev
        </a>
        {{"{% else "}}%}
        <span>
            &laquo; Prev
        </span>
        {{"{% endif "}}%}
        {{"{% for page in (1..paginator.total_pages) "}}%}
        {{"{% if page == paginator.page "}}%}
        <em>
            {{"{{ page "}}}}
        </em>
        {{"{% elsif page == 1 "}}%}
        <a href="{{"{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' "}}}}">
            {{"{{ page "}}}}
        </a>
        {{"{% else "}}%}
        <a href="{{"{{ site.paginate_path | prepend: site.baseurl | replace: '//', '/' | replace: ':num', page "}}}}">
            {{"{{ page "}}}}
        </a>
        {{"{% endif "}}%}
        {{"{% endfor "}}%}
        {{"{% if paginator.next_page "}}%}
        <a href="{{"{{ paginator.next_page_path | prepend: site.baseurl | replace: '//', '/' "}}}}">
            Next &raquo;
        </a>
        {{"{% else "}}%}
        <span>
            Next &raquo;
        </span>
        {{"{% endif "}}%}
    </div>
    {{"{% endif "}}%}
{% endhighlight %}
</li>
<li>
jekyll을 build 한다.
{% highlight shell %}
$ jekyll build
{% endhighlight %}
위와 같이 빌드하면 /blog 디렉토리 아래에 <code>/blog/page1</code>, <code>/blog/page2</code> 와 같이 <code>/blog/pageN</code>의 디렉토리가 생성되고
각 디렉토리 안에 index.html이 생성된다.
index.html 안에는 각 페이지별로 보여질 마크업들이 생성되어 있다.
</li>
</ol>

## 참고
<ul>
    <li><a href="https://teamtreehouse.com/community/jekyllpaginate-gem">https://teamtreehouse.com/community/jekyllpaginate-gem</a></li>
    <li><a href="http://jekyllrb.com/docs/pagination/">http://jekyllrb.com/docs/pagination/</a></li>
</ol>


