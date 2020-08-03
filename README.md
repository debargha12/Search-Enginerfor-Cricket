# Information Retrieval

# Project Report

##### The University of Texas at Dallas CS 6322.

##### Information Retrieval Fall 2018

##### Class Project Proposal

##### Project TITLE: Cricket Search Engine

##### Students :

##### Harsh Verma, hxv180001@utdallas.edu

##### Vatsalkumar Patel, vrp140230@utdallas.edu

##### Surya Alla, sxa180109@utdallas.edu

##### Chandhya Thirugnanasambantham, cxt123730@utdallas.edu

##### Debargha Ghosh, dxg170014@utdallas.edu

##### Anugreh Raina, axr180055@utdallas.edu


### Harsh Verma HXV

## Crawling

##### TECH STACK USED:

1. Apache Nutch-1.
2. Solr-7.3.1d
3. Selenium-2.42.
4. Firefox-29.
5. Java 8.

Apache Nutch is an Internet search engine software, web Crawler, powerful for vertical search
engine :

- For crawling web pages associated with Cricket Sports, The Apache Nutch Framework was
    utilized for crawling as well as feeding the Fetched Content from crawling to the Solr Framework
    hosted on localhost for indexing the fetched web pages as well as creating web graphs for
    implementing Page Rank and the HITS algorithms.

Why Use Nutch?

#### ● Production ready Web Crawler, Scalable , Tried and Tested

#### ● Fine grained Configurations

#### ● Relying on Apache hadoop data structure, batch processing

#### ● MultiThreaded.

#### ● Allows Custom Implementation for parse, index and scoring.

#### ● Pluggable Indexing (solr, mongodb, elastic search, etc)

#### ● Automation on checking broken links

#### ● Handle Duplication

#### ● User friendly ,Url Filters, Normalization

### Screenshot of Documents Stats in solr


#### Harsh Verma HXV

#### Searching Document in Solr:


##### Harsh Verma HXV

### Apache Nutch generates 3 folders during the crawling operation:

1. CRAWLDB: it maintains the information about URLs such as the fetch status, fetching schedule,
metadata, etc.
2. LINKDB: For each URL, the LINKDB maintains the incoming and outgoing URLs for that URL
which are further used to facilitate PAGE RANKING algorithm and the HITS algorithm.
3. SEGMENTS: contains multiple subdirectories within it. During Crawling, the crawl script creates
multiple directory to store information for Crawl Fetching, Crawl Content, Crawl Parsing, Parsed Data
and Parsed Text

### The Crawling Method can be described by the following methods:

### STEP 1: INJECTOR

1) The injector takes all the URLs of the seeds.txt file and adds them to the crawldb.
2) As a central part of Nutch, the crawldb maintains information on all known URLs (fetch schedule,
fetch status, metadata).

$NUTCH_RUNTIME_HOME/bin/nutch inject crawl/crawldb urls

STEP 2: GENERATOR
Based on the data of crawldb, the generator creates a fetchlist and places it in a newly created segment
directory
$NUTCH_RUNTIME_HOME/bin/nutch generate crawl/crawldb crawl/segments -topN 130

STEP 3: FECTHER
The fetcher gets the content of the URLs on the fetchlist and writes it back to the segment directory.
This step usually is the most time-consuming one , This respects the robots rules (robort.txt and robots
directives)
$NUTCH_RUNTIME_HOME/bin/nutch fetch $s

STEP 4: PARSER
Now the parser processes the content of each web page and for example omits all html tags.
If the crawl functions as an update or an extension to an already existing one (e.g. depth of 3), the updater
would add the new data to the crawldb as a next step.
$NUTCH_RUNTIME_HOME/bin/nutch parse $s

STEP 5: LINK INVERTER
Before indexing, all the links need to be inverted, which takes into account that not the number of
outgoing links of a web page is of interest, but rather the number of inbound links.
This is quite similar to how Google PageRank works and is important for the scoring function.
The inverted links are saved in the linkdb.
$NUTCH_RUNTIME_HOME/bin/nutch invertlinks crawl/linkdb -dir crawl/segments

STEP 6 and 7: SOLR INDEXER
Using data from all possible sources (crawldb, linkdb and segments), the indexer creates an index and
saves it within the Solr directory.
For indexing, the popular Lucene library is used.
Now, the user can search for information regarding the crawled web pages via Solr.bin/nutch index
crawl/crawldb/ -linkdb crawl/linkdb/ crawl/segments/*/ -filter -normalize –deleteGone


##### Harsh Verma HXV

- The command used for creating the Dump of segments is:

##### bin/nutch readlinkdb Crawling/LinkDB -dump LinkDBDUMP

##### This generates a Dump containing the incoming and outgoing links for each URL.

### APACHE NUTCH CONFIGURATION

Modifications that were made to the nutch configuration file

- Path:apache-nutch-1.1.15/conf/nutch-site.xml
- Plugins: protocol-http|protocol-httpclient|urlfilter-regex|index-(basic|more)|query- (basic|site|url|
    lang)|indexer-solr|nutch-extensionpoints|protocol-httpclient|urlfilter-regex|parse-(text|html|
    msexcel|msword|mspowerpoint|pdf)|summary-basic|scoring-opic|urlnormalizer-(pass|regex|
    basic)protocol-http|urlfilter-regex|parse-(html|tika|metatags)|index-(basic|anchor|more|metadata)
- Fetcher Server Delay (The number of seconds the fetcher will delay between successive
    requests to the same server): 1
- Selenium Driver: firefox
- Http Redirect Max The maximum number of redirects the fetcher will follow when trying to
    fetch a page. If set to negative or 0, fetcher won't immediately follow redirected URLs, instead it
    will record them for later fetching: 1
- Ignore outlinks to the same hostname: False
- Ignore outlinks to the same domain: False
- Limit to only a single outlink to the same page: False

This number is the maximum number of threads that should be allowed to access a queue at one
time: As per machine we set it to 40

##### Screenshot of Nutch Config Properties:

##### HTTP Agent and Plugin Properties


##### Harsh Verma HXV

##### FTP Properties and Http Content Settings:


##### Harsh Verma HXV

##### Firefox Driver and Dynamic Content Property


##### Harsh Verma HXV

##### Http Timeout and Robot Property Settings:


##### Harsh Verma HXV

##### STATISTICS FOR CRAWLED DATA:

##### Nutch provides an api called readdb with stats as argument, which gives the stats of the

##### data crawled :

###### TOPIC NUMBER

Crawled Links 1,17,

Db_unfetched 2,10,120 (Depth 2)

```
Db_gone 4320
```
### Common Issues Faced and Resolved:

##### 1. Nutch do not fetch AJAX/JavaScript driven dynamic HTML content :

##### -> In order to crawl webpages that rely on JavaScript/AJAX to dynamically load content we used

##### the Selenium.

##### -> The average rate Selenium used to crawl the pages is 500 pages / hour

##### -> So to crawl more than 1,00,000 pages it took around 200+ Hours, so we used multiple

##### machines to achieve it in 10 days parallely.

##### 2. Choosing Specific Version of Apache Nutch :

##### I started with apache nutch 2.X , it has web-api where in we can schedule jobs and monitor. But

##### 2.X is not stable and does not have as many features as 1.X like webgraph api and many more.

##### Hence, I switched to nutch 1.

##### 3. Filters : Pages like blogs dummy pages, help pages where being crawled which are not

##### needed, and where not crawled future with regex url filter.

##### 4. Selenium with Firefox compatibility issue:

##### Selenium is automates browser. Nutch loads its pages in selenium’s browser. So, It’s very

##### important to find the compatible version of browser with particular version of selenium. Version

##### mentioned in requirements are compatible versions with nutch 1.5.

##### 5. Selenium has an threading issue of memory leak: So due to this, many times browser gets

##### stuck on a pages and do not terminate. Throwing port locked exception. Hence I run a command

##### in cron for every 15-30 mins to kill firefox. This releases the locked ports, to be used by active

##### threads.

##### SEEDS LIST

##### Initially we gave 95 URLs as seed URL to start our crawling

##### The sample list of seed URLs are as follows:

##### http://www.iplt20.com/

##### http://www.icc-cricket.com/


##### Harsh Verma HXV

##### http://www.cricbuzz.com/

##### http://www.cricket.com.au/

##### http://www.royalchallengers.com

##### http://www.bcci.tv/

##### http://www.cricketworld.com/

##### http://chennaisuperkings.com/

##### http://kkr.in/

##### http://www.cricketnmore.com/

##### http://www.mumbaiindians.com/

##### http://cricwaves.com/

##### http://www.pcb.com.pk/

##### http://ecb.co.uk/

##### http://www.kxip.in/

##### http://cricketweb.net/

##### http://rajasthanroyals.com/

##### http://www.srilankacricket.lk/

##### http://www.lords.org/

##### http://www.cricket365.com/

##### http://www.islandcricket.lk/

##### http://cricket.af/

##### http://yorkshireccc.com/

##### http://www.lastmanstands.com/

##### http://cricket.co.za/

##### http://www.indiancricketfans.com/

##### http://www.kiaoval.com/

##### http://cricschedule.com/

##### http://www.windiescricket.com/

##### http://www.cricketvictoria.com.au/

##### http://mumbaicricket.com/mca/

##### http://www.cricbay.com/

##### http://www.glamorgancricket.com/

##### http://emiratescricket.com/

##### http://cricketcrowd.com/

##### http://www.kentcricket.co.uk/

##### http://zimcricket.org/

##### http://pcboard.com.pk/

##### http://hycricket.org/

##### http://howstat.com.au/cricket/home.asp

##### http://waca.com.au/

##### http://cricschedule.com/

##### http://cricruns.com/

##### http://www.cricket.co.uk/

##### http://www.worldcricketcentre.com/

##### http://www.20-20.in/

##### http://www.indiatimes.com/sports/

##### http://www.in.com/sports/cricket/


##### Harsh Verma HXV

##### http://www.hotstar.com/sports/cricket

##### http://sports.ndtv.com/cricket

##### http://crickettimes.com/series/2312/IPL-2019/

##### http://www.cricwaves.com/cricket/news/articles/

##### http://www.onlinecricketbetting.net/blog/

##### http://www.cricadium.com/category/ipl/2019-ipl-12/

##### http://www.cricadium.com/category/cricket-world-cup/world-cup-2019/

##### http://www.thefulltoss.com/

##### http://caribbeancricket.com/news

##### http://www.cricindeed.com/

##### http://www.cricketnews.net.in/

##### http://1tip1hand.com/

##### http://www.thecricketblog.com/

##### http://www.cricfirst.com/

##### http://www.cricnmore.com/

##### http://en.wikipedia.org/wiki/Cricket

##### http://en.wikipedia.org/wiki/Indian_Premier_League

##### http://cricket.yahoo.net/

##### http://sportzwiki.com/cricket

##### http://betting.betfair.com/cricket/

##### http://www.edailysports.com/category/cricket/

##### http://www.sportseon.com/cricket/

##### http://www.thecricketblog.info/

##### HANDLING DUPLICATE DATA

Apache Nutch handles duplication of the crawled data via its properties setting and commands

By default Nutch use the org.apache.nutch.crawl.MD5Signature class to calculate the digest of an
URL, this class calculates the digest using the MD5Hash function of the raw binary content of the page, if
no content is found then the URL is used.

The DeduplicationJob first groups fetched URLs by the digest (in your case both URLs should have the
same signature/digest) and marks all the URLs as duplicated, except the one with the highest score, if
both (or more) URLs have the same digest and the same score, then the one with the latest timestamp is
used instead

##### HYPERLINK INFORMATION FOR INDEXING AND RELEVANCE MODEL

##### Crawldb of apache nutch has all the data that has been crawled. A small python script was run all

##### the segments , and that script executed readseg api to get data from all the segments.

##### This api when called with parameters of to get title, url and content , created a dump for each

##### segment.

##### A Python script was ran on this data to create, individual record files with Recno, Title, Url,

##### Outlinks and Page Content which was then shared with Indexing person(Vatsal).

##### The content gathered by the python script from the data present in the segments has all the

##### information such as:


##### ● Record Number

##### ● Title

##### ● Urls

##### ● Outlinks

##### ● Page Content

##### Python file Path: apache-nutch-1.15/crawl_parse/src/parse_crawl.py

##### This output is then passed further for indexing and generating relevance model

### SOLR :

##### Every version of Nutch is built against a specific Solr version , So the compatible version with

##### Nutch-1.15 is Solr-7.3.1. We used Solr to monitor the data.

##### To start Solr

##### ./bin/solr start

##### Admin Console: http://localhost:8983/solr/#/

##### In addition, filters, normalizers and plugins allow Nutch to be highly modular, flexible and very

##### customizable throughout the whole process.

##### SELENIUM

##### Nutch do not fetch AJAX/JavaScript driven dynamic HTML content

##### In order to crawl webpages that rely on JavaScript/AJAX to dynamically load content we used

##### the Protocol-Selenium Plugin.

##### This plugin will load the pages that you're crawling in Selenium so that JavaScript will be

##### handled properly.

##### So we used selenium -2.42.2 with Nutch which is the compatible version

##### With selenium -2.42.2 we used Firefox-29.

##### DISCUSSION

##### We started implementing the crawler with Scrapy and Beautiful soup. But, the issue that we

##### faced was we were not able to get dynamic content from the websites as most of the websites

##### uses JavaScript / Ajax to load the content. We decided to use open source crawler Apache-Nutch

##### which was able to get dynamic content using a selenium plugin and firefox.

##### Monitoring for Nutch:

- Apache Nutch 1.X doesn’t have web-ui , to monitor the jobs and logs error and manage

##### crawling.

- Hence, I created a email script where after every couple of crawl jobs, it reads the logs

##### and send email to me, giving details of the jobs like :

##### ◦ Number of jobs ran

##### ◦ Number of urls processed.

##### ◦ Amount of time each job took.

##### ◦ Urls where Error Occurred , along with error

##### ◦ Report failed Jobs.


##### CONCLUSION

- Apache Nutch is highly scalable, robust and relatively feature rich crawler. Quality –

##### crawling can be biased to fetch "important" pages first. If we use Apache Nutch on AWS,

##### Google Cloud Computing it can be clustered among 100’s of machine at a time which

##### can result in to fast performance or can be run in distributed mode using big-data

##### mapreduce format.


### Vatsalkumar Patel VRP

### Indexing and Relevance

### ● Indexing and relevance based retrieval is perhaps the most important aspect of any search engine.

```
It directly impacts the performance of the search engine.
```
### ● Poor indexing and/or relevance models will result in slower and irrelevant results.

### ● For our project, we used Solr for indexing. Solr is a high performance search server built using

```
Apache Lucene core which provides indexing and other core information retrieval functionality.
```
### ● For our search engine, following figure depicts overall indexing architecture:

### ● Apache Nutch is used to crawl the web and get the documents. Once finished crawling, nutch

```
stores the data in three folders named crawldb, linkdb and segments, as described in the crawling
module. This data is then used by Solr Indexer to create index.
```
### ● Indexer uses schema.xml file. This file defines what kind of fields there are, what should be used

```
as primary key to uniquely identify documents, which fields are required and how to index and
search each field.
```
### ● Before being added to the index, documents go through the analysis phase of the Indexer which

```
transforms documents like lower-casing, removing stop words and stemming/lemmatizing etc.
After that, we have tokens of a document which can then be added to the index.
```
### ● Crawling script used by Harsh Verma does automatic indexing using following command as final

```
step:
bin/nutch solrindex http://localhost:8983/solr/nutch crawl/crawldb/ -linkdb
crawl/linkdb/ -dir crawl/segments/ -deleteGone -filter -normalize
○ This is basically example of incremental indexing as documents are transformed and
indexed as they are being crawled, this is one of major reason to use Solr as it saves a lot
of time.
○ Solr also allows easy merge of two separate crawls(both data and indexes) done on
separate machine, an aspect which was very helpful to us.
```

#### Vatsalkumar Patel VRP

#### Link Analysis

Webgraph

- Apache Nutch Crawler does not generate webgraph but it does generate a segments folder which
    stores actual content of the crawled and fetched documents. That can be used to create a
    webgraph using following nutch command.
    bin/nutch webgraph –segmentDir crawl/segments –webgraphdb crawl/webgraphdb
    Here, crawl/webgraphdb is the webgraphdb to be created or updated.
       - Some of the statistics for the webgraph are as follows:
          ○ Number of nodes: 117330
          ○ Number of links: 264243
          ○ Maximum number of in-links: 8427
             ■ URL: https://m.cricbuzz.com/
          ○ Maximum number of out-links: 98
Pagerank
- Pagerank is a query independent link analysis algorithm that is executed at indexing time and it
gives each document/URL a relevancy score based on a weighting scheme.
- In our project, after indexing and creating a webgraph, pagerank scores of all nodes are calculated
using following nutch command:
bin/nutch org.apache.nutch.scoring.webgraph.LinkRank –webgraphdb crawl/webgraphdb
- LinkRank is implementation of page rank provided by nutch. It uses TfIdf similarity as a
weighting scheme.
- Several pages with highest page ranking are as follows:
351.9641 [http://www.cricwaves.com/mobile/](http://www.cricwaves.com/mobile/)
345.50262 [http://www.cricwaves.com/cricket/tour/390/80/indian-premier-](http://www.cricwaves.com/cricket/tour/390/80/indian-premier-)
league-2018/Series.html
265.01923 [http://www.cricwaves.com/mobile/loadTeams](http://www.cricwaves.com/mobile/loadTeams)
263.91605 [http://www.cricwaves.com/cricket-polls](http://www.cricwaves.com/cricket-polls)
261.40536 [http://www.cricwaves.com/cricket/pages/index.html](http://www.cricwaves.com/cricket/pages/index.html)
261.09048 [http://www.cricwaves.com/cricket/news/articles/aSdwPsKJBP_drh-](http://www.cricwaves.com/cricket/news/articles/aSdwPsKJBP_drh-)
sevawcirc/icc-cricket-world-cup-2019-icc-confirmed-complete-
schedule-venues-fixtures.html
260.7134 [http://www.cricwaves.com/mobile/cricket-schedule](http://www.cricwaves.com/mobile/cricket-schedule)
260.7074 [http://www.cricwaves.com/mobile/Tours](http://www.cricwaves.com/mobile/Tours)
260.70566 [http://www.cricwaves.com/mobile/loadIcc](http://www.cricwaves.com/mobile/loadIcc)
260.70554 [http://www.cricwaves.com/mobile/articles](http://www.cricwaves.com/mobile/articles)
HITS
- Hyperlink-Induced Topic Search is a query dependent link analysis algorithm and thus is
executed at search time. It first creates a subgraph from a webgraph based on pagerank results,
gives hub and authority scores to the documents in this subgraph and returns the top documents
based on these scores.
- We wrote a code in Python to implement HITS algorithm. We used networkx library which
provides functions to create directional webgraph and also an implementation of HITS algorithm.


#### Vatsalkumar Patel VRP

- We got in-links information form web graph using following command:
    bin/nutch readlinkdb crawl/linkdb -dump inlinkDb
- This command will give list of all the docs/URLs and their in-links which we can use to build a
    subgraph.

Relevance Models

- We have used Vector Space Model that is in built to Solr in combination with Pagerank and HITS
    as our relevance model.
- Inbuilt vector space model uses TF-IDF based weighting scheme with cosine similarity between
    documents and queries.
- Vector Space Model:

```
Score(D, q) =
```
##### V ( D ) ⋅V ( q )

##### | V ( D )|| V ( q )|

```
○ where, V(D).V(q) is a dot product of weighted document and query
vectors while |V(D)| and |V(q)| are their euclidean norms.
○ Weighted vectors are calculated with tf-idf weighting scheme.
```
- On top of these calculations, Solr also incorporates query boosts and document length

normalization. More information on these boosts and Solr’s (Lucene’s) score functions can be found here:
https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/
TFIDFSimilarity.html

- Our Retrieval architecture is depicted in a figure below:


### Vatsalkumar Patel VRP

Topic-based page ranking

- I chose following query as topics. They are broad enough queries that they can serve as a topic in
    context of cricket.
       1. Cricket Betting
          0.25876647 https://betting.betfair.com/cricket/how-to-win-money-betting-on-
             cricket.html
          0.25876647 https://betting.betfair.com/cricket/
          0.25175008 https://www.cricketworld.com/cricket-betting/

Collaboration with UI (Chandhya)

- Data, indexes and pagerank scores are available in Solr server. So, information about Solr server
    and query syntax is passed on to Chandhya. Once the query results are available, they are passes
    on to the UI as json object. These results are already sorted according to pagerank scores so UI
    reads the json object and display the results.
- Similarly, Python code written for HITS algorithm runs a server to implement HITS algorithm.
    UI code hits this server with results from pagerank and get the results back from HITS algorithm
    in a json object. Just like before, results are already sorted based on hub and authority score so UI
    just reads the json object and display the results.
- I used about 15 queries (mostly about live match scores, famous players, tournaments such as
    Indian premier league, cricket world cup and governing bodies such as icc) to test the results.
    Results were judged by manually checking contents of top 10 webpages returned by our search
    engine.

Collaboration with Clustering (Debagrah)

- Pagerank results are passed on to Debagrah. Clustering module will look at these results and
    choose a cluster which contains most of these documents.
- Then, it shows top 15 documents from that cluster.


### Chandhya Thirugnanasambantham CXT

### User Interface

### Technologies Used

###### HTML

JavaScript
jQuery
CSS
Bootstrap
Java Servlets
Tomcat Server

##### Design

The design part has three layers which are described below:

- Presentation Layer
- Application Layer
- Data Access Layer
- Business Layer

##### Presentation Layer - User interface

Front end is rendered using HTML5/CSS and Javascript library and is powered by using Java Servlets.
AJAX calls are made asynchronous to the back-end.

### ● Bootstrap CSS library is used to design the page layout.

### ● The search feature allows to query for cricket players and corresponding statistics.

### ● The results of the query are shown in five different tabs.

### ● The web search engine has the following components:

### ○ The Logo

### ○ A search bar

### ○ Navigable tabs view to compare/contrast between different search results.

### ● The tabs to show results has the following four groupings:

### ○ Google - Shows results from google, using google search API.

### ■ Sourced from:

```
https://www.googleapis.com/customsearch/v1?
key=INSERT_YOUR_API_KEY&cx=017576662512468239146:omuauf_lfve&
q=lectures
Following are the 3 query parameters:
```
##### ● API key - Use the key query parameter to identify your application.

##### ● Custom search engine ID - Use cx to specify the custom search engine

```
you want to use to perform this search. The search engine must be
created with the Control Panel
```
##### ● Search query - Use the q query parameter to specify your search

```
expression
```
### ■ Placing a GET request to the above API returns results that are in json format.

### ○ Bing - Shows results from Bing, using Bing Search API.

### ■ Displays results from Microsoft Bing Search API.


### Chandhya Thirugnanasambantham CXT

### ■ Subscription Key

### ■ Custom Configuration Key

### ■ Custom Config ID

### ○ Vector Space + Pagerank + Hits :

### ■ Results from HITS server after applying HITS over pagerank results from Solr.

### ○ Vector Space + Pagerank + Flat Clustering:

### ■ Results from Clustering server after applying K-means clustering over Pagerank

```
results.
```
### ○ Vector Space + Pagerank + Query Expansion: Results from Query Expansion server

```
after expanding the query using relevance feedback
```
```
Tomcat server is being used. GET and POST requests are used to send requests to the
server, via AJAX calls. There are 3 controller programs that are written in Java.
```
Description of Individual Files

- CallHandler.java : Handles calls to individual APIs. HttpURLConnection class is used to make
    POST requests to individual APIs such as for the methods that are used - Bing call, Google Call,
    Solr call, Query Expansion Call and Hits call along with Clustering call.
- ClusterServletHandler.java : Handles AJAX requests that tie to K-means clustering
- QueryExpansionServletHandler.java : Handles AJAX request related to Query Expansion.

##### DataAccessLayer:

- The request that we get from the UI layer is routed to the corresponding

### server and then the result is given back to the Controller layer.

##### Business layer

- There are 3 servers running locally that contains the business logic for

### various relevance models.

### ◦ Server 1: Solr server for handling indexing and Pagerank requests

### ◦ Server 2: Python server for handling HITS/query expansion requests

### ◦ Server 3: Python server for handling Flat Clustering requests All

### requests from the Data Access layer are routed to these three servers.

### JSON is chosen as the data exchange format for ease of implementation

### and support by various programming languages.

##### Collaborations

Collaboration with student responsible for Indexing/Pagerank/HITS

Solr is used for Page Ranking and indexing. The server for pagerank and indexing exposes an
API for accessing the indexed results. The method creates the URL for a select query in Solr, and fetches
the output for that query in Solr.
Calling Solr server directly using the information provided by the student responsible for indexing and
Page Ranking.


### Chandhya Thirugnanasambantham CXT

API also allows us to set some control parameters for e.g number of pages to be returned or format of the
response. We have set the number of pages to be returned to be 15 and JSON format is used for the
exchange of the data.

The following fields are selected for display in the UI:

URL: the URL address of the page
Title: Title of the page.
Description: HTML content of the page (with HTML tags removed) – truncated to 200
characters for display in the UI as a short description about the webpage.

The HITS algorithm is implemented in python and hosted in a separate server by this student. So
the information about this python API was collected from this student to make the API call. Since the
HITS algorithm is dependent on the indexing results from Solr, results from Solr API was sent to this API
via POST and the response was collected back.

Collaboration with student responsible for Clustering:

##### ● Clustering algorithm K-means has been implemented and we have maintained the

```
clustering information in a file.
```
##### ● The file contains the url and the corresponding cluster number of that url.

##### ● The top results from solr are passed to the cluster based sorting algorithm. The most

```
relevant cluster is identified and the documents which belong to the same cluster are
retrieved. Top 15 results in the UI are shown, based on chosen cluster.
```
##### ● The response is similar as with Solr.

Collaboration with student responsible for Query Expansion:

##### ● The query expansion algorithm is implemented and hosted in the same server as HITS.

```
The input to the Query expansion API is the initial query string made by the user and the
initial Solr output.
```
##### ● Final output from the API is the expanded query string. This expanded string is then used

```
to retrieve a new set of results from the Solr API.
```
Comparison with Google and Bing:

In order to compare the results with Google and Bing results, Google web search API and Bing Search
API were used to retrieve their results. Following observations were made during the evaluation:

##### ● For certain queries which contained cricket player names, our results from

```
Pagerank/HITS/Clustering were closely in accordance with results from Google and Bing. We
believe this is due to the selection of very good seed URLs to cover most of the food recipe
possible. Also, from the type of pages that has been crawled, it is observed that the information
about different cricket matches is static information and has good keywords.
```
##### ● Using seed URLs from Google and Bing proved useful in retrieving the top pages during the

```
search. Especially, Wikipedia pages were immensely useful in improving our search results and
were retrieved by our search engine despite the limited number of inlinks and outlinks in
comparison to the entire web used by Google and Bing. This proved the effect of Vector Space
model combined with Pagerank.
```

### Chandhya Thirugnanasambantham CXT123730

##### Testing strategy

- Testing was done throughout the development of the project to provide immediate

##### feedback to students implementing the relevance models. Approximately 200 queries

##### were used to test the results of various models. About 50 queries were used in

##### collaboration with the students building the relevance models and the rest of queries were

##### generated based on various topics.

- When a particular page from the top results of Google/bing does not come up with our

##### relevance model, the page was first checked for existence and if present, the page rank

##### score of the page was compared with the top scoring page from our relevance model. In

##### few cases, the score was too low due to insufficient inlinks and outlinks and in many

##### cases, the page in question was not present in our collection.

##### Home page for Search


### Chandhya Thirugnanasambantham CXT123730

##### Search Results for Google

##### Search Results for Bing


### Chandhya Thirugnanasambantham CXT123730

##### Search Results for PageRank

##### Search Results for Hits:


### Chandhya Thirugnanasambantham CXT123730

##### Query Expansion


#### Debargha Ghosh DXG170014

### Clustering

- Performed K-means, single link, complete link and average link clustering
    K-means description
- **_k_** -means clustering is a method of vector quantization, originally from signal

### processing, that is popular for cluster analysis in Information Retrieval. k -means

### clustering aims to partition n observations into k clusters in which each observation belongs to

### the cluster with the nearest mean, serving as a prototype of the cluster.

- Given a set of observations (x 1 , x 2 , ..., x _n_ ), where each observation is a _d_ -dimensional real
    vector, _k_ -means clustering aims to partition the _n_ observations into _k_ (≤ _n_ ) sets S = { _S_ 1 , _S_ 2 , ..., _Sk_ }
    so as to minimize the within-cluster sum of squares (WCSS) (i.e. variance). Formally, the
    objective is to find:

where **_μ_** _i_ is the mean of points in _Si_. This is equivalent to minimizing the pairwise squared
deviations of points in the same cluster:

- The equivalence can be deduced from identity.
- Because the total variance is constant, this is equivalent to maximizing the sum of squared
    deviations between points in _different_ clusters (between-cluster sum of squares, BCSS)

How it works:

- To process the learning data, the K-means algorithm in data mining starts with a first group of
    randomly selected centroids, which are used as the beginning points for every cluster, and then
    performs iterative (repetitive) calculations to optimize the positions of the centroids

It halts creating and optimizing clusters when either:

- The centroids have stabilized — there is no change in their values because the clustering has been
    successful.
- The defined number of iterations has been achieved.
Hierarchical clustering description
- **_Hierarchical clustering_** treats each data point as a singleton cluster, and then successively
merges clusters until all points have been merged into a single remaining cluster. A hierarchical
clustering is often represented as a dendrogram.
- In complete-link (or complete linkage) hierarchical clustering, we merge in each step the two
clusters whose merger has the smallest diameter (or: the two clusters with the
smallest maximum pairwise distance).


#### Debargha Ghosh DXG170014

- In single-link (or single linkage) hierarchical clustering, we merge in each step the two clusters
    whose two closest members have the smallest distance (or: the two clusters with the
    smallest minimum pairwise distance).
- Complete-link clustering can also be described using the concept of clique. Let dn be the diameter
    of the cluster created in step n of complete-link clustering. Define graph G(n) as the graph that
    links all data points with a distance of at most dn. Then the clusters after step n are the cliques of
    G(n). This motivates the term complete-link clustering.

Single-link clustering can also be described in graph theoretical terms. If dn is the distance of the two
clusters merged in step n, and G(n) is the graph that links all data points with a distance of at most dn,
then the clusters after step n are the connected components of G(n). A single-link clustering also closely
corresponds to a weighted graph's minimum spanning tree.

is a compromise between the sensitivity of complete-link clustering to outliers and the tendency of
single-link clustering to form long chains that do not correspond to the intuitive notion of clusters as
compact, spherical objects.

##### Implementation

I obtained the web crawled data from Harsh who was responsible for crawling.

We used the command $NUTCH_RUNTIME_HOME/bin/nutch dump -segment crawl/segments -
outputDir crawl/dump_dir_op

The dump directory had json files and html files. The json files contained the urls and the corresponding
directory where the html of the url file is.

I read the urls from the json files and read the data from the corresponding html location.

Then performed clustering on the corresponding data after removing the tags and cleaning it up.

After running the four clustering algorithms I generated four text files for each clustering method. These
four text files were of the format

url : #cluster_number

I collaborated with Vatsal who was responsible for incremental indexing the crawled web pages and for
the link analysis of the Web graph that was retrieved by crawling. The top urls obtained by him was
utilized. The cluster that had the maximum number of the urls was chosen and the top 15 from that cluster
was displayed.

I collaborated with Chandhya who was responsible for UI. I gave the results of the cluster as the output


#### Debargha Ghosh DXG170014

Choosing the value of K

Determining the optimal number of clusters in a data set is a fundamental issue in partitioning
clustering, such as k-means clustering, which requires the user to specify the number of clusters k to be
generated. There is no definitive answer to this question. The optimal number of clusters is somehow
subjective and depends on the method used for measuring similarities and the parameters used for
partitioning. A simple and popular solution consists of inspecting the dendrogram produced
using hierarchical clustering to see if it suggests a particular number of clusters. This approach is also
subjective.

I used silhouette Coefficient to determine the value of K

The Silhouette Coefficient is calculated using the mean intra-cluster distance (a) and the mean nearest-
cluster distance (b) for each sample. The Silhouette Coefficient for a sample is (b - a) / max(a, b). To
clarify, b is the distance between a sample and the nearest cluster that the sample is not a part of. Note
that Silhouette Coefficient is only defined if number of labels is 2 <= n_labels <= n_samples - 1.

The best value is 1 and the worst value is -1. Values near 0 indicate overlapping clusters. Negative values
generally indicate that a sample has been assigned to the wrong cluster, as a different cluster is more
similar.

I generated the silhouette coefficients from 10 to 15 and 16 to 21 and found that the k value of 15 gives
the best result. Hence I chose k as 15.

State clearly how many queries you have used to test the impact of the results of each clustering
method, how you have generated them and how you have judged the results of your relevance
models :

I have used 20 queries to test the impact of the results of Kmeans clustering method.

The queries were generated keeping the wide diversity of cricket in mind. The queries encompass a wide
range of topics related to cricket like cricket world cup, famous players like Rahul Dravid, best cricketer
in the world, the ongoing IPL, IPL teams like Royal Challengers Bangalore, Chennai Super Kings etc.

The agglomerative clustering methods were giving Memory Error for 120k dataset so we did it on 10k
dataset.

If the urls are a match they would show the result on the UI.

The results were judged by the url and url content which indicated if the results were relevant to the query
or not.


#### Debargha Ghosh DXG170014

Discuss how you have decided to select the queries for the demonstration of your search engine.
Provide three examples of the queries and the results produced by your search engine and the
clusters that you have created.

For demonstration purpose I have decided to select queries from the test cases which had given relevant
results.

Few examples are as follows:



##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

### QUERY EXPANSION

- The four algorithms used for query expansion were -
    ◦ Rocchio Algorithm
       The idea behind the concept is to expand query using the following method -
    - Association cluster

The idea is that stems which co-occur frequently inside documents have a synonymity association.

- Metric cluster

The idea is that the stems which occur far apart in the document are less co-related to the terms that occur
closer like in the same sentence.

- Scalar cluster

The idea is that two stems are more co-related if they have similar neighborhood.

- 20 QUERIES TO TEST ROCCHIO ALGORITHM (SURYA ALLA)

###### QUERY RELEVANT TERMS

```
CSK IPL,Dhoni,KKR
```
```
India BCCI, International, ODI
```
```
Lords England, crowd, Test
```
```
Delhi Match,Punjab,Runs
```
```
World Cup WC, ODI, News
```
```
Pakistan Cricket, women, tournament
```
```
Ricky Test, Ponting, Australia
```
```
Champions Trophy ICC, Match, Final
```
```
Bowler Match, Rounder,India
```
```
KKR VIVO, Kolkata, CSK
```
```
Australia New, West,England
```

BCCI India, IPL, Supreme

Sydney Sixers, Australia, Scorchers

Jasprit Bumrah, Good, Over

RCB IPL, match, RR

England Wales,ENG,Australia

Mumbai Kolkata,Bangalore, Match

RR Highlights, IPL, VIVO

CPL League, Premier, Caribbean

Waqar Pakistan, Younis, bowling

###### • 20 QUERIES TO TEST ROCCHIO ALGORITHM (ANUGREH RAINA)

###### QUERY RELEVANT TERMS

Lara Test, West, Indies

Oval Ground, ICC, match

IPL May, Indian, Match

SRH HYDERABAD, DC, VIVO

Virat Kholi, Run, Delivery

Perth Cricket, Match, Run

Batsman Impressed, Shoes, Cricketer

Dale Steyn, over, length

MI Pollard, Mumbai, IPL

MS Dhoni, Wickets, Melbourne

West Indies Cricket, Series, Cup

ICC Council, Members, International

Africa RSA, South, World

Rohit Kohli, length, run

Big Bash League, Thunder, Strikers

Holding Michael, LBW, Test


```
Zealand New, ODI, Cup
```
```
Sachin Tendulkar, Anjali, India
```
```
Cricket ODI, History, Greatest
```
```
Melbourne Perth, Match, Hobart
```
###### OBSERVATIONS:

- Rocchio algorithm worked well with most queries and expanded on relevant terms, but some
    query result were highly skewed as the crawled data was topic specific. Changing the alpha and
    beta values also helped in some cases.
    ◦ RELEVANT EXAMPLES - Searching Lara, India, CSK gave very relevant keywords in the
       expanded query. Crawled data was more in sync with these search queries and thus we got
       relevant expanded query.
    ◦ IRRELEVANT EXAMPLES - Searching Pakistan, Bowler, batsmen didn’t give relevant
       search and query expansion terms, instead giving ambiguous results. It may be the case that
       the crawled data was not sufficient for such a query.

Pseudo-relevance feedback: Associative, Metric, Scalar clustering

```
Sno Query Expanded Query
using Metric
```
```
Expanded Query
using Association
```
```
Expanded Query
using Scalar
```
```
1 Ashes England, Australia,
Test
```
```
Australia, series,
ODI
```
```
Crowd, matches,
England
```
```
2 India BCCI, ODI,
Domestic
```
```
Stadium, BCCI,
International
```
```
Australia, Won,
BCCI
```
```
3 Australia England, Cricket,
Indies
```
```
South, Western,
Zealand
```
```
England, India,
Score
```
```
4 Test Ground, England,
SRL
```
```
Match, Series,
Videos
```
```
Team, List, Match
```
```
5 Virat Kohli, India,
Matches
```
```
IPL, Year, Team RCB, India, Runs
```
```
6 Kallis AB, Tour, ICC Africa, South,
Cricket
```
```
Wickets, Steyn,
Runs
```
```
7 MS Dhoni, Team, World Final, Team, CSK ODi, India, April
```

8 Googly Spinner, Australia,
Tour

```
England, Windies,
Leg
```
```
Latest, Spinner,
Driving
```
9 DC Delhi, VIVO, RR Predictions, SRH,
Delhi

```
Match, IPL,
Capitals
```
10 Clark Australia, Bailey,
Innings

```
Yorkshire, Day,
Cricket
```
```
Fifty, Over, Runs
```
11 Lords Aussies, Matches,
Series

```
England, ODI, Ben Win, Latest,
Crowd
```
12 England South, Series, ENG Series, Australia,
Report

```
New, West, Score
```
13 Dravid Rahul, Matches,
World

```
India, Cricket,
Series
```
```
ODI, Rahul, Test
```
14 Captain Squad, Batsmen,
Rahul

```
Sharma, Reddy,
Chris
```
```
Singh, Sri, Kings
```
15 IPL VIVO, April, Live RR, MI, April Prediction, CSK,
VIVO

16 ODI Stadium, Crowd,
Number

```
Ground, Videos,
Series
```
```
India, Series,
Match
```
17 Domestic New, India, Cricket Women, April,
Results

```
BCCI, Team,
News
```
18 Rohit Jason, Sharma,
Delivery

```
Forward, Length,
Cricket
```
```
ODI, Australia,
Runs
```
19 SRH Hyderabad, Live,
VIVO

```
Sunrisers, RCB,
Match
```
```
Preview,
Hyderabad, RR
```
20 ICC Live, England,
Council

```
World, Cricket,
India
```
```
Test, International,
Africa
```
21 Africa RSA, South, Match World, South,
Videos

```
ODI, Test, South
```
22 BCCI Trophy, March,
Won

```
April, India,
Cricket
```
```
Domestic, Women,
News
```
23 Gilchrist Punjab, Kumar, IPL World, Tour,
Match

```
Series, ODI,
Australia
```
24 Kapil Dev, Matches,
Crowd

```
India, Tour,
Wickets
```
```
Dev, England, Cup
```

25 CSK Kings, RR, IPL VIVO, Chennai,
MI

```
IPL, Super, Dhoni
```
26 PCB Cricket, Pakistan,
About

```
Board, Chairman,
National
```
```
Development,
Team,
International
```
27 Lara West, Windies,
Cricket

```
Trinidad, Test,
Series
```
```
Over, Brian,
Academy
```
28 Muralitharan Lbw, Cricket, ICC Sri, Lanka,
Wickets

```
Mailinga, Lanka,
Series
```
29 Kumar Sandeep, Forward,
Sharma

```
XI, Bhuvneshwar,
Runs
```
```
Point, Length,
Runs
```
30 Warne Shane, Won,
Matches

```
World, Profile,
Series,
```
```
Cup, Test, Shane
```
31 Steve Warner, Cricket,
News

```
Smith, Australia,
News
```
```
Brexit, May,
Cricket
```
32 Pakistan Women, Trophy,
Inter

```
Tournament, PCB,
Day
```
```
Arab, Cup,
England
```
33 Sydney Sixers, Strikers,
Stars

```
Cricket, Perth,
Melbourne
```
```
Australia, Perth,
Won
```
34 Ganguly Sourav, India,
Australia

```
Cricket, Sourav,
ODI
```
```
World, Test, India
```
35 Perth City, West,
Australia

```
Western, May,
Australian
```
```
Australia, Ground,
Match
```
36 RR Highlights, MI,
Match

```
IPL, SRH, April VIVO, News, CSK
```
37 Adelaide Melbourne,
England, Match

```
Perth, Cricket,
India
```
```
Australia, Sydney,
Day
```
38 Lanka IPL, Sri, Handed South, Match, ODI News, Team PM

39 Pollock Shaun, Africa, Show South, Cricket,
ODI

```
Shaun, Cup,
Wickets
```
40 KKR Kolkata, DC, Match Knight, Score,
Preview

```
IPL, MI, Riders
```

```
41 Khan Ajmal, Younis,
Defense
```
```
Delivery, Misbah,
Stumps
```
```
Ali, Leg, Ajmal
```
```
42 Bash League, Twenty,
Season
```
```
Australia, Cricket,
Melbourne
```
```
Perth, January,
Sydney
```
```
43 Indies West, Series, World Crowd, Videos,
News
```
```
First, ODI, Gayle
```
```
44 Kotla IPL, Delhi, Match Cricket, IPL,
Kings
```
```
Stadium, Capitals,
Delhi
```
```
45 RCB Challengers, IPL,
Preview
```
```
VIV, RR, Match CSK, Score, Virat
```
```
46 Rahul Punjab, Lokesh, IPL Bowls, Length,
Punjab
```
```
Point, Over, IPL
```
```
47 Ponting World, Ricky,
Cricket
```
```
Test, Series, News Ricky, Latest, ODI
```
```
48 KXIP Stuart, Pulse, Punjab Match, Highlights,
IPL
```
```
Videos, MI, T-
shirts
```
```
49 Adam Zampa, Delivery,
Point
```
```
Straight, Tour,
Over
```
```
Zampa, Cap, short
```
```
50 Sachin Bangalore,
Tendulkar, World
```
```
India, Matches,
Tendulkar
```
```
Anjali,
Tendulakar, News
```
##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### OBSERVATION

- Metric cluster performed much better than associative and scalar cluster. Scalar and Associative
    were almost similar with scalar being a little more relevant. This may be due to the fact that the
    neighbouring terms play an important role in query expansion and bring out more relevant
    keywords.

QUERY 1: VIRAT KOHLI

LOCAL DOCUMENT SET


###### • LOCAL DOCUMENT 1

Menu\nHome\nLive Scores\nSchedule\nIPL 2019\nVideos\nICC Rankings\nIPL Points Table\
nFacebook\nTwitter\nPinterest\nInstagram\nEmail\nSearch\nGo\nLatest cricket news and updates\
nCategory archive\nVirat Kohli\nPosted on\nApril 15, 2019 April 15, 2019\nin\nDinesh Karthik / India /
News / Rishabh Pant / Virat Kohli\nBCCI announces India’s 15-man squad for the ICC Cricket World
Cup 2019\nThe Board of Control for Cricket in India (BCCI) has announced the 15-man India squad for
the ICC World Cup 2019, to ... Keep Reading\nPosted on\nApril 9, 2019\nin\nSarah Taylor / Virat
Kohli\nWhen Virat Kohli was desperate to meet England woman cricketer Sarah Taylor at 5 AM\nIndian
talisman Virat Kohli has emerged as one of the sought after cricketers across the world. However, on his
way to the ... Keep Reading\nPosted on\nApril 7, 2019\nin\nDelhi Capitals / IPL / Royal Challengers
Bangalore / Twitter Reactions / Virat Kohli\nIPL 2019: Twitter Reactions – RCB go down meekly to DC,
Virat Kohli’s side lose sixth game on the trot\nShreyas Iyer’s Delhi Capitals (DC) beat Virat Kohli’s
Royal Challengers Bangalore (RCB) by 4 wickets at the M. Chinnaswamy Stadium in Bengaluru ... Keep
Reading\nPosted on\nApril 7, 2019\nin\nIPL / Royal Challengers Bangalore / Virat Kohli\nIPL 2019
(RCB vs DC): The reason why Virat Kohli & Co. is wearing green jersey at Chinnaswamy stadium\
nVirat Kohli-led Royal Challengers Bangalore (RCB) came out wearing green jerseys against Delhi
Capitals (DC) at the M. Chinnaswamy stadium in Bengaluru ... Keep Reading\nPosted on\nApril 6, 2019
April 6, 2019\nin\nAndre Russell / IPL / Kolkata Knight Riders / Royal Challengers Bangalore / Virat
Kohli\nIPL 2019: Andre Russell’s blitz leaves RCB captain Virat Kohli shell-shocked\nAndre Russell
was at it again as he slammed 48 runs off just 13 deliveries helping Kolkata Knight Riders (KKR) an
unlikely ... Keep Reading\nPosted on\nApril 4, 2019 April 4, 2019\nin\nRoyal Challengers Bangalore /
Virat Kohli\nMixed Gender T20: Danielle Wyatt excited to witness the battle between Kohli and Gozzy\
nThe likes of Virat Kohli, Mithali Raj, Harmanpreet Kaur and Veda Krishnamurthy has called on fans to
support a unique mixed-gender T20 ... Keep Reading\nPosted on\nApril 3, 2019\nin\nIPL / Marcus
Stoinis / Rajasthan Royals / Royal Challengers Bangalore / Video / Virat Kohli\nIPL 2019: WATCH –
Marcus Stoinis hits a monstrous six off Jofra Archer, Virat Kohli cheers\nMarcus Stoinis made his debut
for Royal Challengers Bangalore (RCB) on Tuesday in the IPL 2019 Match 14 against Rajasthan Royals
at ... Keep Reading\nPosted on\nApril 3, 2019\nin\nHarmanpreet Kaur / Mithali Raj / Virat Kohli\nVirat
Kohli, Harmanpreet Kaur and Co. to feature in a mixed-gender T20 match\nRoyal Challenge Sports
Drink launched a campaign #ChallengeAccepted with an aim to break gender-based stereotypes in
cricket. The idea behind the campaign ... Keep Reading\nPosted on\nApril 3, 2019\nin\nAB de Villiers /
IPL / Rajasthan Royals / Royal Challengers Bangalore / Shreyas Gopal / Virat Kohli\nIPL 2019: Shreyas
Gopal in disbelief after dismissing Virat Kohli and AB de Villiers in the same match\nVirat Kohli and
AB de Villiers are batsmen of such great stature that their emphatic dismissals stand out in memories.
And when ... Keep Reading\nPosted on\nApril 2, 2019\nin\nIPL / Rajasthan Royals / Royal Challengers
Bangalore / Shreyas Gopal / Virat Kohli\nIPL 2019: WATCH – Shreyas Gopal bamboozles Virat Kohli
with a perfect wrong-un (RR vs RCB)\nRoyal Challengers Bangalore (RCB) skipper Virat Kohli was
bamboozled by Rajasthan Royals’ bowling all-rounder Shreyas Gopal in Match 14 of the IPL ... Keep

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

Reading\nPosted on\nApril 1, 2019\nin\nIndia / Virat Kohli\nVirat Kohli proud as India retain ICC Test
championship mace for third consecutive year\nThe International Cricket Council (ICC), on Monday,
announced that Virat Kohli-led India have won the ICC Test Championship mace and prize money ...
Keep Reading\nPosted on\nApril 1, 2019 April 1, 2019\nin\nIPL / Royal Challengers Bangalore /
Sunrisers Hyderabad / Virat Kohli\nIPL 2019: Virat Kohli left dejected after RCB’s horror loss against
SRH\nVirat Kohli-led Royal Challengers Bangalore (RCB) were handed a third straight defeat in the
ongoing season of the Indian Premier League (IPL) ... Keep Reading\nPosted on\nMarch 29, 2019 March
29, 2019\nin\nIPL / Lasith Malinga / Mumbai Indians / Players / Royal Challengers Bangalore / Video /
Virat Kohli\nWATCH: The no-ball error that left Virat Kohli fuming at umpires during RCB vs MI clash
in IPL 2019\nCaptain Virat Kohli blasted the umpires for failing to call a no ball on the last delivery as
his side Royal Challengers ... Keep Reading\nPosted on\nMarch 29, 2019 March 29, 2019\nin\nIPL /


Mumbai Indians / Players / Rohit Sharma / Royal Challengers Bangalore / Virat Kohli\nIPL 2019: After
Virat Kohli, Rohit Sharma expresses discontent on no-ball mishap during RCB vs MI clash\nIn a last-
over thriller, Mumbai Indians defeated Royal Challengers Bangalore by 6 runs at M. Chinnaswamy
Stadium yesterday evening. But it was ... Keep Reading\nPosted on\nMarch 28, 2019 March 28, 2019\
nin\nIPL / Mitchell Johnson / Players / Royal Challengers Bangalore / Virat Kohli\nIPL 2019: Mitchell
Johnson takes a subtle dig at RCB captain Virat Kohli\nRoyal Challengers Bangalore (RCB) winning the
Indian Premier League (IPL) trophy was a dream that many of its ardent fans saw when ... Keep
Reading\n1\n2\n3\n...\n64\nNext »\nMust Watch Videos\nApril 15, 2019\nIPL 2019 – WATCH: Ziva
Dhoni win hearts with her kiss to Suresh Raina, award to Imran Tahir\nIPL 2019\nApril 15, 2019\nIPL
2019 – WATCH: Ziva Dhoni win hearts with her kiss to Suresh Raina, award to Imran Tahir\nApril 13,
2019\nIPL 2019 – WATCH: Rishabh Pant plays with Shikhar Dhawan’s son Zoravar after DC beat KKR
in Kolkata\nICC World Cup 2019\nApril 16, 2019\nICC Cricket World Cup 2019: Dinesh Karthik reacts
to being picked in India’s 15-man squad\nFrom Twitter\nApril 12, 2019\nIPL 2019 – Twitter Reactions:
Shikhar Dhawan helps DC to destroy KKR at the Eden Gardens\nApril 11, 2019\nIPL 2019: Cricketing
world reacts as CSK beat RR on last ball after the no-ball error\nFacebook\nTwitter\nPinterest\
nInstagram\nEmail\n© 2018. The Cricket Times. All rights reserved. CreativeBrains Retail (OPC)
Private Limited.\nLatest Cricket News, Live Scores, Match Commentary, Schedule, Results, ICC
Rankings, Stats and Videos\nNever Miss Any Update, Download Our App Today...\nEmail:
contact@crickettimes.com Phone: +91 75250 22888 Address: 206, Gulmarg Apartment, Moti Nagar,
Lucknow, Uttar Pradesh, 226004\nIPL 2019\nSitemap\nAbout Us\nGet the App\nCricket Betting Tips\
nIPL Points Table\nGo to\nTop\n

LOCAL VOCAB LIST

['aamir', 'ab', 'ability', 'about', 'above', 'absence', 'absolute', 'academy', 'according', 'accumulated', 'accurate',
'achieve', 'achievement', 'achievements', 'across', 'actions', 'active', 'actress', 'ad', 'add', 'address', 'adds',
'adelaide', 'adjectives', 'admitted', 'adorable', 'advances', 'advertise', 'advertising', 'afghanistan', 'africa',
'after', 'again', 'against', 'age', 'aggressive', 'ago', 'agree', 'ahead', 'aiadmk', 'aim', 'airport', 'aishwarya', 'ali',
'alia', 'alive', 'almanack', 'almanacks', 'almost', 'along', 'already', 'also', 'altercations', 'although', 'always',
'am', 'amazing', 'among', 'analysis', 'analyze', 'andre', 'android', 'anil', 'announced', 'announces', 'another',
'antigua', 'anuksha', 'anushka', 'anuskha', 'anwar', 'anyone', 'anytime', 'apart', 'apartment', 'app', 'appeared',
'appears', 'appointed', 'appreciated', 'apprentice', 'approaches', 'apps', 'april', 'ar', 'archer', 'archive', 'ardent',
'arjuna', 'around', 'article', 'articles', 'ascended', 'ashes', 'ashwin', 'asia', 'asset', 'assuredly', 'athletes', 'atindra',
'attribute', 'auction', 'audio', 'australia', 'australians', 'australias', 'availability', 'available', 'average',
'averaged', 'averages', 'avg', 'award', 'awarded', 'awards', 'away', 'azam', 'azhar', 'b', 'babar', 'bachchan',
'back', 'bad', 'badly', 'bags', 'balaji', 'ball', 'balls', 'bamboozled', 'bamboozles', 'ban', 'bangalore', 'bangalores',
'bangla', 'bangladesh', 'banglore', 'bank', 'bar', 'barren', 'based', 'bat', 'batsman', 'batsmen', 'batting', 'battle',
'battling', 'bay', 'bcci', 'beat', 'beating', 'beats', 'beautiful', 'became', 'become', 'becomes', 'before', 'begin',

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

'beginning', 'behind', 'being', 'belief', 'believe', 'below', 'bench', 'bengaluru', 'best', 'better', 'betting',
'between', 'bhatt', 'bhavna', 'bhawan', 'bhuvi', 'bhuvneshwar', 'big', 'biggest', 'biopic', 'birthday', 'blame',
'blasted', 'blitz', 'blogger', 'blogs', 'board', 'boasted', 'bollywood', 'bolt', 'bonus', 'border', 'born', 'both',
'bought', 'bowed', 'bowling', 'box', 'boy', 'bradman', 'branch', 'break', 'breaking', 'breaks', 'breather',
'brendon', 'brian', 'brilliantly', 'broke', 'broken', 'brother', 'brought', 'bump', 'bumrah', 'bunch', 'business',
'buvanesh', 'buys', 'c', 'calendar', 'calibre', 'call', 'called', 'came', 'campaign', 'can', 'canberra', 'cap', 'cape',
'capitals', 'captain', 'captaincy', 'captained', 'captains', 'cardiff', 'career', 'casuals', 'catch', 'categories',
'category', 'cause', 'cb', 'cbi', 'celeb', 'celebrated', 'celebrates', 'celebrating', 'celebrities', 'celebrity',
'centuries', 'centurion', 'century', 'ceremony', 'chahal', 'challenge', 'challengeaccepted', 'challengers',
'champion', 'champions', 'championship', 'chance', 'chances', 'change', 'changes', 'chanu', 'chappell',
'charity', 'chart', 'chases', 'chasing', 'check', 'cheeku', 'cheers', 'chennai', 'cheteshwar', 'children',


'chinnaswamy', 'chris', 'cities', 'clarke', 'clash', 'clean', 'clear', 'clearly', 'click', 'clicked', 'clicking', 'clinch',
'clinching', 'close', 'co', 'coach', 'coaching', 'colombo', 'column', 'com', 'combined', 'come', 'comes',
'coming', 'commanding', 'comment', 'commentary', 'commercial', 'commonly', 'commonwealth', 'compare',
'complete', 'concluded', 'conditions', 'conferred', 'confirmed', 'connect', 'conquering', 'consecutive',
'considered', 'consistency', 'contact', 'continue', 'continued', 'continues', 'contract', 'contribution',
'contributor', 'control', 'controversy', 'conversation', 'conversion', 'converting', 'copyright', 'could', 'council',
'count', 'countries', 'country', 'countrys', 'county', 'couple', 'couples', 'crazy', 'creativebrains', 'crew',
'cricadium', 'cricindeed', 'cricket', 'cricketer', 'cricketers', 'cricketing', 'crickettimes', 'criminal', 'crore',
'crossed', 'csk', 'culminating', 'cummins', 'cup', 'current', 'currently', 'cwc', 'd', 'dance', 'daniel', 'danielle',
'data', 'dating', 'day', 'days', 'dc', 'de', 'debate', 'debut', 'december', 'declared', 'deep', 'deepika', 'defeat',
'defeated', 'dejected', 'delhi', 'deliveries', 'delivery', 'depleted', 'depth', 'deserves', 'desperate', 'despite',
'destroy', 'destroying', 'detail', 'details', 'dev', 'dhaka', 'dharamshala', 'dhawan', 'dhoni', 'dhonis', 'did', 'didn',
'didnt', 'die', 'diet', 'different', 'differently', 'dig', 'diljit', 'dilshan', 'dimuth', 'dinesh', 'disbelief', 'disclaimer',
'discontent', 'disha', 'dismay', 'dismissals', 'dismissing', 'disqualified', 'distributions', 'dive', 'do', 'doesn',
'doing', 'domestic', 'dominance', 'dominated', 'dominating', 'domination', 'don', 'dosanjh', 'double', 'doubt',
'down', 'download', 'dream', 'drink', 'drive', 'du', 'due', 'duo', 'durban', 'during', 'duty', 'each', 'earlier', 'early',
'easily', 'easy', 'eden', 'education', 'eighth', 'elder', 'elections', 'electrifying', 'elevating', 'eleventh', 'else',
'email', 'emerged', 'emergence', 'emerging', 'emphatic', 'end', 'ended', 'ends', 'enforce', 'engines', 'england',
'english', 'engvind', 'enjoy', 'enough', 'enrolled', 'entertainment', 'entire', 'epaper', 'episode', 'era', 'error',
'escaped', 'especially', 'eve', 'even', 'evening', 'event', 'eventually', 'ever', 'every', 'evolve', 'evolved', 'exam',
'excellence', 'excellent', 'excited', 'existence', 'experience', 'expertise', 'explained', 'express', 'expressed',
'expresses', 'extend', 'extending', 'extensive', 'eye', 'fab', 'facebook', 'faced', 'faces', 'fact', 'facts', 'faf', 'failed',
'failing', 'fame', 'family', 'famous', 'fan', 'fans', 'fantasy', 'far', 'fascinating', 'fashionable', 'fast', 'faster',
'fastest', 'father', 'fatullah', 'favourite', 'fb', 'feature', 'featured', 'february', 'field', 'fiery', 'fifth', 'fifties', 'fifty',
'fighting', 'figure', 'film', 'final', 'finally', 'finals', 'financial', 'fined', 'finest', 'finish', 'finishing', 'fired', 'first',
'fit', 'fitness', 'five', 'fleming', 'flood', 'follow', 'followed', 'followers', 'food', 'fooled', 'football', 'forbes',
'forced', 'form', 'format', 'formats', 'former', 'formidable', 'forward', 'foundation', 'four', 'fourth', 'fraternity',
'free', 'frenzy', 'from', 'fuming', 'fun', 'furious', 'gained', 'gambhir', 'game', 'gandhi', 'ganguly', 'gardens',
'gary', 'gautam', 'gavaskar', 'gayle', 'gender', 'general', 'generation', 'gentleman', 'get', 'gets', 'getter', 'getting',
'given', 'glenn', 'go', 'god', 'gods', 'goenka', 'goes', 'going', 'gone', 'good', 'google', 'gopal', 'got', 'govt',
'gozzy', 'grade', 'great', 'greater', 'greats', 'green', 'greg', 'group', 'grown', 'guest', 'gully', 'gulmarg', 'gulzar',
'guptill', 'guwahati', 'guy', 'had', 'half', 'halts', 'hambantota', 'hand', 'handed', 'hander', 'hanging', 'hankering',
'harare', 'hardcore', 'hardik', 'harmanpreet', 'harry', 'has', 'hate', 'having', 'head', 'headlines', 'hearts', 'heat',
'helped', 'helping', 'helps', 'hence', 'high', 'highest', 'highlighting', 'highlights', 'highly', 'hilarious', 'himself',
'hints', 'historic', 'history', 'hits', 'hitting', 'hobart', 'holder', 'holds', 'home', 'honors', 'honour', 'hopes',
'hoping', 'horoscope', 'horror', 'hosts', 'housewife', 'however', 'hrithik', 'hudson', 'hug', 'huge', 'hugely',
'hundred', 'hundreds', 'hyderabad', 'ian', 'icc', 'idea', 'if', 'importance', 'important', 'improved', 'imran',
'incident', 'incidentally', 'includes', 'including', 'ind', 'india', 'indian', 'indians', 'indias', 'indies', 'individual',

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

'indore', 'indvsl', 'infographic', 'information', 'injured', 'injury', 'inning', 'innings', 'innovative', 'inr',
'inspiration', 'inspired', 'instagram', 'instinctively', 'integral', 'intelligent', 'interest', 'interestingly',
'intermediate', 'international', 'internationals', 'internet', 'into', 'introducing', 'invaded', 'invades', 'involved',
'ipad', 'iphone', 'ipl', 'ireland', 'ishant', 'italy', 'iyer', 'jac', 'jaden', 'jain', 'jaipur', 'jansatta', 'january', 'jasprit',
'jawaharlal', 'jayasuriya', 'jersey', 'jerseys', 'jobs', 'joe', 'jofra', 'johnson', 'join', 'joint', 'journey', 'july', 'june',
'junior', 'just', 'k', 'kagiso', 'kajol', 'kalank', 'kambli', 'kane', 'kanpur', 'kapil', 'kapoor', 'karisma', 'karthik',
'karunaratne', 'kaur', 'keen', 'keep', 'keeps', 'kept', 'kerala', 'kevin', 'key', 'khan', 'khawaja', 'khel', 'kids',
'kings', 'kingston', 'kirstens', 'kiss', 'kkr', 'knight', 'knockout', 'know', 'known', 'kohli', 'kohlis', 'kolkata',
'kovind', 'krishnamurthy', 'kuala', 'kumar', 'kumble', 'kxip', 'lakh', 'landmark', 'languages', 'lanka', 'lankan',
'lankans', 'lara', 'lasith', 'last', 'late', 'lately', 'later', 'latest', 'lauds', 'launched', 'lawyer', 'lead', 'leader',
'leading', 'leads', 'league', 'lean', 'learn', 'learned', 'least', 'leave', 'leaves', 'led', 'left', 'leg', 'legend',


'legendary', 'less', 'let', 'level', 'life', 'lifestyle', 'like', 'likes', 'limit', 'limited', 'line', 'links', 'lionel', 'list',
'literally', 'little', 'live', 'll', 'load', 'lok', 'loksatta', 'long', 'longer', 'longest', 'look', 'looked', 'looking', 'lose',
'losing', 'loss', 'lost', 'lot', 'lottery', 'loves', 'ltd', 'luckily', 'lucknow', 'lucky', 'lumpur', 'm', 'mace', 'madame',
'made', 'mail', 'mailchimp', 'main', 'maintained', 'maintaining', 'maintains', 'major', 'majority', 'make',
'makes', 'making', 'malaysia', 'malinga', 'man', 'managed', 'management', 'mandhana', 'march', 'marcus',
'mark', 'marriage', 'married', 'massive', 'master', 'mastering', 'match', 'matches', 'maunganui', 'maxwell',
'maxwells', 'may', 'mccullum', 'mcg', 'mean', 'meant', 'media', 'meekly', 'meet', 'melbourne', 'member',
'memes', 'memories', 'men', 'menu', 'merchant', 'message', 'messi', 'met', 'metoo', 'mh', 'mi', 'michael',
'might', 'milestone', 'million', 'millionaire', 'min', 'mind', 'minds', 'minimum', 'minister', 'mins', 'mirabai',
'mishap', 'mismatch', 'miss', 'mistake', 'mitchell', 'mithali', 'mixed', 'mlas', 'mobile', 'mode', 'modern',
'mohali', 'momentum', 'monday', 'money', 'monstrous', 'months', 'more', 'most', 'mother', 'moti', 'mount',
'move', 'moving', 'ms', 'much', 'mumbai', 'must', 'nagar', 'nagpur', 'name', 'named', 'names', 'napier', 'nath',
'nation', 'national', 'natural', 'navigation', 'nd', 'nearly', 'needed', 'nervous', 'netherlands', 'neutral', 'never',
'new', 'news', 'newsletter', 'next', 'nickname', 'nidahas', 'nightmare', 'nine', 'ninth', 'no', 'not', 'nov',
'november', 'now', 'nowadays', 'number', 'numbers', 'october', 'odi', 'odis', 'off', 'offenses', 'old', 'once', 'one',
'ongoing', 'only', 'opc', 'opener', 'openers', 'opinion', 'opponent', 'opposition', 'orange', 'order', 'ordinary',
'other', 'others', 'otherwise', 'out', 'outside', 'outstanding', 'oval', 'over', 'overs', 'overtaking', 'pace', 'pacers',
'padukone', 'page', 'pages', 'paid', 'paine', 'pair', 'pakistan', 'pandit', 'pandya', 'pant', 'paper', 'part',
'partnership', 'passed', 'passion', 'passionate', 'past', 'pat', 'patani', 'peak', 'people', 'per', 'percentage',
'perfect', 'perform', 'performance', 'performances', 'performed', 'performing', 'permanently', 'permitting',
'personal', 'phase', 'phone', 'photographer', 'photos', 'picked', 'picks', 'pics', 'pietersen', 'pinnacle', 'pinterest',
'pitch', 'place', 'planning', 'platforms', 'play', 'played', 'player', 'players', 'playing', 'plays', 'plessis', 'pocket',
'point', 'points', 'pole', 'policy', 'polly', 'ponting', 'poor', 'popular', 'port', 'position', 'possesses', 'possible',
'post', 'posted', 'poster', 'posts', 'poured', 'powers', 'practical', 'pradesh', 'predecessor', 'predicted',
'prediction', 'predictions', 'predicts', 'prem', 'premier', 'preparatory', 'prepared', 'present', 'president',
'pressure', 'preview', 'previews', 'previous', 'previously', 'priceless', 'prime', 'privacy', 'private', 'privilege',
'prize', 'productive', 'profile', 'progressing', 'prominence', 'promoting', 'proud', 'prove', 'proved', 'proves',
'provisions', 'pujara', 'pulled', 'pulls', 'pundit', 'pune', 'punjab', 'punjabi', 'question', 'questioning', 'quick', 'r',
'rabada', 'rahman', 'rahul', 'rai', 'raina', 'raised', 'raj', 'rajasthan', 'rajiv', 'ram', 'ramnath', 'ranchi', 'ranked',
'ranking', 'rankings', 'ranveer', 'rashtrapati', 'rate', 'ratna', 'raveena', 'ravi', 'ravichandran', 'rcb', 'rcbs', 'rd',
'reach', 'reached', 'reaches', 'reaction', 'reactions', 'reacts', 'read', 'reading', 'really', 'reason', 'reasons',
'received', 'receiving', 'recent', 'recently', 'reckons', 'record', 'records', 'reeling', 'referred', 'regime',
'regularly', 'religion', 'remarkable', 'report', 'reporting', 'research', 'reserved', 'respectively', 'rested', 'result',
'results', 'retail', 'retain', 'retained', 'retaining', 'retirement', 'returned', 'returns', 'revelation', 'review',
'reviews', 'riaz', 'ricky', 'riders', 'right', 'rights', 'risen', 'rishabh', 'rival', 'rohit', 'role', 'root', 'roshan', 'rounder',
'routine', 'row', 'royal', 'royals', 'rr', 'rs', 'ruled', 'run', 'runs', 'russell', 's', 'sabha', 'sachin', 'said', 'salary',
'salvo', 'same', 'sara', 'sarah', 'saroj', 'saturday', 'save', 'saw', 'say', 'says', 'scaling', 'scenario', 'schedule',
'schedules', 'score', 'scorecard', 'scored', 'scorer', 'scorers', 'scores', 'scoring', 'scroll', 'sealed', 'search',

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

'searched', 'season', 'second', 'secure', 'secured', 'see', 'seen', 'sehwag', 'select', 'selected', 'selections',
'semifinals', 'sent', 'september', 'series', 'set', 'seven', 'seventh', 'several', 'sexual', 'shane', 'share', 'shared',
'shares', 'sharing', 'sharma', 'shastri', 'sheer', 'shell', 'shere', 'shetty', 'shifted', 'shikhar', 'shilpa', 'ship',
'shocked', 'shoot', 'shooting', 'short', 'shorter', 'shot', 'should', 'show', 'showed', 'shows', 'shrewd', 'shreyas',
'shutterbugs', 'side', 'significant', 'similar', 'similarly', 'simultaneously', 'since', 'singh', 'single', 'sinking',
'sister', 'sitemap', 'situation', 'six', 'sixth', 'skipper', 'sky', 'sl', 'slammed', 'sledged', 'sledging', 'slight',
'slightly', 'slow', 'slowly', 'slumdog', 'slump', 'slumps', 'small', 'smith', 'smriti', 'so', 'social', 'sold', 'someone',
'something', 'son', 'soon', 'sought', 'sourav', 'south', 'spain', 'spam', 'span', 'speaks', 'special', 'specialists',
'specials', 'spell', 'spend', 'spending', 'spin', 'spirits', 'sporting', 'sports', 'spot', 'spotted', 'spotting', 'squad',
'squads', 'sr', 'srh', 'sri', 'srilanka', 'st', 'stadium', 'staff', 'stand', 'stands', 'star', 'stars', 'start', 'started', 'starting',
'statement', 'statistical', 'statistics', 'stats', 'statsindeed', 'stature', 'statutory', 'stay', 'stayed', 'stays',


'stereotypes', 'steve', 'still', 'stoinis', 'stood', 'stop', 'stopping', 'story', 'straight', 'streak', 'street', 'strike',
'striking', 'stumping', 'style', 'subscribe', 'subtle', 'successful', 'such', 'suffer', 'summer', 'sunday', 'sunrisers',
'super', 'superlative', 'superstars', 'support', 'supremacy', 'sure', 'suresh', 'surprise', 'surprised', 'surprises',
'surprising', 'sweep', 'swept', 't', 'table', 'tactful', 'tactician', 'tactics', 'tagged', 'tahir', 'take', 'takes', 'talisman',
'talk', 'talked', 'talks', 'tally', 'tandon', 'target', 'taylor', 'team', 'teams', 'tech', 'technology', 'tell', 'tendulkar',
'tenth', 'terms', 'terrible', 'test', 'tests', 'th', 'than', 'thanks', 'theme', 'themes', 'then', 'thereby', 'these', 'things',
'thiraviam', 'third', 'those', 'though', 'thought', 'thousands', 'three', 'thriller', 'ti', 'tide', 'tied', 'tiktok', 'till', 'tim',
'time', 'times', 'timings', 'tips', 'tis', 'to', 'today', 'together', 'toggle', 'ton', 'tons', 'too', 'took', 'top', 'topping',
'total', 'tour', 'tournament', 'tours', 'towards', 'town', 'training', 'transfered', 'travels', 'travis', 'treat',
'tremendous', 'trending', 'tri', 'trial', 'tried', 'troll', 'trophy', 'trot', 'true', 'trust', 'ts', 'tuesday', 'turn', 'turned',
'turning', 'turns', 'tussauds', 'tweet', 'twice', 'twitter', 'two', 'uae', 'ultimately', 'umpires', 'umrigar', 'un',
'unbeaten', 'unbelievable', 'uncharacteristic', 'under', 'understand', 'undoubtedly', 'unique', 'unlikely',
'unveiled', 'up', 'upcoming', 'update', 'updates', 'updating', 'upping', 'usain', 'use', 'used', 'users', 'usman',
'usual', 'uttar', 'v', 've', 'veda', 'venue', 'version', 'versus', 'very', 'vettori', 'vice', 'victory', 'video', 'videos',
'views', 'vigorously', 'vijay', 'vikas', 'villiers', 'vinod', 'viral', 'virat', 'viratkohli', 'virats', 'virender', 'virendra',
'virushka', 'visakhapatnam', 'visitors', 'vs', 'wahab', 'wait', 'walk', 'want', 'wanting', 'warner', 'wary', 'watch',
'watson', 'wax', 'way', 'we', 'wearing', 'wednesday', 'week', 'weightlifter', 'well', 'went', 'were', 'west',
'whenever', 'whereas', 'while', 'white', 'whitewash', 'whom', 'wicket', 'wickets', 'wife', 'williamson', 'win',
'windies', 'winners', 'winning', 'wisden', 'wise', 'wishes', 'within', 'witness', 'woman', 'women', 'won',
'wonder', 'wonderful', 'wondering', 'wordpress', 'world', 'would', 'wrap', 'wrapping', 'written', 'wrong',
'wwe', 'wyatt', 'xi', 'y', 'yards', 'year', 'years', 'yearwise', 'yesterday', 'yo', 'young', 'youngest', 'yuvraj',
'yuzvendra', 'zealand', 'zimbabwe', 'ziva', 'zoravar']

LOCAL STEM LIST

['aamir', 'ab', 'abil', 'about', 'abov', 'absenc', 'absolut', 'academi', 'accord', 'accumul', 'accur', 'achiev', 'achiev',
'achiev', 'across', 'action', 'activ', 'actress', 'ad', 'add', 'address', 'add', 'adelaid', 'adject', 'admit', 'ador',
'advanc', 'advertis', 'advertis', 'afghanistan', 'africa', 'after', 'again', 'against', 'age', 'aggress', 'ago', 'agre',
'ahead', 'aiadmk', 'aim', 'airport', 'aishwarya', 'ali', 'alia', 'aliv', 'almanack', 'almanack', 'almost', 'along',
'alreadi', 'also', 'alterc', 'although', 'alway', 'am', 'amaz', 'among', 'analysi', 'analyz', 'andr', 'android', 'anil',
'announc', 'announc', 'anoth', 'antigua', 'anuksha', 'anushka', 'anuskha', 'anwar', 'anyon', 'anytim', 'apart',
'apart', 'app', 'appear', 'appear', 'appoint', 'appreci', 'apprentic', 'approach', 'app', 'april', 'ar', 'archer', 'archiv',
'ardent', 'arjuna', 'around', 'articl', 'articl', 'ascend', 'ash', 'ashwin', 'asia', 'asset', 'assuredli', 'athlet', 'atindra',
'attribut', 'auction', 'audio', 'australia', 'australian', 'australia', 'avail', 'avail', 'averag', 'averag', 'averag', 'avg',
'award', 'award', 'award', 'away', 'azam', 'azhar', 'b', 'babar', 'bachchan', 'back', 'bad', 'badli', 'bag', 'balaji',
'ball', 'ball', 'bamboozl', 'bamboozl', 'ban', 'bangalor', 'bangalor', 'bangla', 'bangladesh', 'banglor', 'bank',
'bar', 'barren', 'base', 'bat', 'batsman', 'batsmen', 'bat', 'battl', 'battl', 'bay', 'bcci', 'beat', 'beat', 'beat', 'beauti',
'becam', 'becom', 'becom', 'befor', 'begin', 'begin', 'behind', 'be', 'belief', 'believ', 'below', 'bench',

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

'bengaluru', 'best', 'better', 'bet', 'between', 'bhatt', 'bhavna', 'bhawan', 'bhuvi', 'bhuvneshwar', 'big', 'biggest',
'biopic', 'birthday', 'blame', 'blast', 'blitz', 'blogger', 'blog', 'board', 'boast', 'bollywood', 'bolt', 'bonu',
'border', 'born', 'both', 'bought', 'bow', 'bowl', 'box', 'boy', 'bradman', 'branch', 'break', 'break', 'break',
'breather', 'brendon', 'brian', 'brilliantli', 'broke', 'broken', 'brother', 'brought', 'bump', 'bumrah', 'bunch',
'busi', 'buvanesh', 'buy', 'c', 'calendar', 'calibr', 'call', 'call', 'came', 'campaign', 'can', 'canberra', 'cap', 'cape',
'capit', 'captain', 'captainci', 'captain', 'captain', 'cardiff', 'career', 'casual', 'catch', 'categori', 'categori', 'caus',
'cb', 'cbi', 'celeb', 'celebr', 'celebr', 'celebr', 'celebr', 'celebr', 'centuri', 'centurion', 'centuri', 'ceremoni',
'chahal', 'challeng', 'challengeaccept', 'challeng', 'champion', 'champion', 'championship', 'chanc', 'chanc',
'chang', 'chang', 'chanu', 'chappel', 'chariti', 'chart', 'chase', 'chase', 'check', 'cheeku', 'cheer', 'chennai',
'cheteshwar', 'children', 'chinnaswami', 'chri', 'citi', 'clark', 'clash', 'clean', 'clear', 'clearli', 'click', 'click',
'click', 'clinch', 'clinch', 'close', 'co', 'coach', 'coach', 'colombo', 'column', 'com', 'combin', 'come', 'come',


'come', 'command', 'comment', 'commentari', 'commerci', 'commonli', 'commonwealth', 'compar',
'complet', 'conclud', 'condit', 'confer', 'confirm', 'connect', 'conquer', 'consecut', 'consid', 'consist', 'contact',
'continu', 'continu', 'continu', 'contract', 'contribut', 'contributor', 'control', 'controversi', 'convers', 'convers',
'convert', 'copyright', 'could', 'council', 'count', 'countri', 'countri', 'countri', 'counti', 'coupl', 'coupl', 'crazi',
'creativebrain', 'crew', 'cricadium', 'cricinde', 'cricket', 'cricket', 'cricket', 'cricket', 'crickettim', 'crimin',
'crore', 'cross', 'csk', 'culmin', 'cummin', 'cup', 'current', 'current', 'cwc', 'd', 'danc', 'daniel', 'daniel', 'data',
'date', 'day', 'day', 'dc', 'de', 'debat', 'debut', 'decemb', 'declar', 'deep', 'deepika', 'defeat', 'defeat', 'deject',
'delhi', 'deliveri', 'deliveri', 'deplet', 'depth', 'deserv', 'desper', 'despit', 'destroy', 'destroy', 'detail', 'detail',
'dev', 'dhaka', 'dharamshala', 'dhawan', 'dhoni', 'dhoni', 'did', 'didn', 'didnt', 'die', 'diet', 'differ', 'differ', 'dig',
'diljit', 'dilshan', 'dimuth', 'dinesh', 'disbelief', 'disclaim', 'discont', 'disha', 'dismay', 'dismiss', 'dismiss',
'disqualifi', 'distribut', 'dive', 'do', 'doesn', 'do', 'domest', 'domin', 'domin', 'domin', 'domin', 'don', 'dosanjh',
'doubl', 'doubt', 'down', 'download', 'dream', 'drink', 'drive', 'du', 'due', 'duo', 'durban', 'dure', 'duti', 'each',
'earlier', 'earli', 'easili', 'easi', 'eden', 'educ', 'eighth', 'elder', 'elect', 'electrifi', 'elev', 'eleventh', 'els', 'email',
'emerg', 'emerg', 'emerg', 'emphat', 'end', 'end', 'end', 'enforc', 'engin', 'england', 'english', 'engvind', 'enjoy',
'enough', 'enrol', 'entertain', 'entir', 'epap', 'episod', 'era', 'error', 'escap', 'especi', 'eve', 'even', 'even', 'event',
'eventu', 'ever', 'everi', 'evolv', 'evolv', 'exam', 'excel', 'excel', 'excit', 'exist', 'experi', 'expertis', 'explain',
'express', 'express', 'express', 'extend', 'extend', 'extens', 'eye', 'fab', 'facebook', 'face', 'face', 'fact', 'fact', 'faf',
'fail', 'fail', 'fame', 'famili', 'famou', 'fan', 'fan', 'fantasi', 'far', 'fascin', 'fashion', 'fast', 'faster', 'fastest', 'father',
'fatullah', 'favourit', 'fb', 'featur', 'featur', 'februari', 'field', 'fieri', 'fifth', 'fifti', 'fifti', 'fight', 'figur', 'film',
'final', 'final', 'final', 'financi', 'fine', 'finest', 'finish', 'finish', 'fire', 'first', 'fit', 'fit', 'five', 'fleme', 'flood',
'follow', 'follow', 'follow', 'food', 'fool', 'footbal', 'forb', 'forc', 'form', 'format', 'format', 'former', 'formid',
'forward', 'foundat', 'four', 'fourth', 'fratern', 'free', 'frenzi', 'from', 'fume', 'fun', 'furiou', 'gain', 'gambhir',
'game', 'gandhi', 'ganguli', 'garden', 'gari', 'gautam', 'gavaskar', 'gayl', 'gender', 'gener', 'gener', 'gentleman',
'get', 'get', 'getter', 'get', 'given', 'glenn', 'go', 'god', 'god', 'goenka', 'goe', 'go', 'gone', 'good', 'googl', 'gopal',
'got', 'govt', 'gozzi', 'grade', 'great', 'greater', 'great', 'green', 'greg', 'group', 'grown', 'guest', 'gulli', 'gulmarg',
'gulzar', 'guptil', 'guwahati', 'guy', 'had', 'half', 'halt', 'hambantota', 'hand', 'hand', 'hander', 'hang', 'hanker',
'harar', 'hardcor', 'hardik', 'harmanpreet', 'harri', 'ha', 'hate', 'have', 'head', 'headlin', 'heart', 'heat', 'help',
'help', 'help', 'henc', 'high', 'highest', 'highlight', 'highlight', 'highli', 'hilari', 'himself', 'hint', 'histor', 'histori',
'hit', 'hit', 'hobart', 'holder', 'hold', 'home', 'honor', 'honour', 'hope', 'hope', 'horoscop', 'horror', 'host',
'housewif', 'howev', 'hrithik', 'hudson', 'hug', 'huge', 'huge', 'hundr', 'hundr', 'hyderabad', 'ian', 'icc', 'idea',
'if', 'import', 'import', 'improv', 'imran', 'incid', 'incident', 'includ', 'includ', 'ind', 'india', 'indian', 'indian',
'india', 'indi', 'individu', 'indor', 'indvsl', 'infograph', 'inform', 'injur', 'injuri', 'inning', 'inning', 'innov', 'inr',
'inspir', 'inspir', 'instagram', 'instinct', 'integr', 'intellig', 'interest', 'interestingli', 'intermedi', 'intern', 'intern',
'internet', 'into', 'introduc', 'invad', 'invad', 'involv', 'ipad', 'iphon', 'ipl', 'ireland', 'ishant', 'itali', 'iyer', 'jac',
'jaden', 'jain', 'jaipur', 'jansatta', 'januari', 'jasprit', 'jawaharl', 'jayasuriya', 'jersey', 'jersey', 'job', 'joe', 'jofra',
'johnson', 'join', 'joint', 'journey', 'juli', 'june', 'junior', 'just', 'k', 'kagiso', 'kajol', 'kalank', 'kambl', 'kane',
'kanpur', 'kapil', 'kapoor', 'karisma', 'karthik', 'karunaratn', 'kaur', 'keen', 'keep', 'keep', 'kept', 'kerala',
'kevin', 'key', 'khan', 'khawaja', 'khel', 'kid', 'king', 'kingston', 'kirsten', 'kiss', 'kkr', 'knight', 'knockout',

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

'know', 'known', 'kohli', 'kohli', 'kolkata', 'kovind', 'krishnamurthi', 'kuala', 'kumar', 'kumbl', 'kxip', 'lakh',
'landmark', 'languag', 'lanka', 'lankan', 'lankan', 'lara', 'lasith', 'last', 'late', 'late', 'later', 'latest', 'laud', 'launch',
'lawyer', 'lead', 'leader', 'lead', 'lead', 'leagu', 'lean', 'learn', 'learn', 'least', 'leav', 'leav', 'led', 'left', 'leg',
'legend', 'legendari', 'less', 'let', 'level', 'life', 'lifestyl', 'like', 'like', 'limit', 'limit', 'line', 'link', 'lionel', 'list',
'liter', 'littl', 'live', 'll', 'load', 'lok', 'loksatta', 'long', 'longer', 'longest', 'look', 'look', 'look', 'lose', 'lose', 'loss',
'lost', 'lot', 'lotteri', 'love', 'ltd', 'luckili', 'lucknow', 'lucki', 'lumpur', 'm', 'mace', 'madam', 'made', 'mail',
'mailchimp', 'main', 'maintain', 'maintain', 'maintain', 'major', 'major', 'make', 'make', 'make', 'malaysia',
'malinga', 'man', 'manag', 'manag', 'mandhana', 'march', 'marcu', 'mark', 'marriag', 'marri', 'massiv', 'master',
'master', 'match', 'match', 'maunganui', 'maxwel', 'maxwel', 'may', 'mccullum', 'mcg', 'mean', 'meant',
'media', 'meekli', 'meet', 'melbourn', 'member', 'meme', 'memori', 'men', 'menu', 'merchant', 'messag',
'messi', 'met', 'metoo', 'mh', 'mi', 'michael', 'might', 'mileston', 'million', 'millionair', 'min', 'mind', 'mind',


'minimum', 'minist', 'min', 'mirabai', 'mishap', 'mismatch', 'miss', 'mistak', 'mitchel', 'mithali', 'mix', 'mla',
'mobil', 'mode', 'modern', 'mohali', 'momentum', 'monday', 'money', 'monstrou', 'month', 'more', 'most',
'mother', 'moti', 'mount', 'move', 'move', 'ms', 'much', 'mumbai', 'must', 'nagar', 'nagpur', 'name', 'name',
'name', 'napier', 'nath', 'nation', 'nation', 'natur', 'navig', 'nd', 'nearli', 'need', 'nervou', 'netherland', 'neutral',
'never', 'new', 'news', 'newslett', 'next', 'nicknam', 'nidaha', 'nightmar', 'nine', 'ninth', 'no', 'not', 'nov',
'novemb', 'now', 'nowaday', 'number', 'number', 'octob', 'odi', 'odi', 'off', 'offens', 'old', 'onc', 'one', 'ongo',
'onli', 'opc', 'open', 'open', 'opinion', 'oppon', 'opposit', 'orang', 'order', 'ordinari', 'other', 'other', 'otherwis',
'out', 'outsid', 'outstand', 'oval', 'over', 'over', 'overtak', 'pace', 'pacer', 'padukon', 'page', 'page', 'paid', 'pain',
'pair', 'pakistan', 'pandit', 'pandya', 'pant', 'paper', 'part', 'partnership', 'pass', 'passion', 'passion', 'past', 'pat',
'patani', 'peak', 'peopl', 'per', 'percentag', 'perfect', 'perform', 'perform', 'perform', 'perform', 'perform',
'perman', 'permit', 'person', 'phase', 'phone', 'photograph', 'photo', 'pick', 'pick', 'pic', 'pietersen', 'pinnacl',
'pinterest', 'pitch', 'place', 'plan', 'platform', 'play', 'play', 'player', 'player', 'play', 'play', 'plessi', 'pocket',
'point', 'point', 'pole', 'polici', 'polli', 'pont', 'poor', 'popular', 'port', 'posit', 'possess', 'possibl', 'post', 'post',
'poster', 'post', 'pour', 'power', 'practic', 'pradesh', 'predecessor', 'predict', 'predict', 'predict', 'predict', 'prem',
'premier', 'preparatori', 'prepar', 'present', 'presid', 'pressur', 'preview', 'preview', 'previou', 'previous',
'priceless', 'prime', 'privaci', 'privat', 'privileg', 'prize', 'product', 'profil', 'progress', 'promin', 'promot',
'proud', 'prove', 'prove', 'prove', 'provis', 'pujara', 'pull', 'pull', 'pundit', 'pune', 'punjab', 'punjabi', 'question',
'question', 'quick', 'r', 'rabada', 'rahman', 'rahul', 'rai', 'raina', 'rais', 'raj', 'rajasthan', 'rajiv', 'ram', 'ramnath',
'ranchi', 'rank', 'rank', 'rank', 'ranveer', 'rashtrapati', 'rate', 'ratna', 'raveena', 'ravi', 'ravichandran', 'rcb', 'rcb',
'rd', 'reach', 'reach', 'reach', 'reaction', 'reaction', 'react', 'read', 'read', 'realli', 'reason', 'reason', 'receiv',
'receiv', 'recent', 'recent', 'reckon', 'record', 'record', 'reel', 'refer', 'regim', 'regularli', 'religion', 'remark',
'report', 'report', 'research', 'reserv', 'respect', 'rest', 'result', 'result', 'retail', 'retain', 'retain', 'retain', 'retir',
'return', 'return', 'revel', 'review', 'review', 'riaz', 'ricki', 'rider', 'right', 'right', 'risen', 'rishabh', 'rival', 'rohit',
'role', 'root', 'roshan', 'rounder', 'routin', 'row', 'royal', 'royal', 'rr', 'rs', 'rule', 'run', 'run', 'russel', 's', 'sabha',
'sachin', 'said', 'salari', 'salvo', 'same', 'sara', 'sarah', 'saroj', 'saturday', 'save', 'saw', 'say', 'say', 'scale',
'scenario', 'schedul', 'schedul', 'score', 'scorecard', 'score', 'scorer', 'scorer', 'score', 'score', 'scroll', 'seal',
'search', 'search', 'season', 'second', 'secur', 'secur', 'see', 'seen', 'sehwag', 'select', 'select', 'select', 'semifin',
'sent', 'septemb', 'seri', 'set', 'seven', 'seventh', 'sever', 'sexual', 'shane', 'share', 'share', 'share', 'share',
'sharma', 'shastri', 'sheer', 'shell', 'shere', 'shetti', 'shift', 'shikhar', 'shilpa', 'ship', 'shock', 'shoot', 'shoot',
'short', 'shorter', 'shot', 'should', 'show', 'show', 'show', 'shrewd', 'shreya', 'shutterbug', 'side', 'signific',
'similar', 'similarli', 'simultan', 'sinc', 'singh', 'singl', 'sink', 'sister', 'sitemap', 'situat', 'six', 'sixth', 'skipper',
'sky', 'sl', 'slam', 'sledg', 'sledg', 'slight', 'slightli', 'slow', 'slowli', 'slumdog', 'slump', 'slump', 'small', 'smith',
'smriti', 'so', 'social', 'sold', 'someon', 'someth', 'son', 'soon', 'sought', 'sourav', 'south', 'spain', 'spam', 'span',
'speak', 'special', 'specialist', 'special', 'spell', 'spend', 'spend', 'spin', 'spirit', 'sport', 'sport', 'spot', 'spot',
'spot', 'squad', 'squad', 'sr', 'srh', 'sri', 'srilanka', 'st', 'stadium', 'staff', 'stand', 'stand', 'star', 'star', 'start', 'start',
'start', 'statement', 'statist', 'statist', 'stat', 'statsinde', 'statur', 'statutori', 'stay', 'stay', 'stay', 'stereotyp', 'steve',
'still', 'stoini', 'stood', 'stop', 'stop', 'stori', 'straight', 'streak', 'street', 'strike', 'strike', 'stump', 'style', 'subscrib',
'subtl', 'success', 'such', 'suffer', 'summer', 'sunday', 'sunris', 'super', 'superl', 'superstar', 'support',

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

'supremaci', 'sure', 'suresh', 'surpris', 'surpris', 'surpris', 'surpris', 'sweep', 'swept', 't', 'tabl', 'tact', 'tactician',
'tactic', 'tag', 'tahir', 'take', 'take', 'talisman', 'talk', 'talk', 'talk', 'talli', 'tandon', 'target', 'taylor', 'team', 'team',
'tech', 'technolog', 'tell', 'tendulkar', 'tenth', 'term', 'terribl', 'test', 'test', 'th', 'than', 'thank', 'theme', 'theme',
'then', 'therebi', 'these', 'thing', 'thiraviam', 'third', 'those', 'though', 'thought', 'thousand', 'three', 'thriller', 'ti',
'tide', 'tie', 'tiktok', 'till', 'tim', 'time', 'time', 'time', 'tip', 'ti', 'to', 'today', 'togeth', 'toggl', 'ton', 'ton', 'too',
'took', 'top', 'top', 'total', 'tour', 'tournament', 'tour', 'toward', 'town', 'train', 'transfer', 'travel', 'travi', 'treat',
'tremend', 'trend', 'tri', 'trial', 'tri', 'troll', 'trophi', 'trot', 'true', 'trust', 'ts', 'tuesday', 'turn', 'turn', 'turn', 'turn',
'tussaud', 'tweet', 'twice', 'twitter', 'two', 'uae', 'ultim', 'umpir', 'umrigar', 'un', 'unbeaten', 'unbeliev',
'uncharacterist', 'under', 'understand', 'undoubtedli', 'uniqu', 'unlik', 'unveil', 'up', 'upcom', 'updat', 'updat',
'updat', 'up', 'usain', 'use', 'use', 'user', 'usman', 'usual', 'uttar', 'v', 've', 'veda', 'venu', 'version', 'versu', 'veri',
'vettori', 'vice', 'victori', 'video', 'video', 'view', 'vigor', 'vijay', 'vika', 'villier', 'vinod', 'viral', 'virat',


'viratkohli', 'virat', 'virend', 'virendra', 'virushka', 'visakhapatnam', 'visitor', 'vs', 'wahab', 'wait', 'walk',
'want', 'want', 'warner', 'wari', 'watch', 'watson', 'wax', 'way', 'we', 'wear', 'wednesday', 'week', 'weightlift',
'well', 'went', 'were', 'west', 'whenev', 'wherea', 'while', 'white', 'whitewash', 'whom', 'wicket', 'wicket',
'wife', 'williamson', 'win', 'windi', 'winner', 'win', 'wisden', 'wise', 'wish', 'within', 'wit', 'woman', 'women',
'won', 'wonder', 'wonder', 'wonder', 'wordpress', 'world', 'would', 'wrap', 'wrap', 'written', 'wrong', 'wwe',
'wyatt', 'xi', 'y', 'yard', 'year', 'year', 'yearwis', 'yesterday', 'yo', 'young', 'youngest', 'yuvraj', 'yuzvendra',
'zealand', 'zimbabw', 'ziva', 'zoravar']

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### ASSOCIATION CLUSTER VALUE


Association Clusters were made through creating frequency matrix of terms and local document stems.
The above result shows the normalized scores in a sorted linked list hashmap according to which the
expanded terms were selected.

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

EXPANDED QUERY: Virat Kohli IPL Cricket Series

METRIC CLUSTER VALUES


For metric clusters, I used positions and difference in the positions between document terms to get the
neighbours , this was followed by creating the corresponding matrices and doing the calculations required
by metric cluster. The results were stored in the sorted linked list hashmap.

EXPANDED QUERY: Virat Kohli Runs ODI India Series

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### SCALAR CLUSTER VALUES


For scalar clusters, passages of text from local docs were taken and cosine similarity between different
sets of queries and passages were taken out, finally the closest matched docs top terms were taken into
account.

EXPANDED QUERY: Virat Kohli India Match ODI Indian

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### QUERY 2:WEST INDIES

###### LOCAL DOCUMENT SET


###### • L OCAL DOCUMENT 1

Toggle navigation\nHome\nCategories\nAll Categories\nTest Matches\nLimited Overs (ODI & T20)\
nControversies & Issues\nMatch Fixing\nFunny Clips\nSledging\nAll Time Best Videos\nClassic Tests
(Black & White)\nCricketCrowd Specials\nExpert Analysis & Views\nFielding\nInterviews\nOther (incl.
IPL)\nWorld Cups\nBatting\nAll Rounders\nBowling\nClipdex\nClipdex Overview\nAll Matches\n ODI
Matches\n Test Matches\n T20 Matches\nMajor Events\nAll Players\nAll Teams\nAll Grounds\nLatest
News\nAround The World\nCricketCrowd Articles\nMy Play List\nSearch\nx\nSearch Cricket Crowd\
nVideo\nArticle\nSearch\nOr check out our Top Searches\n- Choose One -\nAlastair Cook\nEspn\nab de
villiers\nguru greg\nganguly\nTim southee\nIcc world cup t20 2012\nAjantha Mendis\nindia 2009\
nKagiso Rabada\nClose\nLogin\nx\nLogin to Cricket Crowd\nUser name / email:\nPassword:\
nRemember me\nLogin\nClose | Forgot password | New To Cricket Crowd? Sign up for free\nHome\
nClipdex\nAll Teams\nClipdex page for \"West Indies\"\nVideos\nTeam Players\nAll\nA\nB\nC\nD\nE\
nF\nG\nH\nI\nJ\nK\nL\nM\nN\nO\nP\nQ\nR\nS\nT\nU\nV\nW\nX\nY\nZ\n \n‹\n1\n2\n3\n4\n5\n6\n7\
n8\n9\n›\n»\nVideos for West Indies Latest Series for West Indies\nPlayer Name Team Career Videos\
nBravo, Dwayne J J\nWest Indies\nTest : 2004 / -\n165\nBreese, Gareth R\nWest Indies\nTest : 2002 /
2002\n0\nBrown, Darryl\nWest Indies\nODI : 2001 / 2002\n0\nBrowne, Barrington S\nWest Indies\nODI
: 1994 / 1994\n0\nBrowne, Courtney O\nWest Indies\nODI : 1995 / 2005\n8\nTest : 1995 / 2005\
nBrowne, Cyril R\nWest Indies\nTest : 1928 / 1930\n0\nBrowne, Patrick A\nWest Indies\nODI : 2008 / -\
n0\nBryan, Henderson R\nWest Indies\nODI : 1999 / 1999\n1\nButcher, Basil F\nWest Indies\nTest :
1958 / 1969\n7\nButler, Deighton C\nWest Indies\nODI : 2005 / 2006\n0\nButler, Lennox S\nWest
Indies\nTest : 1955 / 1955\n0\nButts, Clyde G\nWest Indies\nFirst Class : 1980 / 1993\n0\nTest : 1985 /
1988\nBynoe, Michael R\nWest Indies\nTest : 1959 / 1967\n0\nCamacho, George S\nWest Indies\nTest :
1968 / 1971\n2\nCameron, Francis J\nWest Indies\nTest : 1948 / 1949\n0\nCameron, John H\nWest
Indies\nTest : 1939 / 1939\n0\nCampbell John D\nWest Indies\nFirst Class : 2014 / -\n7\nODI : 2019 / -\
nTest : 2019 / -\nCampbell, Sherwin L\nWest Indies\nODI : 1994 / 2001\n8\nTest : 1995 / 2002\nCarew,
George M\nWest Indies\nTest : 1935 / 1948\n0\nCarew, Michael C\nWest Indies\nTest : 1963 / 1972\n2\
nCarter, Jonathan L\nWest Indies\nNot defined\n2\nChallenor, George\nWest Indies\nTest : 1928 / 1928\
n0\nChanderpaul, Shivnarine\nWest Indies\nODI : 1994 / 2011\n116\nT20 : 2006 / 2010\nTest : 1994 /
2015\nChandrika, Rajendra\nWest Indies\nNot defined\n2\nChang, Herbert S\nWest Indies\nTest : 1979 /
1979\n0\nCharles, Johnson\nWest Indies\nFirst Class : 2009 / -\n39\nODI : 2012 / -\nOther : 2011 / -\
nChase, Roston L\nWest Indies\nFirst Class : 2011 / -\n41\nTest : 2016 / -\nChattergoon, Sewnarine\
nWest Indies\nODI : 2006 / -\n4\nTest : 2008 / -\nChristian, Derwin O\nWest Indies\nNot defined\n0\
nVideos for West Indies Latest Series for West Indies\nAll\nA\nB\nC\nD\nE\nF\nG\nH\nI\nJ\nK\nL\nM\
nN\nO\nP\nQ\nR\nS\nT\nU\nV\nW\nX\nY\nZ\n \n‹\n1\n2\n3\n4\n5\n6\n7\n8\n9\n›\n»\nShare with\
nWhat are these?\nCopy URL\nEmbed this page\nFeedback\nNewcomers\nCampbell John D\nWest
Indies\nEmbuldeniya, Lasith\nSri Lanka\nFernando B Oshada P\nSri Lanka\nBirthdays Today\nTrott,
Jonathan\n22-04-1981\nEngland\nAlexander, George\n22-04-1851\nAustralia\nAshraf Ali\n22-04-1958\
nPakistan\nLatest Series\n2019 PAK v AUS ODI series\n2019 RSA v SRL T20 series\n2019 WIN v
ENG T20 series\n2019 RSA v SRL ODI series\n2019 IND v AUS ODI series\nLatest News\nfrom
around the world\nChennai loses out on hosting IPL final\nThe title clash will be played in Hyderabad
with the long-standing issue of the ...\nLast-ball run-out as Lancs beat Yorkshire\nLancashire beat
Yorkshire by one run after the last man is run out off the ...\nConcerns for Super Kings as they look to
bounce back after successive losses\nSunrisers Hyderabad have won their last two games, one of them
against Super Kin ...\nmore...\nCricketCrowd articles\nPAGE OF SHAME series : A timeline of Match
and Spot Fixing activities\nWhat are the sequence of events in the colourful and recent history of...\

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

nFastest ever centuries in Tests, ODIs and T20's\nChris Gayle hit the fastest century in World Twenty20
history as West Indies...\nTriple century club: Highlights of legendary Test knocks\nEver since England
opener Andy Sandham stroked his way to 325 (640 balls, 28...\nmore...\nOn This Day (22nd Apr)\


nShahid Afridi wrecks Aussies with career best: 1st ODI (Dubai) - Part 2 of 2\nFirst ever test: Hookes &
Wessels crush Sri Lanka (1983)\nSimmons and Bishoo down Pakistan: Only T20 (St Lucia)\nRandom
Video (I'm Feeling Lucky)\nNB: Click for new random video only works when Javascript is turned on\
nTop 5 moments from the epic 1st Ashes Test (Trent Bridge)\nCopyright 2007 - 2019 Cricket Crowd Ltd\
nLatest videos\nLatest articles\nDownloads | News |\nLinks | About Us | Help | Contact Us | Sitemap\
nprivacy policy | disclaimer |\nVersion: 03.02.093 23/05/2017\n"},

LOCAL VOCAB LIST

['ab', 'about', 'achong', 'activities', 'adams', 'adrian', 'africa', 'afridi', 'after', 'against', 'ajantha', 'alastair',
'alexander', 'ali', 'allan', 'allen', 'allround', 'ambrose', 'analysis', 'anchors', 'anderson', 'andre', 'andrew',
'andy', 'anthony', 'antigua', 'anton', 'apr', 'around', 'arthur', 'arthurton', 'article', 'articles', 'asgarli', 'ashes',
'ashraf', 'ashton', 'assad', 'atkinson', 'aus', 'aussies', 'austin', 'australia', 'australian', 'australians', 'azam', 'b',
'babar', 'bacchus', 'back', 'badree', 'baichan', 'baker', 'ball', 'balls', 'bangladesh', 'banks', 'baptiste', 'barath',
'barnwell', 'barrett', 'barrie', 'barrington', 'barrow', 'bartlett', 'bascombe', 'basil', 'batsman', 'batting', 'bbc',
'beat', 'becomes', 'believe', 'berkeley', 'best', 'bid', 'birthday', 'birthdays', 'bishoo', 'black', 'bounce', 'bowl',
'bowling', 'boycott', 'brace', 'bravo', 'breese', 'bresnan', 'bridge', 'brown', 'browne', 'bryan', 'butcher', 'butler',
'butts', 'bynoe', 'c', 'caires', 'camacho', 'cameron', 'campbell', 'capitals', 'career', 'carew', 'carlos', 'carter',
'categories', 'centuries', 'centurion', 'century', 'challenor', 'chanderpaul', 'chandrika', 'chang', 'charge',
'charles', 'chase', 'chattergoon', 'check', 'chennai', 'choke', 'choose', 'chowdhury', 'chris', 'christian',
'christiani', 'christopher', 'clairmonte', 'clarke', 'clash', 'class', 'classic', 'claude', 'click', 'clifford', 'clipdex',
'clips', 'close', 'club', 'clyde', 'colin', 'collins', 'collymore', 'colourful', 'combine', 'commentary', 'concerns',
'confrontation', 'constantine', 'contact', 'continues', 'controversies', 'cook', 'copy', 'copyright', 'corey', 'costa',
'cottrell', 'county', 'courtney', 'crandon', 'cricket', 'cricketcrowd', 'croft', 'crowd', 'crush', 'cuffy', 'cummins',
'cup', 'cups', 'curtly', 'cyril', 'd', 'da', 'daniel', 'darcy', 'darren', 'darryl', 'dave', 'david', 'davis', 'day', 'de',
'defined', 'deighton', 'delhi', 'dennis', 'deonarine', 'depeiza', 'derwin', 'deryck', 'dewdney', 'dhanraj', 'dillon',
'dimuth', 'dion', 'disclaimer', 'do', 'dowe', 'dowlin', 'down', 'downloads', 'dowrich', 'drakes', 'drummond',
'dubai', 'duck', 'dujon', 'dunedin', 'dwayne', 'e', 'easton', 'edward', 'edwards', 'eldine', 'ellis', 'email', 'embed',
'embuldeniya', 'emmanuel', 'emrit', 'eng', 'england', 'epic', 'eric', 'espn', 'events', 'ever', 'everton', 'expert',
'extra', 'eye', 'ezra', 'f', 'fabian', 'faith', 'falls', 'fastest', 'feedback', 'feeling', 'ferguson', 'fernandes', 'fernando',
'fidel', 'fielding', 'fifth', 'fight', 'final', 'find', 'findlay', 'fir', 'first', 'fixing', 'fixtures', 'fletcher', 'forgot', 'form',
'foster', 'francis', 'frank', 'franz', 'frederick', 'fredericks', 'free', 'from', 'fudadin', 'fuller', 'funny', 'furlonge',
'g', 'gabriel', 'galore', 'game', 'games', 'ganga', 'ganguly', 'ganteaume', 'gareth', 'garey', 'garner', 'garrick',
'gaskin', 'gayle', 'gears', 'george', 'gets', 'gl', 'goddard', 'golden', 'gordon', 'greg', 'grounds', 'guptill', 'guru',
'h', 'hafeez', 'hamish', 'hammond', 'happy', 'haris', 'has', 'help', 'henderson', 'herbert', 'highlights', 'history',
'hit', 'hits', 'hitting', 'home', 'hookes', 'hosting', 'hyderabad', 'ian', 'icc', 'im', 'imtiaz', 'incl', 'including', 'ind',
'india', 'indies', 'innings', 'inshan', 'inspire', 'interviews', 'ipl', 'issue', 'issues', 'ivan', 'ivor', 'j', 'james', 'jason',
'javascript', 'jeffrey', 'joel', 'john', 'johnson', 'jonathan', 'k', 'kagiso', 'karachi', 'karunaratne', 'keep', 'keith',
'kin', 'kings', 'kirk', 'knight', 'knocks', 'kuldeep', 'l', 'labuschagne', 'lancashire', 'lancs', 'lanka', 'lasith', 'last',
'late', 'latest', 'learie', 'leave', 'legendary', 'lennox', 'leon', 'leonard', 'limited', 'links', 'lionel', 'list', 'listen',
'login', 'long', 'look', 'loses', 'losses', 'love', 'ltd', 'lucia', 'lucky', 'luke', 'm', 'made', 'madray', 'mahela', 'major',
'malcolm', 'man', 'manhandled', 'marius', 'marshall', 'martin', 'martindale', 'match', 'matches', 'mathurin',
'mattis', 'maurice', 'maxwell', 'mccoy', 'mcgarrell', 'mclean', 'mcmorris', 'mcwatt', 'meet', 'mendis',
'mendonca', 'merry', 'merwyn', 'michael', 'middle', 'miguel', 'miles', 'miller', 'mohammed', 'moments',
'more', 'morton', 'moseley', 'mudie', 'murray', 'n', 'nadif', 'name', 'narsingh', 'navigation', 'nb', 'nd', 'need',
'needed', 'needing', 'neil', 'new', 'newcomers', 'news', 'nicol', 'nikita', 'nixon', 'no', 'norman', 'not', 'nyron', 'o',

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

'obed', 'odi', 'odis', 'off', 'old', 'omari', 'one', 'only', 'opener', 'opt', 'order', 'oscar', 'oshada', 'other', 'out', 'over',
'overs', 'overseas', 'overview', 'p', 'page', 'pak', 'pakistan', 'part', 'password', 'patrick', 'pattinson', 'pedro',
'performing', 'play', 'played', 'player', 'players', 'policy', 'power', 'privacy', 'proteas', 'putting', 'q', 'r', 'rabada',


'radio', 'rajasthan', 'rajendra', 'rajindra', 'random', 'rayad', 'rd', 'recent', 'record', 'records', 'remember',
'richard', 'riders', 'robert', 'ronchi', 'roston', 'round', 'rounders', 'roy', 'royals', 'royston', 'rsa', 'run', 'runako',
'runs', 'ryan', 's', 'sachin', 'same', 'samuel', 'sandham', 'scotland', 'seals', 'search', 'searches', 'season',
'seeking', 'sequence', 'series', 'seven', 'sewnarine', 'shahid', 'shame', 'shane', 'shannon', 'share', 'shawn',
'sheik', 'sheldon', 'shepherd', 'sherwin', 'shivnarine', 'show', 'sides', 'sign', 'simmons', 'since', 'sitemap',
'sledging', 'smith', 'sohail', 'solomon', 'south', 'southee', 'specials', 'spin', 'spot', 'sri', 'srinivasaghavan', 'srl',
'st', 'stand', 'standing', 'stroked', 'successive', 'suffers', 'sunrisers', 'super', 'sylvester', 't', 'team', 'teams',
'tendulkar', 'test', 'tests', 'th', 'thaddeus', 'these', 'thilan', 'third', 'thomas', 'three', 'thrilling', 'tim', 'time',
'timeline', 'times', 'title', 'to', 'today', 'toggle', 'ton', 'top', 'trafford', 'trent', 'triple', 'trott', 'ts', 'turned', 'turner',
'twenty', 'two', 'u', 'unchanged', 'up', 'url', 'user', 'uton', 'v', 'vasbert', 'venkataraghavan', 'version', 'video',
'videos', 'views', 'villiers', 'vs', 'w', 'waugh', 'way', 'wayne', 'wessels', 'west', 'while', 'white', 'wicket',
'wilfred', 'william', 'win', 'windies', 'winston', 'won', 'works', 'world', 'wrecks', 'x', 'xavier', 'xi', 'y', 'yorks',
'yorkshire', 'younis', 'z', 'zealand']

LOCAL STEM LIST
['ab', 'about', 'achong', 'activ', 'adam', 'adrian', 'africa', 'afridi', 'after', 'against', 'ajantha', 'alastair', 'alexand',
'ali', 'allan', 'allen', 'allround', 'ambros', 'analysi', 'anchor', 'anderson', 'andr', 'andrew', 'andi', 'anthoni',
'antigua', 'anton', 'apr', 'around', 'arthur', 'arthurton', 'articl', 'articl', 'asgarli', 'ash', 'ashraf', 'ashton', 'assad',
'atkinson', 'au', 'aussi', 'austin', 'australia', 'australian', 'australian', 'azam', 'b', 'babar', 'bacchu', 'back',
'badre', 'baichan', 'baker', 'ball', 'ball', 'bangladesh', 'bank', 'baptist', 'barath', 'barnwel', 'barrett', 'barri',
'barrington', 'barrow', 'bartlett', 'bascomb', 'basil', 'batsman', 'bat', 'bbc', 'beat', 'becom', 'believ', 'berkeley',
'best', 'bid', 'birthday', 'birthday', 'bishoo', 'black', 'bounc', 'bowl', 'bowl', 'boycott', 'brace', 'bravo', 'brees',
'bresnan', 'bridg', 'brown', 'brown', 'bryan', 'butcher', 'butler', 'butt', 'byno', 'c', 'cair', 'camacho', 'cameron',
'campbel', 'capit', 'career', 'carew', 'carlo', 'carter', 'categori', 'centuri', 'centurion', 'centuri', 'challenor',
'chanderpaul', 'chandrika', 'chang', 'charg', 'charl', 'chase', 'chattergoon', 'check', 'chennai', 'choke', 'choos',
'chowdhuri', 'chri', 'christian', 'christiani', 'christoph', 'clairmont', 'clark', 'clash', 'class', 'classic', 'claud',
'click', 'clifford', 'clipdex', 'clip', 'close', 'club', 'clyde', 'colin', 'collin', 'collymor', 'colour', 'combin',
'commentari', 'concern', 'confront', 'constantin', 'contact', 'continu', 'controversi', 'cook', 'copi', 'copyright',
'corey', 'costa', 'cottrel', 'counti', 'courtney', 'crandon', 'cricket', 'cricketcrowd', 'croft', 'crowd', 'crush', 'cuffi',
'cummin', 'cup', 'cup', 'curtli', 'cyril', 'd', 'da', 'daniel', 'darci', 'darren', 'darryl', 'dave', 'david', 'davi', 'day',
'de', 'defin', 'deighton', 'delhi', 'denni', 'deonarin', 'depeiza', 'derwin', 'deryck', 'dewdney', 'dhanraj', 'dillon',
'dimuth', 'dion', 'disclaim', 'do', 'dow', 'dowlin', 'down', 'download', 'dowrich', 'drake', 'drummond', 'dubai',
'duck', 'dujon', 'dunedin', 'dwayn', 'e', 'easton', 'edward', 'edward', 'eldin', 'elli', 'email', 'emb', 'embuldeniya',
'emmanuel', 'emrit', 'eng', 'england', 'epic', 'eric', 'espn', 'event', 'ever', 'everton', 'expert', 'extra', 'eye', 'ezra',
'f', 'fabian', 'faith', 'fall', 'fastest', 'feedback', 'feel', 'ferguson', 'fernand', 'fernando', 'fidel', 'field', 'fifth',
'fight', 'final', 'find', 'findlay', 'fir', 'first', 'fix', 'fixtur', 'fletcher', 'forgot', 'form', 'foster', 'franci', 'frank',
'franz', 'frederick', 'frederick', 'free', 'from', 'fudadin', 'fuller', 'funni', 'furlong', 'g', 'gabriel', 'galor', 'game',
'game', 'ganga', 'ganguli', 'ganteaum', 'gareth', 'garey', 'garner', 'garrick', 'gaskin', 'gayl', 'gear', 'georg', 'get',
'gl', 'goddard', 'golden', 'gordon', 'greg', 'ground', 'guptil', 'guru', 'h', 'hafeez', 'hamish', 'hammond', 'happi',
'hari', 'ha', 'help', 'henderson', 'herbert', 'highlight', 'histori', 'hit', 'hit', 'hit', 'home', 'hook', 'host', 'hyderabad',
'ian', 'icc', 'im', 'imtiaz', 'incl', 'includ', 'ind', 'india', 'indi', 'inning', 'inshan', 'inspir', 'interview', 'ipl', 'issu',
'issu', 'ivan', 'ivor', 'j', 'jame', 'jason', 'javascript', 'jeffrey', 'joel', 'john', 'johnson', 'jonathan', 'k', 'kagiso',
'karachi', 'karunaratn', 'keep', 'keith', 'kin', 'king', 'kirk', 'knight', 'knock', 'kuldeep', 'l', 'labuschagn',
'lancashir', 'lanc', 'lanka', 'lasith', 'last', 'late', 'latest', 'leari', 'leav', 'legendari', 'lennox', 'leon', 'leonard',
'limit', 'link', 'lionel', 'list', 'listen', 'login', 'long', 'look', 'lose', 'loss', 'love', 'ltd', 'lucia', 'lucki', 'luke', 'm',
'made', 'madray', 'mahela', 'major', 'malcolm', 'man', 'manhandl', 'mariu', 'marshal', 'martin', 'martindal',

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

'match', 'match', 'mathurin', 'matti', 'mauric', 'maxwel', 'mccoy', 'mcgarrel', 'mclean', 'mcmorri', 'mcwatt',
'meet', 'mendi', 'mendonca', 'merri', 'merwyn', 'michael', 'middl', 'miguel', 'mile', 'miller', 'moham',
'moment', 'more', 'morton', 'moseley', 'mudi', 'murray', 'n', 'nadif', 'name', 'narsingh', 'navig', 'nb', 'nd',


'need', 'need', 'need', 'neil', 'new', 'newcom', 'news', 'nicol', 'nikita', 'nixon', 'no', 'norman', 'not', 'nyron', 'o',
'obe', 'odi', 'odi', 'off', 'old', 'omari', 'one', 'onli', 'open', 'opt', 'order', 'oscar', 'oshada', 'other', 'out', 'over',
'over', 'oversea', 'overview', 'p', 'page', 'pak', 'pakistan', 'part', 'password', 'patrick', 'pattinson', 'pedro',
'perform', 'play', 'play', 'player', 'player', 'polici', 'power', 'privaci', 'protea', 'put', 'q', 'r', 'rabada', 'radio',
'rajasthan', 'rajendra', 'rajindra', 'random', 'rayad', 'rd', 'recent', 'record', 'record', 'rememb', 'richard', 'rider',
'robert', 'ronchi', 'roston', 'round', 'rounder', 'roy', 'royal', 'royston', 'rsa', 'run', 'runako', 'run', 'ryan', 's',
'sachin', 'same', 'samuel', 'sandham', 'scotland', 'seal', 'search', 'search', 'season', 'seek', 'sequenc', 'seri',
'seven', 'sewnarin', 'shahid', 'shame', 'shane', 'shannon', 'share', 'shawn', 'sheik', 'sheldon', 'shepherd',
'sherwin', 'shivnarin', 'show', 'side', 'sign', 'simmon', 'sinc', 'sitemap', 'sledg', 'smith', 'sohail', 'solomon',
'south', 'southe', 'special', 'spin', 'spot', 'sri', 'srinivasaghavan', 'srl', 'st', 'stand', 'stand', 'stroke', 'success',
'suffer', 'sunris', 'super', 'sylvest', 't', 'team', 'team', 'tendulkar', 'test', 'test', 'th', 'thaddeu', 'these', 'thilan',
'third', 'thoma', 'three', 'thrill', 'tim', 'time', 'timelin', 'time', 'titl', 'to', 'today', 'toggl', 'ton', 'top', 'trafford',
'trent', 'tripl', 'trott', 'ts', 'turn', 'turner', 'twenti', 'two', 'u', 'unchang', 'up', 'url', 'user', 'uton', 'v', 'vasbert',
'venkataraghavan', 'version', 'video', 'video', 'view', 'villier', 'vs', 'w', 'waugh', 'way', 'wayn', 'wessel', 'west',
'while', 'white', 'wicket', 'wilfr', 'william', 'win', 'windi', 'winston', 'won', 'work', 'world', 'wreck', 'x', 'xavier',
'xi', 'y', 'york', 'yorkshir', 'youni', 'z', 'zealand']

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### ASSOCIATION CLUSTER VALUES


EXPANDED QUERY: West indies Test ODI All Latest Series

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### METRIC CLUSTER VALUES


EXPANDED QUERY: West Indies Matches Videos Crowd Cricket

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### SCALAR CLUSTER VALUES


EXPANDED QUERY: West
Indies First Class Cricket Crowd

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

Query 3: CSK


Local Document Set:
· Local Document 1
March 23 - May 12 2019\nBCCI.tv\nFollow Us\nFacebook\nInstagram\nTwitter\nIPL on the go\nGet all
the IPL action at your fingertips\nIndian Premier League\nHome\nSchedule\nResults\nVideos\nStats\
nFantasy\nNews\nTeams\nSocial\nAll Time Records\nArchive\nPhotos\nAbout\nAuction\nMobile
Products\nVenues\nMore\nHome\nSchedule\nResults\nVideos\nStats\nFantasy\nNews\nTeams\nSocial\
nAll Time Records\nArchive\nPhotos\nAbout\nAuction\nMobile Products\nVenues\nMarch 23 - May 19
2019\nBCCI.tv\niplt20.com\nFollow us\nFacebook\nInstagram\nTwitter\nPulse Poll\nClose mobile
menu\nPulse Poll\nOPEN QUESTIONS\nThere are currently no open questions.\nVoting History What's
Pulse?\nVoting History\nA breakdown of the questions you voted on will appear here.\nPulse connects
you to the\nLive\naction\nGet closer to the IPL 2019 this season by using Pulse while you watch the
LIVE matches. Pulse asks you a range of questions relevant to the LIVE action as it unfolds. Your votes
will be featured in the telecast in real-time and debated by the commentators, players and stars. If you've
got a great question of your own, we'd love to hear it.\nIPL on the go\nGet all the IPL action at your
fingertips\nChennai Super Kings\nDelhi Capitals\nKings XI Punjab\nKolkata Knight Riders\nMumbai
Indians\nRajasthan Royals\nRoyal Challengers Bangalore\nSunrisers Hyderabad\nChennai Super Kings\
n2010, 2011, 2018\nOwner Chennai Super Kings Cricket ltd.\nCoach Stephen Fleming\nVenue M. A.
Chidambaram Stadium\nCaptain MS Dhoni\nOfficial Team Site\nFacebook\nTwitter\nInstagram\
nOverview\nResults\nSquad\nNews\nArchive\nLatest Videos\n01:59\nImran Tahir reacts to funny tweets
and memes\n52,812 01:59 14 Apr 19\n01:59\nRussell will be ready for the RCB game on Friday -
Jacques Kallis\n7,545 01:59 14 Apr 19\n01:43\nM29: KKR vs CSK – Man of the Match – Imran Tahir\
n20,872 01:43 14 Apr 19\n01:30\nM29: KKR vs CSK – Dream11 GameChanger of the Match – Imran
Tahir\n11,575 01:30 14 Apr 19\n07:19\nM29: KKR vs CSK – Post Match Presentation\n25,598 07:19 14
Apr 19\n01:04\nM29: KKR vs CSK – Amazing Moments\n19,574 01:04 14 Apr 19\n06:01\nM29: KKR
vs CSK – Match Highlights\n256,221 06:01 14 Apr 19\n05:13\nM29: KKR vs CSK – Fall of Wickets\
n13,702 05:13 14 Apr 19\nLatest News\nPreview: M33 - SRH vs CSK\nNews 17 hrs 27 mins ago\
nMatch Report: M29 – KKR v CSK\nMatch Report 14 Apr 19\nPreview: M29 -KKR vs CSK\nNews 13
Apr 19\nMS Dhoni fined for Code of Conduct breach\nAnnouncements 11 Apr 19\nMatch Report: M25 -
RR vs CSK\nMatch Report 11 Apr 19\nPreview: M25 - RR vs CSK\nNews 10 Apr 19\nReport: Final -
CSK vs SRH\nMatch Report 27 May 18\nQuick Stats: Final - CSK vs SRH\nNews 27 May 18\nArchive\
nView More\n2019 - Match 29\nKKR\n161/8\nv\nCSK\n162/5\nChennai Super Kings won by 5 wickets\
n2018 - Final\nCSK\n181/2\nv\nSRH\n178/6\nChennai Super Kings won by 8 wickets\n2015 - Final\
nMI\n202/5\nv\nCSK\n161/8\nMumbai Indians won by 41 runs\n2014 - Qualifier 2\nKXIP\n226/6\nv\
nCSK\n202/7\nKings XI Punjab won by 24 runs\n2013 - Final\nCSK\n125/9\nv\nMI\n148/9\nMumbai
Indians won by 23 runs\n2012 - Final\nKKR\n192/5\nv\nCSK\n190/3\nKolkata Knight Riders won by 5
wickets\n2011 - Final\nCSK\n205/5\nv\nRCB\n147/8\nChennai Super Kings won by 58 runs\n2010 -
Final\nCSK\n168/5\nv\nMI\n146/9\nChennai Super Kings won by 22 runs\n2009 - 2nd Semi-Final\
nRCB\n149/4\nv\nCSK\n146/5\nRoyal Challengers Bangalore won by 6 wickets\n2008 - Final\nCSK\
n163/5\nv\nRR\n164/7\nRajasthan Royals won by 3 wickets\nView More\nTeam Social\nFOLLOW
#IPL\nFollow IPL on Facebook\nFollow IPL on Instagram\nFollow IPL on Twitter\nPrevious social
items Next social items\nSquad List\nTitle Sponsor\nOfficial broadcaster\nOfficial digital streaming
partner\nOfficial partners\nUmpire partner\nOfficial strategic timeout partner\nFOLLOW #IPL\nFollow
IPL on Facebook\nFollow IPL on Instagram\nFollow IPL on Twitter\nQuicklinks\nNews\nResults\
nVideos\nPhotos\nVenues\nStats\nArchive\nTickets\nTeams\nChennai Super Kings\nDelhi Capitals\
nKings XI Punjab\nKolkata Knight Riders\nMumbai Indians\nRajasthan Royals\nRoyal Challengers
Bangalore\nSunrisers Hyderabad\nAbout\nAbout Us\nAnti Corruption Policy\nAnti Doping Policy\nTUE
Application Form\nAnti Racism Policy\nClothing and Equipment Regulations\nCode of Conduct for

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

Players and Team Officials\nTitle\nIPL Code of Conduct for Match officials\nBrand and Protection


Guidelines\nGoverning Council\nIPL Committees\nMatch Playing Conditions\nPMOA Minimum
Standard\nSuspect Action Policy\nContact\nContact Us\nSponsorship\n© Copyright IPL, BCCI 2019. All
Rights Reserved.\nTerms and Conditions\nPrivacy Policy\nMar 23\nChennai Super Kings won by 7
wickets\nCSK\n71 / 3 V 70\nRCB\nMar 26\nChennai Super Kings won by 6 wickets\nDC\n147 / 6 V
150 / 4\nCSK\nMar 31\nChennai Super Kings won by 8 runs\nCSK\n175 / 5 V 167 / 8\nRR\nApr 3\
nMumbai Indians won by 37 runs\nMI\n170 / 5 V 133 / 8\nCSK\nApr 6\nChennai Super Kings won by
22 runs\nCSK\n160 / 3 V 138 / 5\nKXIP\nApr 9\nChennai Super Kings won by 7 wickets\nCSK\n111 / 3
V 108 / 9\nKKR\nApr 11\nChennai Super Kings won by 4 wickets\nRR\n151 / 7 V 155 / 6\nCSK\nApr
14\nChennai Super Kings won by 5 wickets\nKKR\n161 / 8 V 162 / 5\nCSK\nApr 17\nRajiv Gandhi Intl.
Cricket Stadium, Hyderabad\nSRH\n20:00 IST (14:30 GMT)\nCSK\nApr 21\nM. Chinnaswamy
Stadium, Bengaluru\nRCB\n20:00 IST (14:30 GMT)\nCSK\nApr 23\nM. A. Chidambaram Stadium,
Chennai\nCSK\n20:00 IST (14:30 GMT)\nSRH\nApr 26\nM. A. Chidambaram Stadium, Chennai\nCSK\
n20:00 IST (14:30 GMT)\nMI\nMay 1\nM. A. Chidambaram Stadium, Chennai\nCSK\n20:00 IST (14:30
GMT)\nDC\nMay 5\nIS Bindra Stadium, Mohali\nKXIP\n16:00 IST (10:30 GMT)\nCSK\n

LOCAL VOCAB LIST

['abandoned', 'abhishek', 'about', 'above', 'absence', 'action', 'addthis', 'affected', 'afghanistan', 'again',
'against', 'ago', 'ahmed', 'aid', 'ajinkya', 'allow', 'along', 'also', 'amazing', 'ambati', 'among', 'andre',
'announcements', 'anti', 'anything', 'appear', 'application', 'apr', 'april', 'archer', 'archive', 'archives', 'area',
'areas', 'around', 'asks', 'attacks', 'auction', 'august', 'australia', 'available', 'average', 'back', 'bairstow',
'balanced', 'ball', 'bangalore', 'bangladesh', 'batting', 'bcci', 'beat', 'beautifully', 'become', 'began',
'behaviour', 'behind', 'below', 'ben', 'bengaluru', 'best', 'better', 'between', 'betwen', 'bhui', 'bhuvneshwar',
'bindra', 'bit', 'both', 'bounce', 'bowl', 'bowling', 'brand', 'brathwaite', 'breach', 'breakdown', 'bringing',
'broadcaster', 'buttler', 'c', 'can', 'cape', 'capitals', 'captain', 'carlos', 'centuries', 'centurion', 'chahar',
'challengers', 'champions', 'chance', 'change', 'chase', 'chases', 'chawla', 'check', 'chennai', 'chepauk',
'chidambaram', 'chinnaswamy', 'choice', 'chopra', 'chris', 'cl', 'close', 'closer', 'clothing', 'clt', 'coach', 'code',
'com', 'combination', 'coming', 'commentators', 'committees', 'complete', 'concerned', 'conditions',
'conduct', 'connects', 'consistent', 'contact', 'content', 'copyright', 'corruption', 'could', 'couldn', 'council',
'cracks', 'cricket', 'cricketnews', 'crucial', 'csk', 'currently', 'date', 'david', 'day', 'dc', 'debated', 'december',
'deepak', 'defeated', 'defending', 'delhi', 'denly', 'despite', 'details', 'dew', 'dewy', 'dhawal', 'dhoni', 'didnt',
'digital', 'dinesh', 'disclaimer', 'doping', 'download', 'dream', 'dry', 'du', 'due', 'each', 'ease', 'easier',
'economical', 'economy', 'eden', 'eight', 'encounter', 'encounters', 'england', 'equipment', 'f', 'facebook',
'factor', 'faf', 'fall', 'fantasy', 'far', 'faster', 'featured', 'february', 'feels', 'ferguson', 'fewest', 'final', 'fined',
'fingertips', 'first', 'five', 'fixtures', 'fleming', 'flu', 'follow', 'force', 'form', 'four', 'free', 'fresh', 'friday', 'from',
'fu', 'full', 'funny', 'game', 'gamechanger', 'games', 'gandhi', 'gardens', 'get', 'getters', 'gill', 'given', 'gmt', 'go',
'good', 'gopal', 'got', 'governing', 'gowtham', 'grass', 'grassed', 'great', 'grounds', 'guidelines', 'gujarat',
'gurney', 'h', 'had', 'hamstring', 'hand', 'harbhajan', 'harry', 'has', 'having', 'hea', 'head', 'hear', 'heavier',
'hence', 'hey', 'hide', 'highlights', 'history', 'hits', 'home', 'honest', 'hooda', 'hot', 'hours', 'hrs', 'huge', 'humid',
'humidity', 'hundred', 'hurting', 'hyderabad', 'if', 'imran', 'including', 'india', 'indian', 'indians', 'indies',
'instagram', 'international', 'interspersed', 'intl', 'ipl', 'iplt', 'ireland', 'ist', 'items', 'jacques', 'jadeja', 'jadhav',
'jaipur', 'january', 'jaydev', 'joe', 'jofra', 'join', 'jonny', 'jos', 'july', 'june', 'just', 'kallis', 'kane', 'karthik', 'kaul',
'kedar', 'khaleel', 'khan', 'kings', 'kkr', 'knight', 'kolkata', 'krishna', 'krishnappa', 'kuggeleijn', 'kuldeep',
'kulkarni', 'kumar', 'kxip', 'labels', 'lanka', 'last', 'late', 'later', 'latest', 'league', 'length', 'less', 'like', 'likely',
'lions', 'list', 'little', 'live', 'lockie', 'lomror', 'looking', 'losing', 'loss', 'love', 'low', 'lowest', 'ltd', 'lynn', 'm',
'mahipal', 'make', 'man', 'mansingh', 'mar', 'march', 'match', 'matches', 'maximum', 'may', 'mc', 'memes',
'men', 'menu', 'met', 'mi', 'midhun', 'minimum', 'minor', 'mins', 'mitchell', 'mobile', 'mohali', 'mohit',
'moments', 'momentum', 'more', 'most', 'mostly', 'move', 'ms', 'much', 'mumbai', 'narine', 'natch', 'nd',
'needs', 'net', 'new', 'news', 'newsletter', 'next', 'nicely', 'nitish', 'no', 'november', 'now', 'numbers', 'october',
'offer', 'official', 'officials', 'once', 'one', 'only', 'open', 'openers', 'opening', 'options', 'other', 'out', 'over',
'overall', 'overs', 'overview', 'own', 'owner', 'pace', 'pages', 'pakistan', 'par', 'parag', 'partner', 'partners',
'pathan', 'patient', 'performances', 'photos', 'picked', 'pinterest', 'pitch', 'piyush', 'place', 'play', 'played',


'players', 'playing', 'plessis', 'pm', 'pmoa', 'point', 'points', 'policy', 'poll', 'poorest', 'positions', 'possible',
'post', 'powerplay', 'prashant', 'prasidh', 'predicted', 'predictio', 'prediction', 'premier', 'presentation',
'preview', 'previous', 'privacy', 'probable', 'probably', 'products', 'progressively', 'protection', 'pulse', 'pune',
'punjab', 'qualifier', 'question', 'questions', 'quick', 'quicklinks', 'r', 'racism', 'rahane', 'rahul', 'rain', 'raina',
'rajasthan', 'rajiv', 'rana', 'ranchi', 'range', 'rashid', 'rate', 'ravindra', 'rayudu', 'rcb', 'rd', 'reacts', 'readers',
'ready', 'real', 'record', 'records', 'reddit', 'regulations', 'released', 'relevant', 'replaced', 'replacing', 'report',
'reserved', 'respectively', 'result', 'results', 'return', 'ricky', 'riders', 'rights', 'rising', 'riyan', 'robbieuthappa',
'royal', 'royals', 'rr', 'ruled', 'run', 'runs', 'russell', 'rusult', 's', 'same', 'samson', 'sandeep', 'sanju', 'santner',
'sawai', 'schedule', 'score', 'scored', 'scores', 'scoring', 'scott', 'seamers', 'season', 'second', 'see', 'semi',
'september', 'seven', 'seventh', 'shane', 'shankar', 'shardul', 'share', 'sharing', 'sharma', 'should', 'shouldnt',
'show', 'shreyas', 'shubman', 'siddharth', 'side', 'sidebar', 'since', 'singh', 'site', 'sitemap', 'situation', 'six',
'skipper', 'slide', 'slow', 'smallest', 'smith', 'so', 'social', 'socialize', 'spin', 'spinners', 'spins', 'sponsor',
'sponsorship', 'squad', 'srh', 'sri', 'stadium', 'standard', 'standing', 'stars', 'start', 'stats', 'stephen', 'steve',
'steven', 'stokes', 'strain', 'strategic', 'streaming', 'subscribe', 'sudhesan', 'sunil', 'sunrisers', 'super',
'supergiants', 'suresh', 'surface', 'surfaces', 'suspect', 't', 'table', 'tahir', 'take', 'taken', 'team', 'teams', 'telecast',
'temperature', 'terms', 'th', 'thakur', 'than', 'then', 'therefore', 'third', 'though', 'three', 'through', 'tickets', 'till',
'time', 'timeout', 'times', 'timetable', 'title', 'to', 'took', 'top', 'topper', 'toss', 'total', 'tough', 'tournament',
'town', 'track', 'trending', 'tricky', 'tripathi', 'trivia', 'tue', 'tv', 'tweets', 'twice', 'twitter', 'two', 'types', 'uae',
'umpire', 'unadkat', 'unfolds', 'up', 'uppal', 'use', 'using', 'v', 'venue', 'venues', 'very', 'videos', 'view', 'vijay',
'visible', 'vivo', 'voted', 'votes', 'voting', 'vs', 'w', 'warm', 'warner', 'wasnt', 'watch', 'watson', 'way', 'we',
'weather', 'wed', 'well', 'were', 'west', 'whats', 'whatsapp', 'while', 'wicket', 'wickets', 'williamson', 'win',
'winner', 'winning', 'wins', 'witnessed', 'wk', 'won', 'would', 'xi', 'yadav', 'yet', 'youve', 'yusuf', 'zealand']

###### LOCAL STEM LIST

['abandon', 'abhishek', 'about', 'abov', 'absenc', 'action', 'addthi', 'affect', 'afghanistan', 'again', 'against', 'ago',
'ahm', 'aid', 'ajinkya', 'allow', 'along', 'also', 'amaz', 'ambati', 'among', 'andr', 'announc', 'anti', 'anyth',
'appear', 'applic', 'apr', 'april', 'archer', 'archiv', 'archiv', 'area', 'area', 'around', 'ask', 'attack', 'auction',
'august', 'australia', 'avail', 'averag', 'back', 'bairstow', 'balanc', 'ball', 'bangalor', 'bangladesh', 'bat', 'bcci',
'beat', 'beauti', 'becom', 'began', 'behaviour', 'behind', 'below', 'ben', 'bengaluru', 'best', 'better', 'between',
'betwen', 'bhui', 'bhuvneshwar', 'bindra', 'bit', 'both', 'bounc', 'bowl', 'bowl', 'brand', 'brathwait', 'breach',
'breakdown', 'bring', 'broadcast', 'buttler', 'c', 'can', 'cape', 'capit', 'captain', 'carlo', 'centuri', 'centurion',
'chahar', 'challeng', 'champion', 'chanc', 'chang', 'chase', 'chase', 'chawla', 'check', 'chennai', 'chepauk',
'chidambaram', 'chinnaswami', 'choic', 'chopra', 'chri', 'cl', 'close', 'closer', 'cloth', 'clt', 'coach', 'code', 'com',
'combin', 'come', 'comment', 'committe', 'complet', 'concern', 'condit', 'conduct', 'connect', 'consist',
'contact', 'content', 'copyright', 'corrupt', 'could', 'couldn', 'council', 'crack', 'cricket', 'cricketnew', 'crucial',
'csk', 'current', 'date', 'david', 'day', 'dc', 'debat', 'decemb', 'deepak', 'defeat', 'defend', 'delhi', 'denli', 'despit',
'detail', 'dew', 'dewi', 'dhawal', 'dhoni', 'didnt', 'digit', 'dinesh', 'disclaim', 'dope', 'download', 'dream', 'dri',
'du', 'due', 'each', 'eas', 'easier', 'econom', 'economi', 'eden', 'eight', 'encount', 'encount', 'england', 'equip', 'f',
'facebook', 'factor', 'faf', 'fall', 'fantasi', 'far', 'faster', 'featur', 'februari', 'feel', 'ferguson', 'fewest', 'final',
'fine', 'fingertip', 'first', 'five', 'fixtur', 'fleme', 'flu', 'follow', 'forc', 'form', 'four', 'free', 'fresh', 'friday', 'from',
'fu', 'full', 'funni', 'game', 'gamechang', 'game', 'gandhi', 'garden', 'get', 'getter', 'gill', 'given', 'gmt', 'go',
'good', 'gopal', 'got', 'govern', 'gowtham', 'grass', 'grass', 'great', 'ground', 'guidelin', 'gujarat', 'gurney', 'h',
'had', 'hamstr', 'hand', 'harbhajan', 'harri', 'ha', 'have', 'hea', 'head', 'hear', 'heavier', 'henc', 'hey', 'hide',
'highlight', 'histori', 'hit', 'home', 'honest', 'hooda', 'hot', 'hour', 'hr', 'huge', 'humid', 'humid', 'hundr', 'hurt',
'hyderabad', 'if', 'imran', 'includ', 'india', 'indian', 'indian', 'indi', 'instagram', 'intern', 'interspers', 'intl', 'ipl',
'iplt', 'ireland', 'ist', 'item', 'jacqu', 'jadeja', 'jadhav', 'jaipur', 'januari', 'jaydev', 'joe', 'jofra', 'join', 'jonni', 'jo',

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

'juli', 'june', 'just', 'kalli', 'kane', 'karthik', 'kaul', 'kedar', 'khaleel', 'khan', 'king', 'kkr', 'knight', 'kolkata',


'krishna', 'krishnappa', 'kuggeleijn', 'kuldeep', 'kulkarni', 'kumar', 'kxip', 'label', 'lanka', 'last', 'late', 'later',
'latest', 'leagu', 'length', 'less', 'like', 'like', 'lion', 'list', 'littl', 'live', 'locki', 'lomror', 'look', 'lose', 'loss', 'love',
'low', 'lowest', 'ltd', 'lynn', 'm', 'mahip', 'make', 'man', 'mansingh', 'mar', 'march', 'match', 'match',
'maximum', 'may', 'mc', 'meme', 'men', 'menu', 'met', 'mi', 'midhun', 'minimum', 'minor', 'min', 'mitchel',
'mobil', 'mohali', 'mohit', 'moment', 'momentum', 'more', 'most', 'mostli', 'move', 'ms', 'much', 'mumbai',
'narin', 'natch', 'nd', 'need', 'net', 'new', 'news', 'newslett', 'next', 'nice', 'nitish', 'no', 'novemb', 'now',
'number', 'octob', 'offer', 'offici', 'offici', 'onc', 'one', 'onli', 'open', 'open', 'open', 'option', 'other', 'out', 'over',
'overal', 'over', 'overview', 'own', 'owner', 'pace', 'page', 'pakistan', 'par', 'parag', 'partner', 'partner', 'pathan',
'patient', 'perform', 'photo', 'pick', 'pinterest', 'pitch', 'piyush', 'place', 'play', 'play', 'player', 'play', 'plessi',
'pm', 'pmoa', 'point', 'point', 'polici', 'poll', 'poorest', 'posit', 'possibl', 'post', 'powerplay', 'prashant', 'prasidh',
'predict', 'predictio', 'predict', 'premier', 'present', 'preview', 'previou', 'privaci', 'probabl', 'probabl', 'product',
'progress', 'protect', 'puls', 'pune', 'punjab', 'qualifi', 'question', 'question', 'quick', 'quicklink', 'r', 'racism',
'rahan', 'rahul', 'rain', 'raina', 'rajasthan', 'rajiv', 'rana', 'ranchi', 'rang', 'rashid', 'rate', 'ravindra', 'rayudu', 'rcb',
'rd', 'react', 'reader', 'readi', 'real', 'record', 'record', 'reddit', 'regul', 'releas', 'relev', 'replac', 'replac', 'report',
'reserv', 'respect', 'result', 'result', 'return', 'ricki', 'rider', 'right', 'rise', 'riyan', 'robbieuthappa', 'royal', 'royal',
'rr', 'rule', 'run', 'run', 'russel', 'rusult', 's', 'same', 'samson', 'sandeep', 'sanju', 'santner', 'sawai', 'schedul',
'score', 'score', 'score', 'score', 'scott', 'seamer', 'season', 'second', 'see', 'semi', 'septemb', 'seven', 'seventh',
'shane', 'shankar', 'shardul', 'share', 'share', 'sharma', 'should', 'shouldnt', 'show', 'shreya', 'shubman',
'siddharth', 'side', 'sidebar', 'sinc', 'singh', 'site', 'sitemap', 'situat', 'six', 'skipper', 'slide', 'slow', 'smallest',
'smith', 'so', 'social', 'social', 'spin', 'spinner', 'spin', 'sponsor', 'sponsorship', 'squad', 'srh', 'sri', 'stadium',
'standard', 'stand', 'star', 'start', 'stat', 'stephen', 'steve', 'steven', 'stoke', 'strain', 'strateg', 'stream', 'subscrib',
'sudhesan', 'sunil', 'sunris', 'super', 'supergi', 'suresh', 'surfac', 'surfac', 'suspect', 't', 'tabl', 'tahir', 'take',
'taken', 'team', 'team', 'telecast', 'temperatur', 'term', 'th', 'thakur', 'than', 'then', 'therefor', 'third', 'though',
'three', 'through', 'ticket', 'till', 'time', 'timeout', 'time', 'timet', 'titl', 'to', 'took', 'top', 'topper', 'toss', 'total',
'tough', 'tournament', 'town', 'track', 'trend', 'tricki', 'tripathi', 'trivia', 'tue', 'tv', 'tweet', 'twice', 'twitter', 'two',
'type', 'uae', 'umpir', 'unadkat', 'unfold', 'up', 'uppal', 'use', 'use', 'v', 'venu', 'venu', 'veri', 'video', 'view',
'vijay', 'visibl', 'vivo', 'vote', 'vote', 'vote', 'vs', 'w', 'warm', 'warner', 'wasnt', 'watch', 'watson', 'way', 'we',
'weather', 'wed', 'well', 'were', 'west', 'what', 'whatsapp', 'while', 'wicket', 'wicket', 'williamson', 'win',
'winner', 'win', 'win', 'wit', 'wk', 'won', 'would', 'xi', 'yadav', 'yet', 'youv', 'yusuf', 'zealand']

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### ASSOCIATION CLUSTER VALUES


###### EXPANDED QUERY: CSK VIVO RCB RR DC SRH

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### METRIC CLUSTER VALUES


EXPANDED QUERY: CSK KKR Match MI IPL Preview

##### Surya Alla Anugreh Raina

##### sxa180109 axr180055

###### SCALAR CLUSTER VALUES


EXPANDED QUERY: CSK HEAD KXIP Chennai Kings Super
COLLABORATION WITH TEAM

We as a group had multiple discussions about the integration of the query expansion module into the
GUI. We decided to implement Rocchio Algorithm. Regular interactions helped the project to remain in
sync. After talking at great lengths with Chandhya Thirugnanasambantham (team mate responsible for
GUI), we decided on a design.


