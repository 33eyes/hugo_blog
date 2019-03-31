---
title: Exploring Wikipedia clickstream
date: 2018-04-16T10:20:00-05:00
categories: Projects
tags: ["graph-databases", "neo4j", "Cypher", "clickstream", "ETL", "network-analysis", "Python"]
summary: An investigation of aggregated Wikipedia clickstream data using a graph database and network analysis.
draft: true
---

Whenever I want to learn about something, I google it, and sooner or later I end up on Wikipedia. And so do millions of other people, in many different languages. Wikipedia is a vast repository of knowledge organized into interlinked articles, and there are lots of ways to navigate through it as we learn. Whenever we browse Wikipedia, we learn and gather information, so perhaps our browsing behavior tendencies on Wikipedia can tell us something about how we learn on the internet. Do we tend to get the info that we need and move on? Do we delve into details and subtopics when we find something interesting? Do we tend to wander off into other topics? Do these behaviors vary across the different Wikipedia language domains? We can explore such questions by looking at the Wikipedia clickstream data.

What is Wikipedia clickstream data?  
----
[Clickstream](https://en.wikipedia.org/wiki/Clickstream) data in general is data about which webpages a website user visits. The Wikipedia clickstream data we will be looking at contains the paths Wikipedia users took from article to article on Wikipedia, along with general categories for webpages from which they came to Wikipedia. The Wikipedia clickstream data is released at aggregate level, summing up all user paths per webpage-article pair.  

In this project, I will explore the Wikipedia clickstream data across multiple language domains. Due to the highly interconnected nature of the data, I will use a graph database for data modeling, storage and manipulation, and network analysis for deriving insights from the data. I will be using the [neo4j](https://neo4j.com/) graph database along with python.  

The goals of this project are to explore clickstream data as a graph in general using network analysis techniques, and to see if there are any interesting patterns or differences in the way Wikipedia is used across different language domains.  




The dataset
----

In 2015, Wikipedia started releasing datasets of clickstream counts to Wikipedia articles for research purposes. The project has evolved over time, and as of April 2018 it takes the form of monthly automatic releases in 11 Wikipedia language domains: English, German, French, Spanish, Russian, Italian, Japanese, Portuguese, Polish, Chinese and Persian.  

The clickstream datasets contain counts of times someone went to a Wikipedia article from either another Wikipedia article or from some other webpage. The destination Wikipedia article here is called a **resource**, and the webpage from which the user went to the resource is called a **referer** (yep, [it's referer and not referrer](https://en.wikipedia.org/wiki/HTTP_referer)). Referers can be another Wikipedia article from the same language domain, a Wikimedia page that is not part of that Wikipedia language domain, a search engine, or some other webpage. If the referer is a Wikipedia article from the same language domain, then the article name is given. If not, then a general referer category is given.  

**Some examples of the data records:**

1. _Article-to-article data record_  
	In the English Wikipedia, the article 'Suki Waterhouse' links to the article 'List of Divergent characters'. In March 2018, users went from the 'Suki Waterhouse' article to the 'List of Divergent characters' article 86 times. This clickstream activity between the two articles is recorded in the 2018-03 English Wikipedia clickstream data release as:  

      referer | resource | reference type | count
      --- | --- | --- | ---
      [Suki_Waterhouse](https://en.wikipedia.org/wiki/Suki_Waterhouse) | [List_of_Divergent_characters](https://en.wikipedia.org/wiki/List_of_Divergent_characters) | link | 86


2. _External-source-to-article data record_  
	In the English Wikipedia, the article 'Bureau of Investigative Journalism' was visited 18 times during March 2018 from some other Wikimedia page that is not part of the English Wikipedia. This clickstream activity is recorded in the 2018-03 English Wikipedia clickstream data release as:  

      referer | resource | reference type | count
      --- | --- | --- | ---  
      other-internal | [Bureau_of_Investigative_Journalism](https://en.wikipedia.org/wiki/Bureau_of_Investigative_Journalism) | external | 18



The Wikipedia clickstream data shows how users get to Wikipedia articles per language domain. It is a weighted network of articles and external sources, weighted by the number of times users went to a given Wikipedia article from either another Wikipedia article or some other webpage.


For this project I'm using the March 2018 Wikipedia clickstream data release, and all of the 11 language domains provided. I'm restricting the data subset to March 2018 for two reasons. First, the way the Wikipedia clickstream data is processed has evolved significantly over time, and the number of language domains included in the releases has changed as well. The changes in data processing are likely to affect the data analysis results, so I am using the March 2018 subset in order to avoid that. Second, the Wikipedia clickstream data is quite large, and the March 2018 subset gives me enough data for an initial exploration.

---


The roadmap for this project
----
The data examples above show that the Wikipedia clickstream data is just a long list of article-to-article or external-webpage-to-article references, along with the number of times those references were made. Many articles refer to many other articles, which makes this data highly interconnected. We can use graph theory and network analysis techniques to analyze this data. And since the clickstream data is pretty large, we'll need to load it into a graph database to get the best network analysis performance.  
Here's the plan:

1. ETL (Export, Transform and Load the data)
2. Descriptive statistics and exploratory data analysis
3. Let's ask the data some questions!  

This blog post will cover the 1st step, and the next steps will be covered in subsequent posts.



# ETL  

Data download and cleaning
-----
We can download the March 2018 Wikipedia clickstream data from [here](https://dumps.wikimedia.org/other/clickstream/2018-03/). The English Wikipedia clickstream data file is the largest of those, and it unzips to 1.3G. The second largest is the German Wikipedia clickstream, and it unzips to 219M. The data files for the other language domains are much smaller.  

The Wikipedia clickstream datasets don't need much cleaning on their own.  
There are just 4 columns:  

- referer webpage (freetext field)
- target article (freetext field)
- reference (categorical text field)
- number of occurrences of the reference (numerical)

That being said, I'm going to import this data into the neo4j graph database, which has its own data import quirks. To address those, I need to clean up the quotes in the freetext fields. Neo4j seems to use quotes as a field terminator, even if you explicitly specify a different one, so the quotes need to be escaped. This can be easily done by running the `sed 's/"/\\"/g'` command in terminal for each of the data files. That's it for data cleaning, now we can load the data into a graph database.


Data modeling
-------
Graph databases are structured differently from RDBMS like MySQL or PotgreSQL, where tables are the key concept. In a graph database, the key concepts are relationships (a.k.a. edges or links) and nodes (a.k.a. vertices or entities), and both of those can have various properties. Before we can load our data into a graph database, we need to decide what are our nodes and relationships in the data.  

In terms of nodes and relationships, it's quite easy to model the Wikipedia clickstream data: the nodes are the webpages and the relationships are the references. But even in this simple case, there are several variations of this model that we could consider. We have two types of webpages in our data: the Wikipedia articles, where we know the exact IRL, and the external webpages, where we only know a broad category of the webpage, like a search engine, but not the exact webpage. These two types of webpages are very different, and we might want to have different kinds of nodes for them, rather than a single node. Similarly, we have 3 kinds of references from webpage to article in the data. The references could be article-to-article links, external or other, and we could model each one of them as a separate relationship type. There are lots of options and no right answer, it all depends on the use case.

For this project, I've decided on the following model:

- nodes:
	- Wikipedia articles
		- properties: article name, language domain
	- External sources
		- properties: external source type (e.g. search engine), language domain
- relationships:
	- REFERRED_TO
		- properties: reference type (link, external, other), count of reference occurrences (users went from the referer article to the referred article N times in the data)
		- this is a one-directional relationship
		- An external source can only refer to articles, and cannot be referred to. An article can refer to other articles, and can be referred to by articles or external sources.  

We will take a look at what this model looks like in neo4j once we load the data.


Importing the data into neo4j
-------
Now that we've defined our data model, let's import the clickstream data into neo4j. [Neo4j](https://neo4j.com/) is a graph database that is both storage- and analytics-friendly. It is currently the most popular graph database, and it has a free community edition. Instead of SQL, neo4j uses the [Cypher query language](https://en.wikipedia.org/wiki/Cypher_Query_Language) in order to interact with the database.

There are two ways to import data into neo4j: cypher `LOAD CSV` and neo4j's batch importer tool. Neo4j has a nice [guide](https://neo4j.com/developer/guide-import-csv/) that covers both of them. For this project, I'll import the data using cypher `LOAD CSV`.

The `LOAD CSV` cypher query can be run in either the cypher-shell or from python using the py2neo driver. In both cases, we're running cypher queries against the neo4j database.

**Overview of import steps:**

1. _Create indexes for any matches or merges that will happen during `LOAD CSV`_  
	Indexes make finding nodes faster. Since Wikipedia clickstream data rows are webpage-to-article and article-to-article references with lots of articles repeating, when we use `LOAD CSV` to load this data into the neo4j database, we will use the `MERGE` statement, which checks if a node already exists before creating it. This means that the `LOAD CSV` query will search the database for each node to be created. Indexing the nodes beforehand makes this process much faster.  
	<br>
	Neo4j automatically manages indices and updates them whenever the graph database has changed. This means that we can set indices before loading the data, and benefit from them at load time. For more info about neo4j indices, see [neo4j's index documentation](https://neo4j.com/docs/developer-manual/current/cypher/schema/index/).  
	<br>

	Cypher query example for setting an index on Article nodes, which are unique by article title and language domain:  

		CREATE INDEX ON :Article(title, language_code);  

	To see all indices set on a graph, run the following cypher statement:  

		CALL db.indexes;  
	<br>

2. _Load nodes_  
	While it is possible to load both nodes and relationships in a single `LOAD CSV` query, it is often faster to load nodes and relationships in separate queries. To keep the cypher queries simple and easy to read, I'll also load the two types of nodes separately.  
	<br>

	Cypher query example for loading Article nodes (from internal references only, for now), for English language domain:  

		USING PERIODIC COMMIT
		LOAD CSV FROM {myfilepath} AS row
		FIELDTERMINATOR '\t'
		WITH row
		WHERE row[2] <> 'external'
		MERGE (n1:Article {title: row[0], language_code: 'EN' })
			ON CREATE SET
				n1.language = 'English'
		MERGE (n2:Article {title: row[1], language_code: 'EN' })
			ON CREATE SET
				n2.language = 'English'
		;

	Cypher query example for loading ExternalSource and Article nodes (from external references), for English language domain:  

		USING PERIODIC COMMIT
		LOAD CSV FROM {myfilepath} AS row
		FIELDTERMINATOR '\t'
		WITH row
		WHERE row[2] = 'external'
		MERGE (n1:ExternalSource {source_type: row[0], language_code: 'EN' })
			ON CREATE SET
				n1.language = 'English',
				n1.description = CASE
					WHEN row[0] = 'other-internal' THEN 'a page from any other Wikimedia project (not Wikipedia)'
					WHEN row[0] = 'other-search' THEN 'an external search engine'
					WHEN row[0] = 'other-external' THEN 'any other external site (not search engine)'
					WHEN row[0] = 'other-empty' THEN 'an empty referer'
					WHEN row[0] = 'other-other' THEN 'anything else (catch-all)'
					ELSE '' END

		MERGE (n2:Article {title: row[1], language_code: 'EN' })
			ON CREATE SET
				n2.language = 'English'
		;
	<br>

3. _Load relationships_  
	When loading relationships, we simply find the two nodes involved in the relationship in the graph database, and create a relationship record between them.  
	<br>

	Cypher query example for loading Article-to-Article references, for English language domain:  

		USING PERIODIC COMMIT
		LOAD CSV FROM {myfilepath} AS row
		FIELDTERMINATOR '\t'
		WITH row
		WHERE row[2] <> 'external'
		MATCH (n1:Article {title: row[0], language_code: 'EN' })
		MATCH (n2:Article {title: row[1], language_code: 'EN' })
		MERGE (n1)-[r:REFERRED_TO]->(n2)
			ON CREATE SET
				r.type = row[2],
				r.count = row[3]
		;

	Cypher query example for loading ExternalSource-to-Article references, for English language domain:  

		USING PERIODIC COMMIT
		LOAD CSV FROM {myfilepath} AS row
		FIELDTERMINATOR '\t'
		WITH row
		WHERE row[2] = 'external'
		MATCH (n1:ExternalSource {source_type: row[0], language_code: 'EN' })
		MATCH (n2:Article {title: row[1], language_code: 'EN' })
		MERGE (n1)-[r:REFERRED_TO]->(n2)
			ON CREATE SET
				r.type = row[2],
				r.count = row[3]
		;
	<br>

	When developing `LOAD CSV` queries, it is helpful to test them out to see how the data is being processed before writing the data to the graph. This can be done by replacing the `MERGE` statement with a `RETURN` statement and adding a `LIMIT` clause. The `RETURN` statement will return the result of the query without writing it to the database, and the `LIMIT` clause will limit the returned output to a small number of items for testing.  
	<br>
	Here are some examples:  
	<br>
	This query returns 10 data rows as cypher sees them:

		LOAD CSV FROM {myfilepath} AS row
		FIELDTERMINATOR '\t'
		RETURN row
		LIMIT 10;


	This query returns the first field and a newly created description field from 10 data rows:

		LOAD CSV FROM {myfilepath} AS row
		FIELDTERMINATOR '\t'
		WITH row
		WHERE row[2] = 'external'
		RETURN row[0] as n1_source_type,
				CASE
					WHEN row[0] = 'other-internal' THEN 'a page from any other Wikimedia project (not Wikipedia)'
					WHEN row[0] = 'other-search' THEN 'an external search engine'
					WHEN row[0] = 'other-external' THEN 'any other external site (not search engine)'
					WHEN row[0] = 'other-empty' THEN 'an empty referer'
					WHEN row[0] = 'other-other' THEN 'anything else (catch-all)'
					ELSE ''
				END as n1_description
		LIMIT 10;
<br>

[Here's my jupyter notebook](https://github.com/33eyes/wiki-clickstream-graph/blob/master/00_import_data_into_neo4j.ipynb) with the full import code for March 2018 Wikipedia clickstream data across all language domains using py2neo.

---

Now that we have loaded the data into neo4j, we can check that our database schema matches our graph data model. The easiest way to explore the data that's been loaded into neo4j is through the [neo4j browser](https://neo4j.com/developer/guide-neo4j-browser/).  

To view the database schema, run `CALL db.schema()` in the neo4j browser.  
<br>  
<div class="centered" markdown="1">
##### **Wikipedia clickstream db schema**
</div>
<div class="centered" markdown="1">
![Wikipedia clickstream db schema](/images/graph_db_schema.png)
</div>
<br>

To view a sample of the data in the graph, we can run `MATCH p=()-[r:REFERRED_TO]->(n:Article {language_code:'EN'}) RETURN p LIMIT 25` in the neo4j browser:  
<div class="centered scaled" markdown="1">
![neo4j query output](/images/english_wiki_25_rels_graph.png)
</div>

From this sample subgraph, we can see that the clickstream data is highly interconnected. We can inspect closer a handful of nodes in the neo4j browser on a case by case basis, but to get a sense of what's going on in this graph as a whole, we need to use network analysis techniques.

# Next steps

- Descriptive statistics and EDA
- Hypotheses testing


# Reference

Wikipedia clickstream data
------------
- Data source/download: [Wikipedia clickstream data dumps](https://dumps.wikimedia.org/other/clickstream/)
- Data description: [Research:Wikipedia clickstream](https://meta.wikimedia.org/wiki/Research:Wikipedia_clickstream)
- The data is released and maintained by the Wikimedia Foundation's [Analytics Engineering team](https://wikitech.wikimedia.org/wiki/Analytics)

Neo4j graph database
------------
 - [What is a graph database?](https://neo4j.com/developer/graph-database/)
 - [Download](https://neo4j.com/download/)
 - [Intro to neo4j's browser](https://neo4j.com/developer/guide-neo4j-browser/)
 - [Documentation](https://neo4j.com/docs/)
 - [Nicole White's Hello-World with jupyter and py2neo](https://nicolewhite.github.io/neo4j-jupyter/hello-world.html)
 - [Cypher documentation](https://neo4j.com/docs/developer-manual/current/cypher/)  
 - [Guide to graph data modeling](https://neo4j.com/developer/guide-data-modeling/)

This project
----------
 - [GitHub repo](https://github.com/33eyes/wiki-clickstream-graph)
 - [Data import jupyter notebook](https://github.com/33eyes/wiki-clickstream-graph/blob/master/00_import_data_into_neo4j.ipynb)
