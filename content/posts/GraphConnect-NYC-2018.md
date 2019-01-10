---
title: GraphConnect NYC 2018
date: 2018-10-20T12:19:52-05:00
categories: Conferences
tags: ['graph-databases', 'neo4j', 'Cypher', 'network-analysis']
summary: Notes and highlights from the GraphConnect 2018 conference in NYC.
---

About a month ago, I've attended the [GraphConnect conference](https://graphconnect.com/) in NYC.
I had a great time at the conference, and thought I'd share my experience, along with a brief intro to graph concepts and technologies.


About the conference  
----  
### **Day 1: Sessions**
  - [Keynote speeches](https://www.youtube.com/watch?v=dW6JsFccdkM)
  - Talks about applications of graph databases and graph analytics across a
  wide gamut of industries, including:
    - Fraud detection
    - Healthcare IT
    - Medical and scientific research
    - Web development
    - AI and ML
    - GDPR compliance
    - Master data management
    - Identity and access management
    - HR
    - Finance
    - And anything that relies on recommender engines
  - Neo4j-led sessions covering product features, use cases and new releases
    - 2018 update highlights:
        - [Neo4j Bloom](https://neo4j.com/bloom/): a visual graph exploration application
        - [Spatial features in Neo4j](https://medium.com/neo4j/whats-new-in-neo4j-spatial-features-586d69cda8d0)
        - [Temporal date types](https://www.adamcowley.co.uk/neo4j/temporal-native-dates/)
        - [Graph algorithms library](https://neo4j.com/docs/graph-algorithms/current/)
        - [An updated APOC library](https://neo4j.com/blog/apoc-release-for-neo4j-3-4-with-graph-grouping/)
        - [Neo4j Morpheus](https://www.youtube.com/watch?v=NMDdVH9hHYo): combines graph and relational table functionalities into one application
  - [2018 Graphie Awards](https://www.youtube.com/watch?v=OlTtBG6mQbc): a recognition of companies and graph community members for their contributions to the graph tech community  

### **Day 2: Training**  
  - Code-along training workshops in Neo4j  
  - Included workshops on:  
    - Natural language understanding  
    - Graph modeling  
    - Data science applications  
    - Graph algorithms  
    - Full stack development with [GRANDstack](https://grandstack.io/)  

### **Day 3: GraphHack!**
  - A graph tech hackathon
  - It was really fun, and great opportunity to try out awesome graph tech!

### **Throughout the conference**
  - **1-1 graph clinics**: you could schedule a 15 min meeting with a Neo4j expert to get help with Neo4j or graph tech in general  
  - **Expo**: a showcase of businesses that either leverage or support Neo4j solutions


About Neo4j  
----  
GraphConnect was organized by [Neo4j](https://neo4j.com/), the company that makes the Neo4j graph database platform. Neo4j was formed way back when it was cool to name things after 'The Matrix' movies, and it has been leading the way in graph tech ever since.  

Originally, Neo4j's founders were trying to solve a specific data problem, caused by inefficient merges of relational database tables. Ironically, the relational database structure was preventing them from efficiently accessing and operating on the relationships in the data. When they realized that, they set out to build a database structure where data relationships are a first-class entity.  
And build it they did. The Neo4j graph database they've built was a fast back-end for applications that derive value from highly connected data, like product recommendations on online retailer websites, flight fares and hotel bookings, and so on.  

But that didn't end there. As people started to experiment with graph databases more and more, some started using Neo4j for data analytics, taxonomy explorations, feature engineering, and lots of other applications. Through engaging with this graphs community, Neo4j has gone on to create new graph analytics solutions, ranging from analytics-friendly data visualization to complex graph algorithms made easy and fast.  

So now, the Neo4j graph database is really a two-pronged product. On the one hand, it offers developers a fast database back-end for highly connected data. And on the other hand, it is a powerful tool for graph analytics and connected data exploration, which greatly benefits researchers, data scientists, business analysts, and anyone else who is trying to derive insights from highly connected data.  

<hr>

A very brief intro to graphs  
----  
### **What is a graph?**  
It's just a way to think of and represent interconnected things. A graph can also be called a network, the two terms are equivalent.  
Graphs are made of **nodes** (the interconnected things themselves) and the **relationships** between those things. Relationships are usually represented by lines/arrows.  
A very basic graph can be just two nodes connected by a relationship. If we have a relationship where the direction of it matters, then we have a **directional graph**. We can also attach various properties to both nodes and relationships. Such graphs are called **property graphs**.   
<div class="flexbox-container">
  <div class="col">
    <img alt="basic graph types" style="width:350px;" src="/images/basic_graph_types_sm2.png">
  </div>
  <div class="col">
  <img alt="graph with many node and relationship types" style="width:350px;" src="/images/graph_with_many_types_sm.png">
  </div>
</div>
<br><br>
In large connected datasets, these basic node and relationship elements can form complicated graph structures.  


![internet map graph](https://upload.wikimedia.org/wikipedia/commons/d/d2/Internet_map_1024.jpg)
<span class="img-annotation"> Image source: [Partial map of the Internet based on the January 15, 2005 data found on opte.org.](https://en.wikipedia.org/wiki/Complex_network#/media/File:Internet_map_1024.jpg) (node = IP address) </span>



When connected data is modeled as a graph, we can leverage graph structure properties to either
optimize connected data processing in real-time solutions (a use case for devs), or derive new insights (a use case for researchers, analysts and data scientists). Or, we might just visualize the data graph as is, and visually investigate the data. The graph data representation is intuitive enough for anyone to just look at it and gain
some understanding of the data.

<br>


(graphs)-[:ARE]->(everywhere)  
----  
According to the CEO and co-founder of Neo4j Emil Eifrem, if you've booked a
hotel room, browsed airfare online, or even used an ATM recently, then you've
most likely used Neo4j without even realizing it.
Insurance companies use graph databases to make the most of their connected data,
and many of them choose Neo4j to do that.
Fraud detection is at this point a classic problem to tackle with graph tech, and lots of
large financial institutions use graph tech to make financial data connections
transparent enough so that fraudulent behavior can't hide within big data.  
Those are just the larger businesses that people are likely to run into on a daily basis, but there are many other industries and companies that are using graph technologies as well, ranging from retail to healthcare to scientific research.  

<hr>

Graphs + data science = new insights into connected data  
----  
Graphs are an incredibly powerful data tool that spans many industries and techniques.
I think that graphs are going to become an essential tool for data scientists in the near future. While a graph approach is not needed in every data-driven research or statistical analysis project, more and more of the big data sources and challenges involve connected data. From wearable fitness trackers to online consumer behavior, to drug interactions, to movie recommendations, to scientific research databases, people are constantly creating vast amounts of interconnected data. As data scientists, we are tasked with extracting insights from that data, and we can gain novel insights from the relationship structures within the interconnected data by adding graph techniques to our data science toolkits.  

Right now, there are 3 main ways that any data scientist working with highly interconnected data can immediately gain value from using graph tech: visual exploration of connections within the data, graph algorithms, and feature extraction.  

### **Visual exploration of connected data**  
Visual data exploration is a simple yet powerful tool in data science.
It can help data scientists better understand their data and make informed decisions when planning their approach to analyzing the data. Visualization also helps data scientists communicate insights about the data to less technical stakeholders.  

When it comes to highly interconnected data, graph-based data visualization takes on an interesting role, because in some data-driven industries a custom visualization of data interconnections is the final data analytics product. For example, in fraud detection the visualization of connections between financial transactions, businesses and individuals is used by fraud investigators to visually inspect the data an find suspicious behavior. Another example is social network analytics, where an analyst might visually inspect the impact of a social media influencer on a marketing campaign.

### **Graph algorithms**  
Graph algorithms can be used to quantify graph structures within the connected data. Currently there are 3 main kinds of graph algorithms: centrality, community detection, and pathfinding.


#### **Centrality algorithms**  
Centrality algorithms measure the relationship-based importance of individual nodes within a network. They assign a computed metric to each node, ranking its importance in the graph.  

<div class="flexbox-container">
  <div class="col">
    <h4>Typical questions these algorithms can help answer:</h4>
    <ul>
      <li>Who are the key influencers in your connected data?</li>
      <li>Who has the most connections?</li>
      <li>Who is directly or indirectly connected to the largest proportion of the network?</li>
      <li>Who connects distinct groups of nodes that are otherwise not connected to each other?</li>
      <li>If a specific node is to be changed or removed, how important is this to the network? How many of the other nodes will be affected, and how far can these effects propagate through the graph?</li>
    </ul>
  </div>
  <div class="col">
    <img alt="graph centrality sketch" style="margin-top: 20px;" src="/images/graph_centrality.png">
  </div>
</div>

#### Algorithm examples:
  -  [PageRank](https://neo4j.com/docs/graph-algorithms/current/algorithms/page-rank/)
  - [Betweenness centrality](https://neo4j.com/docs/graph-algorithms/current/algorithms/betweenness-centrality/)
  - [Closeness centrality](https://neo4j.com/docs/graph-algorithms/current/algorithms/closeness-centrality/)

<br>

#### **Community detection**  
Community detection algorithms are used to cluster data based on the distribution of relationships in a graph. The resulting clusters of nodes are the sub-communities within the whole network, where the nodes within the communities have stronger relationship ties to each other than to the nodes in other communities.

<div class="flexbox-container">
  <div class="col">
    <h4>Typical questions these algorithms can help answer:</h4>
    <ul>
      <li>Are there communities in your connected data? What are they?</li>
      <li>Are there hierarchical communities?</li>
      <li>Are all of the nodes in the network connected?</li>
    </ul>
    <h4>Algorithm examples:</h4>
    <ul>
      <li><a href="https://neo4j.com/docs/graph-algorithms/current/algorithms/label-propagation/">Label propagation</a></li>
      <li><a href="https://neo4j.com/docs/graph-algorithms/current/algorithms/louvain/">Louvain</a></li>
      <li><a href="https://neo4j.com/docs/graph-algorithms/current/algorithms/triangle-counting-clustering-coefficient/">Triangle counting</a></li>
      <li><a href="https://neo4j.com/docs/graph-algorithms/current/algorithms/connected-components/">Connected components</a></li>
    </ul>
  </div>
  <div class="col" style="flex-basis: 50%;">
    <img alt="graph communities sketch" style="margin-top: 20px;" src="/images/graph_communities.png">
  </div>
</div>

<br>

#### **Pathfinding**  
These algorithms find and evaluate paths in a network. They can help answer a wide range of questions related to traversing a path in a graph along nodes and relationships. This set of algorithms includes a random walk long the graph.

<div class="flexbox-container">
  <div class="col">
    <h4>Typical questions these algorithms can help answer:</h4>
    <ul>
      <li>What's the shortest path from node A to node B?</li>
      <li>If we start at node A, where can we go in the graph?
        <br> What are the possible paths?</li>
      <li><a href="https://en.wikipedia.org/wiki/Six_degrees_of_separation">6 degrees of separation</a>-based questions</li>
    </ul>

    <h4>Algorithm examples:</h4>
    <ul>
      <li><a href="https://neo4j.com/docs/graph-algorithms/current/algorithms/shortest-path/">Shortest path</a></li>
      <li><a href="https://neo4j.com/docs/graph-algorithms/current/algorithms/minimum-weight-spanning-tree/">Minimum weight spanning tree</a></li>
      <li><a href="https://neo4j.com/docs/graph-algorithms/current/algorithms/random-walk/">Random walk</a></li>
    </ul>
  </div>
  <div class="col" style="flex-basis: 40%;">
    <img alt="graph path sketch" style="margin-top:20px;" src="/images/graph_path.png">
  </div>
</div>

<br><br>

### **Feature extraction**  
Feature extraction in data science can mean two things. There is the more general definition of deriving a set of features from input data, and there is a more specific definition in machine learning as a dimensionality reduction technique. I'm going with the more general definition first here, and then with the machine learning one.

#### **Graph metrics and properties as features**
Graph algorithms are useful analytic tools on their own, but they can also be used to extract features from the graph that can then be fed into more traditional data science techniques. For a simple example, we can run a community detection algorithm on our connected data, and then extract the found communities as a categorical variable to be used in regression analysis.  
Similarly, we can extract any kind of localized graph feature associated with a node and use it in further analyses.  

#### **Graph embeddings**
We can also use graph embeddings, similar to word embeddings like word2vec, to automatically extract graph-based features to feed into machine learning models. [Mark Needham has a great article on this](https://towardsdatascience.com/deepgl-on-neo4j-b27e8c64190f).


### **Neo4j resources for data scientists**  
   - [GraphGists](https://neo4j.com/graphgists/): explore sample graph data projects  
   - [Community forum](https://community.neo4j.com)
   - [Graph algorithms docs](https://neo4j.com/docs/graph-algorithms/current/introduction/)
   - [Neo4j's youtube channel](https://www.youtube.com/channel/UCvze3hU6OZBkB1vkhH2lH9Q) has lots of tutorials and conference/webinar recordings  
       - For data scientists working with customer journey data, I highly recommend this [overview of graph-based customer journey analytics](https://www.youtube.com/watch?v=Rlh9PkHinAs)!  



----

<div class="quoted-block">
  <p class="quoted-text">Graph analysis is possibly the single most effective competitive differentiator for organizations pursuing data-driven operations and decisions.</p>
  <p class="quoted-by">- Gartner Research</p>
</div>

----

Book recommendations
----
- ["Connected" by James Fowler](https://www.amazon.com/dp/B002OFVO5Y)  
- [Oreilly's "Ethics and Data Science" by DJ Patil, Hilary Mason, Mike Loukides](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/)  
- ["The Data Science Design Manual" by Steven Skiena](https://www.amazon.com/Science-Design-Manual-Texts-Computer/dp/3319554433)  
- ["The Algorithm Design Manual" by Steven Skiena](https://www.amazon.com/Algorithm-Design-Manual-Steven-Skiena-dp-1848000693/dp/1848000693/)
- ["The Software Paradox" by Stephen O'Grady](https://www.amazon.com/Software-Paradox-Rise-Commercial-Market/dp/1491900938)
