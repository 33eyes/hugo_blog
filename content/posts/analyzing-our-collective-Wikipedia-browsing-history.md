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

The next big chunk of our online traffic to Wikipedia articles comes from the Wikipedia articles themselves. About 25% of the December 2018 traffic to Wikipedia articles consisted of users clicking on a link in a Wikipedia article to go to another Wikipedia article. Another 0.9% of the traffic was from users going from one Wikipedia article to another, but with no link from the former to the latter. According to the Wikipedia clickstream data's [documentation](https://meta.wikimedia.org/wiki/Research:Wikipedia_clickstream#Format), this could happen when users search from a Wikipedia article's page, or if they spoof their referer. For simplicity, we'll assume that these clickstream events are internal searches from Wikipedia article pages. Putting the links traffic and internal searches together, we see that 25.9% of Wikipedia traffic in December 2018 happened because people went from one Wikipedia article to another and kept on reading.  


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

In the
<a href="https://upload.wikimedia.org/wikipedia/commons/4/46/English_Wikipedia_clickstream_communities_December_2018.png" target="_blank">high-res version of the article-to-article graph above</a>, it's possible to look around an article community and get a sense of what it is about by reading the article titles. When grouping the article communities into community nodes, we lose that ability to see the individual article titles within a community, which makes it hard to tell what the article communities are about. To remedy that, we can use natural language processing techniques to extract key terms from the article titles within communities. We'll use a technique called [bag-of-words](https://en.wikipedia.org/wiki/Bag-of-words_model) to extract the top 10 most frequent [lemmatized](https://nlp.stanford.edu/IR-book/html/htmledition/stemming-and-lemmatization-1.html) words used in article titles within each community. These lists of top 10 lemmatized words for each article community can give us a rough idea of what each communty is about. We'll also use a technique called [named entity recognition](https://en.wikipedia.org/wiki/Named-entity_recognition) to extract the top 5 named entities for each community. This will tell us whether an article community mostly contains articles about people, places, organizations, or something else.   



The result is the article community-to-community graph shown in the visualization below. It is still a hairball of sorts, but it is now much easier to navigate. And since the aggregated communities are less complex to visualize, we can display all 1,698 of them.

> The visualization below is interactive. Click the "Show legend" button for more information about the visualization notation. Zoom in, drag the article community nodes around, or click on one of them to see its community stats card.  
At the bottom of each community stats card are listings of the top 5 most significant articles in the community, organized into tabs by a few selected metrics: top viewed articles, influencers and connectors. Click on the tabs to see the lists.  


<iframe id="viz2" style="margin-left: -14%; margin-top: 1rem; margin-bottom: 1rem;" type="text/html" width="1000" height="700" src="/visualizations/analyzing-our-collective-wikipedia-browsing-history-viz-2" allowfullscreen frameborder="1">
</iframe>
<a href="/visualizations/analyzing-our-collective-wikipedia-browsing-history-viz-2" target="_blank">Click here for a full page version of this visualization.</a>

Visualizing our Wikipedia browsing history this way, we can see some patterns emerge.  

There are 7 major article communities that can be roughly described by the following topics:  
 **- Community id 3**: current events, politics and famous people relevant to the United States  
**- Community id 7**: religion, literature, history and culture  
**- Community id 4**: movies, tv and actors  
**- Community id 10**: the United States  
**- Community id 5**: music, musicians and discographies  
**- Community id 1**: software and tech  
**- Community id 2**: health, biology and sexuality  

>You can look up any of these article communities by plugging their ids into the search box in the visualization above.  

The 7 largest article communities above cover the topics one would expect to see, but if we take a look at the smaller communities, the topics get a bit more interesting. Towards the bottom of the graph visualization, we have a cluster of medium-sized communities, which seem to have lots of search traffic between each other. Many of them are focused on specific sports, hobbies and interests. For example, we've got a wrestling/boxing community (id 15), a chess community (id 20), a Tolkien community (id 46), an Indian movies community (id 9), a trains community (id 0), and so on.
And then we have a big cloud of very small article communities, where each of those tiny article communities focuses on a very narrow topic, like a specific book or event.  

An interesting feature of the topics of these article communities is that many of them are combinations of multiple themes, and many topic words span multiple communities. For example, while community 7 is a combination of religion, literature, history and culture, topic terms related to literature can be found across many other article communities.  

To explore these community topic patterns a little, we can search the article community topics for specific terms and highlight the communities that match.  

Let's start with everyone's favorite topic.

### Politics  

<div class="img-row">
  <div class="img-col-half">
    <img src="/images/comm_graph_highlights_13.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 202</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection:
      <br>
      politic || elect || govern || diplomat || presid || feder || parliament || mayor || gubernatori || senat
    </div>
  </div>
  <div class="img-col-half">
    Politics is a key topic in the largest article community in our Wikipedia browsing history network. But it is also present in the topic terms of many small and relatively isolated communities.  
    <blockquote>
      In this and subsequent visualizations, the pink highlights show article communities that matched the chosen topic terms.
      <br><br>
      <strong>WARNING:</strong> These highlights are meant to show how various topics spread and overlap across large and interconnected communities versus those that are relatively small and isolated. These highlights simply illustrate in broad strokes the complexity of topics we read about on Wikipedia.  
      They are not meant to precisely show a topic term distribution. Doing so would call for a more in-depth NLP analysis and a more precise topic term matching. If a community is not highlighted for a particular topic term, it could still contain matching articles for that term, but they were not frequent enough to be part of the top 10 terms for that community.
    </blockquote>
    Many of the small politics-themed communities are about various past local elections around the world. Those of them located to the bottom left of the largest politics node are mainly about North American past local elections.
  </div>
</div>

The visualization above shows not the quantity of politics-themed articles read on Wikipedia, but rather the **spread of the topic** of politics across both a very large and highly interconnected article community and a multitude of very small and isolated communities.  

> For a closer look at the highlighted article communities, <a href="/visualizations/analyzing-our-collective-wikipedia-browsing-history-viz-3" target="_blank">here is the communities graph visualization with highlighting</a>. To highlight the communities with political topics, copy the topic terms selection listed under the politics graph visualization, and paste it into the highlighter prompt.  

> You can also try highlighting your own topic terms in the graph. Just make sure to read the highlighter search prompt instructions.

<br><br>


Let's try highlighting a few more popular topic terms.  

### Movies vs TV  

<div class="img-row">
  <div class="img-col-third">
    <h2>Movies</h2>
    <img src="/images/comm_graph_highlights_3.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 90</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection: film
      </div>
  </div>
  <div class="img-col-third">
    <h2>TV</h2>
    <img src="/images/comm_graph_highlights_4.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 37</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection: (tv && !(latvi)) || episod
    </div>
  </div>
  <div class="img-col-third">
    Unsurprisingly, movies and TV shows are very popular topics in the Wikipedia browsing history, with movies being much more popular than TV. The movies topic spans the 3 largest article communities, overlapping with topics about TV, books and politics. And there are lots of small article communities dedicated to specific movies, actors or TV series.
    <br>
    Some ethnicities have their own movie/TV article communities.
  </div>
</div>

### Music and books  

<div class="img-row">
  <div class="img-col-third">
    <h2>Music</h2>
    <img src="/images/comm_graph_highlights_5.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 147</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection:
      <br>
      music || song || singer || discograph
    </div>
  </div>
  <div class="img-col-third">
    <h2>Books</h2>
    <img src="/images/comm_graph_highlights_2.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 121</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection:
      <br>
      novel || book || literatur || author || writer
    </div>
  </div>
  <div class="img-col-third">
    Both music and literature are key topics in large article communities, and both have many small niche article communities focused on individual musicians and novels, respectively.
  </div>
</div>



### Sports  
There are lots of sports-themed article communities of all sizes in our Wikipedia browsing history. The most popular sports topic in the graph is football, including both American football and soccer. I've attempted to separate them out in the highlighted graphs below.

<div class="img-row">
  <div class="img-col-third">
    <h2>All sports</h2>
    <img src="/images/comm_graph_highlights_9.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 85</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection:
      <br>
      sport || olympi || champion || athlet || team || swim || runn || footbal || soccer || basketbal || basebal || hockey || wrestl || tennis || golf || cricket || rugbi
    </div>
  </div>
  <div class="img-col-third">
    <h2>American football</h2>
    <img src="/images/comm_graph_highlights_10.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 18</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection:
      <br>
      (footbal && team && !(nation)) || (footbal && (america || (unit && state) ))
    </div>
  </div>
  <div class="img-col-third">
    <h2>Soccer</h2>
    <img src="/images/comm_graph_highlights_11.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 31</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection:
      <br>
      soccer || (associ && footbal) || futebol || (footbal && !( (footbal && team && !(nation)) || (footbal && (america || (unit && state) )) ) )
    </div>
  </div>
</div>


### John vs Mary & Co.

<div class="img-row">
  <div class="img-col-third">
    <h2>Just John</h2>
    <img src="/images/comm_graph_highlights_16.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 73</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection: john
    </div>
  </div>
  <div class="img-col-third">
    <h2>Mary, Maria, Anna, Anne, Elizabeth, etc.</h2>
    <img src="/images/comm_graph_highlights_17.png" alt="Community graph with highlights by topic terms">
    <strong>Communities found: 48</strong>
    <br>
    <div class="topic-terms">
      Topic terms selection:
      <br>
      (mari && !(mariag || marin)) || anna || (anne && !(channel || branne || gannet)) || elizabeth
    </div>
  </div>
  <div class="img-col-third">
    I've noticed that the name John shows up in many of the top topic terms lists of the article communities. The reason "John" shows up in the top 10 topic terms for so many communities is because those communities have articles about people, many of whom are named John. While "John" is not a topic, I thought it would be interesting to highlight all article communities that have "John" in their top topic terms.  
    <br><br>
    For comparison, I've also tried highlighting several popular Western female names. The highlighting on the left shows matches for Mary, Maria, Anna, Anne, Elizabeth, and also any names that contain these names, i.e., Rosemary, Hannah and Annette are also included in that highlighting.
  </div>
</div>


Summary  
---  

About a quarter of traffic to Wikipedia articles was from internal browsing of the articles, and the remaining 3/4 came from outside of Wikipedia. The 3/4 of external traffic tells us how we typically got to wikipedia: mostly through search engines (about 60% of external traffic), and maybe about 10% of the time through bookmarks or some other direct way to the Wikipedia's Main Page. The remaining 30% of external traffic is an unclear mix of empty referer traffic, external website links, etc.  



Reference
---
 - Wikipedia clickstream datasets are available for download [here](https://dumps.wikimedia.org/other/clickstream/), along with their accompanying [documentation](https://dumps.wikimedia.org/other/clickstream/readme.html)
