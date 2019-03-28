---
title: "Analyzing our collective Wikipedia browsing history"
date: 2019-03-24T15:15:28-04:00
categories: Projects
tags: ["graph-databases", "neo4j", "clickstream", "graphs", "network-analysis", "Python", "visualization", "D3", "vis.js"]
summary: An investigation of our collective browsing behaviors on Wikipedia.
draft: true
---

Since November 2017, Wikipedia has been regularly releasing monthly clickstream datasets that capture summarized page-to-page user visits to Wikipedia articles. This clickstream data captures how we browse Wikipedia.  

To maintain user privacy, the clickstream events (hops from one webpage to another) are added up across all users for the given month, and the clickstream events that add up to less than 10 are dropped. This means that there is no individual user information in this data, and that clickstream events that are so rare that they could potentially uniquely identify users have been removed. In addition, the publishers of the data do their best to filter any known spider traffic out of it.  

The resulting clickstream data is an anonymized version of our collective browsing history on Wikipedia, released in monthly batches. We can use data science techniques to explore what's in it.

So, let's see what we've been up to on Wikipedia in December 2018.  

> Why December 2018? These monthly clickstream datasets are quite large and can take a long time to process, so for practical purposes we'll start off with analyzing just one month's worth of the data. I've picked December 2018 because it was the most recent month available when I started working on this side project, but the same analyses can be done on any of the available English Wikipedia datasets (and with adjustments for language differences, for the several non-English language Wikipedia clickstream datasets available).


What did we do on Wikipedia?
---  

There are 6.88 billion clickstream events in the Wikipedia clickstream data for December 2018, reaching about 5.2 million unique Wikipedia articles, or about 90% of the 5.8 million Wikipedia articles in existence at the time. The clickstream data has been cut off to a traffic minimum of 10, so each of those 5.2 million articles was visited at least 10 times, and the 6.88 billion clickstream events in the dataset make up about 90% of the total 7.67 billion clickstream events to English Wikipedia in December 2018 ([source]((https://stats.wikimedia.org/v2/#/en.wikipedia.org/reading/total-page-views/normal|table|2017-03-01~2019-03-01|~total))).  

To see where all of this traffic tends to go on Wikipedia, we can check Wikipedia's [Topviews Analysis tool](https://tools.wmflabs.org/topviews/). According to it, the most viewed Wikipedia articles in December 2018 were:  

<img src="/images/topviews_analysis_tool_top10_dec2018.png" alt="Top 10 most viewed articles from the Topviews Analysis tool">
_Source: [Wikipedia's Topviews Analysis tool](https://tools.wmflabs.org/topviews/?project=en.wikipedia.org&platform=all-access&date=2018-12&excludes=)_  

The top viewed Wikipedia articles in December 2018 were mostly about recently released movies and famous people. The \#1 most viewed article was about [George H. W. Bush](https://en.wikipedia.org/wiki/George_H._W._Bush), who died on November 30, 2018.  

Using clickstream data, we can split the Wikipedia article views by traffic type. This way we can roughly see how people got to the article pages.  

> The visualization below is interactive. Click on a traffic type to see its stats.  

>Note: the traffic counts here are slightly lower than those from the Wikipedia’s Topviews Analysis tool. This difference is likely due to the traffic minimum cutoff.  

<iframe id="viz1" style="margin-left: -24%;" type="text/html" width="1200" height="940" src="/visualizations/analyzing-our-collective-wikipedia-browsing-history-viz-1" allowfullscreen frameborder="0">
</iframe>
<hr>

The breakdown of Wikipedia article views by traffic type shows that about 40% of the time we got to Wikipedia from online search results, and it looks like we've searched for nearly 60% of all English Wikipedia articles that were in existence as of December 2018. That's impressive, but not as impressive as the empty referer traffic, which has visited nearly 90% of the existing articles.  

Empty referer traffic could result from several scenarios, including entering the website URL into the browser address bar, opening a bookmarked webpage, setting a webpage as the browser's default page, various security measures, spiders and other automated browsing, etc. (see [this discussion on StackOverflow](https://stackoverflow.com/questions/6880659/in-what-cases-will-http-referer-be-empty) for more details). About half a billion, or 27%, of the 1.8 billion empty referer traffic went to the Wikipedia [Main Page](https://en.wikipedia.org/wiki/Main_Page) article, which seems like a good candidate for bookmarking, setting the page as browser default, or typing in the page into the browser address bar. The rest of the empty referer traffic is thinly spread across the 90% of existing acticles, with less than 1% of the empty referer traffic going to any one article. The wide reach of this remaining empty referer traffic is probably the result of automated article visits by spiders and bots.  

The next big chunk of online traffic to English Wikipedia articles comes from the Wikipedia articles themselves. About 25% of the December 2018 traffic to Wikipedia articles was users clicking on a link in a Wikipedia article to go to another Wikipedia article. Another 0.9% of the traffic was from users going from one Wikipedia article to another, but with no link from the former to the latter. According to the Wikipedia clickstream data's [documentation](https://meta.wikimedia.org/wiki/Research:Wikipedia_clickstream#Format), this could happen when users search from a Wikipedia article, or if they spoof their referer. For simplicity, we'll assume that these clickstream events are internal searches from Wikipedia article pages. Putting the links traffic and internal searches together, we see that 25.9% of Wikipedia traffic in December 2018 happened because people went from one Wikipedia article to another and kept on reading.  


Where did we go when we kept on reading?
---

For the quarter of Wikipedia traffic that is links and internal searches, the clickstream data consists of a list of every article-to-article traversal done by users in December 2018. If we model these article-to-article hops as a [graph](https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)), we can use [graph/network analytics](https://en.wikipedia.org/wiki/Network_science#Network_analysis) to see if there are any interesting patterns in these article-to-article connections. This way we can explore how we browse Wikipedia and where we tend to go when we follow Wikipedia article links or search for more content to read.  

<div class="flexbox-container">
  <div class="img-col-half">
    <img src="/images/en_1218_louvain_viz_1_copied_img2_small2.png" alt="Wikipdedia articles clickstream network">
  </div>
  <div class="img-col-half">
    Here is the article-to-article graph of our Wikipedia browsing history, trimmed down a little because it was too large to visualize whole. The little circles are Wikipedia articles, and the lines connecting them represent the article-to-article clickstream traffic. The graph looks like a hairball, which is <a href="http://www.ulib.iupui.edu/digitalscholarship/blog/networks-how-i-learned-stop-worrying-and-love-hairball">a technical term</a>.  

    <blockquote>
    <a href="https://upload.wikimedia.org/wikipedia/commons/4/46/English_Wikipedia_clickstream_communities_December_2018.png" target="_blank">Click here</a> for a higher resolution version of this graph visualization with article titles.  
    </blockquote>

    The colors in the graph represent clusters of articles grouped by traffic. These clusters of articles have more clickstream connections among each other than with the reset of the network. The article clusters were identified using a community detection algorithm, which has found 1,698 such clusters.  
  </div>
</div>

The hairball graph visualization does a great job to illustrate just how complex our browsing behaviors are, but it's very hard to see what's going on in it. To clean up the hairball, we can group the article clusters, a.k.a. communities, into article community nodes, and sum up the article-to-article traffic into community-to-community traffic.


TODO: talk about using NLP to labels the article communities here.


Below is the article community-to-community graph visualization. It is still a hairball of sorts, but it is now much easier to navigate.  

> The visualization below is interactive. Click the "Show legend" button for more information about the visualization notation. You can zoom in, drag the nodes around, or click on the article community nodes to see a community's stats card.  
At the bottom of each community stats card are listings of the top 5 most significant articles in the community, organized into tabs by a few selected metrics: top viewed articles, influencers and connectors. Click on the tabs to see the lists.


<iframe id="viz2" style="margin-left: -14%; margin-top: 1rem; margin-bottom: 1rem;" type="text/html" width="1000" height="700" src="/visualizations/analyzing-our-collective-wikipedia-browsing-history-viz-2" allowfullscreen frameborder="1">
</iframe>
<a href="/visualizations/analyzing-our-collective-wikipedia-browsing-history-viz-2" target="_blank">Click here for a full page version of this visualization.</a>



Focus on communities graph stats here.


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


Summary  
---  

About a quarter of traffic to Wikipedia articles was from internal browsing of the articles, and the remaining 3/4 came from outside of Wikipedia. The 3/4 of external traffic tells us how we typically got to wikipedia: mostly through search engines (about 60% of external traffic), and maybe about 10% of the time through bookmarks or some other direct way to the Wikipedia's Main Page. The remaining 30% of external traffic is an unclear mix of empty referer traffic, external website links, etc.  



Reference
---
 - Wikipedia clickstream datasets are available for download [here](https://dumps.wikimedia.org/other/clickstream/), along with their accompanying [documentation](https://dumps.wikimedia.org/other/clickstream/readme.html)
