---
title: "Analyzing our collective Wikipedia browsing history"
date: 2019-03-24T15:15:28-04:00
categories: Projects
tags: ["graph-databases", "neo4j", "clickstream", "graphs", "network-analysis", "Python", "visualization", "D3", "vis.js"]
summary: An investigation of our collective browsing behaviors on Wikipedia.
draft: true
---

Since November 2017, Wikipedia has been regularly releasing monthly clickstream datasets that capture summarized page-to-page user visits to Wikipedia articles. This clickstream data captures how we browse Wikipedia.  
To maintain user privacy, the clickstream events (hops from one webpage to another) are added up across all users for the given month, and the clickstream events that add up to less than 10 are dropped. This means that there is no individual user information in this data, and that clickstream events that are so rare that they could potentially uniquely identify users have been removed. In addition, the publishers of the data filter any known spider traffic out of it. This filtering is not bulletproof, however, and some spider traffic may remain in the data.  
The resulting clickstream data is an anonymized version of our collective browsing history on Wikipedia, released in monthly batches. We can use data science techniques to explore what's in it.

So, let's see what we've been up to on Wikipedia in December 2018.  

> Why December 2018? These monthly clickstream datasets are quite large and can take a long time to process, so for practical purposes we'll start off with analyzing just one month's worth of the data. I've picked December 2018 because it was the most recent month available when I started working on this side project, but the same analyses can be done on any of the available English Wikipedia datasets (and with adjustments for language differences, for the several non-English language Wikipedia clickstream datasets available).


What do we do on Wikipedia?
---  

In addition to making its raw clickstream datasets available to the public, Wikipedia also has great publicly available tools for exploring its online traffic. One of them is this [Topviews Analysis tool](https://tools.wmflabs.org/topviews/), where users can look up the top viewed Wikipedia articles.  
According to the Topviews Analysis tool, the most viewed Wikipedia articles in December 2018 were:  

<img src="/images/topviews_analysis_tool_top10_dec2018.png" alt="Top 10 most viewed articles from the Topviews Analysis tool">
_Source: [Wikipedia's Topviews Analysis tool](https://tools.wmflabs.org/topviews/?project=en.wikipedia.org&platform=all-access&date=2018-12&excludes=)_  

The top viewed Wikipedia articles in December 2018 were mostly about recently released movies and famous people. The \#1 most viewed article was about [George H. W. Bush](https://en.wikipedia.org/wiki/George_H._W._Bush), who died on November 30, 2018.  

Using clickstream data, we can split the Wikipedia article views by traffic type. This way we can roughly see how people got to the article pages.  

> The visualization below is interactive. Click on a traffic type to see its stats.  

<iframe id="viz1" style="margin-left: -24%;" type="text/html" width="1200" height="940" src="/visualizations/analyzing-our-collective-wikipedia-browsing-history-viz-1" allowfullscreen frameborder="0">
</iframe>
<hr>

The breakdown of Wikipedia article views by traffic type shows that about 40% of the time we got to Wikipedia from online search results, and it looks like we've searched for nearly 60% of all English Wikipedia articles that were in existence as of December 2018.  
That is impressive, but not as impressive as the [empty referer](https://stackoverflow.com/questions/6880659/in-what-cases-will-http-referer-be-empty) traffic, which has visited nearly 90% of the existng articles. Empty referer traffic could result from several scenarios, including security measures, spiders and other automated browsing. The wide reach of the empty referer is likely the product of automated article visits by spiders and bots.  


Talk about links traffic, and about internal vs external browsing.  



Where do we go when we keep on reading?
---

Focus on graph analytics and community detection here.

Chart.
<div class="flexbox-container">
  <div class="img-col-half">
    <img src="/images/en_1218_louvain_viz_1_copied_img2_small2.png" alt="Wikipdedia articles clickstream network">
  </div>
  <div class="img-col-half">
    Because this graph is too large to load whole, for this visualization it is limited to nodes with ArticleRank > 3 and edges with traffic > 100.  
  </div>
</div>

<a href="https://upload.wikimedia.org/wikipedia/commons/4/46/English_Wikipedia_clickstream_communities_December_2018.png" target="_blank">Here is a higher definition version of this graph</a>, which can be explored in more detail by clicking on it to zoom in. Note that it has the same ArticleRank and traffic filters as the visualization above. It is just a zoomed in version with legible article node labels.



Bla bla bla. Focus on article graph stats here.

Chart.
<iframe id="viz2" style="margin-left: -14%;" type="text/html" width="1000" height="700" src="/visualizations/analyzing-our-collective-wikipedia-browsing-history-viz-2" allowfullscreen frameborder="1">
</iframe>
<a href="/visualizations/analyzing-our-collective-wikipedia-browsing-history-viz-2" target="_blank">Click here for a full page version of this visualization.</a>



Bla bla bla. Focus on communities graph stats here.


Highlighting the graph by topic terms.  


<div class="img-row">
  <div class="img-col-half">
    <img src="/images/comm_graph_highlights_13.png" alt="Community graph with highlights by topic terms">
    Topic terms selected: politic || elect || govern || diplomat || presid || feder || parliament || mayor || gubernatori || senat | communities found: 202  
  </div>
  <div class="img-col-half">

  </div>
</div>

Entertainment highlights  

<div class="img-row">
  <div class="img-col-third">
  </div>
  <div class="img-col-third">
    <img src="/images/comm_graph_highlights_3.png" alt="Community graph with highlights by topic terms">


      `film` - 90 communities
  </div>
  <div class="img-col-third">
    <img src="/images/comm_graph_highlights_4.png" alt="Community graph with highlights by topic terms">
    Topic terms selected: (tv && !(latvi)) || episod | communities found: 37   
  </div>
</div>

<div class="img-row">
  <div class="img-col-third">
  </div>
  <div class="img-col-third">
    <img src="/images/comm_graph_highlights_5.png" alt="Community graph with highlights by topic terms">
    Topic terms selected: music || song || singer || discograph | communities found: 147
  </div>
  <div class="img-col-third">
    <img src="/images/comm_graph_highlights_2.png" alt="Community graph with highlights by topic terms">
    `novel || book || literatur || author || writer` - 121 communities  
  </div>
</div>



Sports


<div class="img-row">
  <div class="img-col-third">
    <img src="/images/comm_graph_highlights_9.png" alt="Community graph with highlights by topic terms">
    Topic terms selected: sport || olympi || champion || athlet || team || swim || runn || footbal || soccer || basketbal || basebal || hockey || wrestl || tennis || golf || cricket || rugbi | communities found: 85
  </div>
  <div class="img-col-third">
    <img src="/images/comm_graph_highlights_10.png" alt="Community graph with highlights by topic terms">
    Topic terms selected: (footbal && team && !(nation)) || (footbal && (america || (unit && state) )) | communities found: 18
  </div>
  <div class="img-col-third">
    <img src="/images/comm_graph_highlights_11.png" alt="Community graph with highlights by topic terms">
    Topic terms selected: soccer || (associ && footbal) || futebol || (footbal && !( (footbal && team && !(nation)) || (footbal && (america || (unit && state) )) ) ) | communities found: 31  
  </div>
</div>




John VS Mary & Co.

<div class="img-row">
  <div class="img-col-third">
  </div>
  <div class="img-col-third">
    <img src="/images/comm_graph_highlights_16.png" alt="Community graph with highlights by topic terms">
    Topic terms selected: john | communities found: 73  
  </div>
  <div class="img-col-third">
    <img src="/images/comm_graph_highlights_17.png" alt="Community graph with highlights by topic terms">
    Topic terms selected: (mari && !(mariag || marin)) || anna || (anne && !(channel || branne || gannet)) || elizabeth | communities found: 48  

    ( just Mary/Maria returns 25 communities )
  </div>
</div>





Reference
---
 - Wikipedia clickstream datasets are available for download [here](https://dumps.wikimedia.org/other/clickstream/), along with their accompanying [documentation](https://dumps.wikimedia.org/other/clickstream/readme.html)
