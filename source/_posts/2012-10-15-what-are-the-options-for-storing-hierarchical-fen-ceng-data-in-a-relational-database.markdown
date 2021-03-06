---
layout: post
title: "What are the Options for Storing Hierarchical 分层 Data in a Relational Database?"
date: 2012-04-13 23:29
comments: true
categories: [rails,专业]
tags: [Database,Hierarchial,Relational,Storing,分层]
---
## What are the Options for Storing Hierarchical 分层 Data in a Relational Database?
Generally speaking you're making a decision between fast read times (e.g. nested set) or fast write times (adjacency list). Usually you end up with a combination of the options below that best fit your needs. The following provides some in depth reading:
<ul>
	<li><a href="http://vadimtropashko.wordpress.com/2008/08/09/one-more-nested-intervals-vs-adjacency-list-comparison/" rel="nofollow">One more Nested Intervals vs. Adjacency List comparison</a>: <em>the best comparison</em> of Adjacency List, Materialized Path, Nested Set and Nested Interval I've found.</li>
	<li><a href="http://www.slideshare.net/billkarwin/models-for-hierarchical-data" rel="nofollow">Models for hierarchical data</a>: slides with good explanations of tradeoffs and example usage</li>
	<li><a href="http://mikehillyer.com/articles/managing-hierarchical-data-in-mysql/" rel="nofollow">Representing hierarchies in MySQL</a>: very good overview of Nested Set in particular</li>
	<li><a href="http://troels.arvin.dk/db/rdbms/links/#hierarchical" rel="nofollow">Hierarchical data in RDBMSs</a>: most comprehensive and well organized set of links I've seen, but not much in the way on explanation</li>
</ul>
<strong>Options</strong>

Ones I am aware of and general features:
<ol>
	<li><a href="http://en.wikipedia.org/wiki/Adjacency_list" rel="nofollow">Adjacency List</a>:
<ul>
	<li>Columns: ID, ParentID</li>
	<li>Easy to implement.</li>
	<li>Cheap node moves, inserts, and deletes.</li>
	<li>Expensive to find level (can store as a computed column), ancestry &amp; descendants (Bridge Hierarchy combined with level column can solve), path (Lineage Column can solve).</li>
	<li>Use <a href="http://en.wikipedia.org/wiki/Common_table_expressions" rel="nofollow">Common Table Expressions</a> in those databases that support them to traverse.</li>
</ul>
</li>
	<li><a href="http://en.wikipedia.org/wiki/Nested_set_model" rel="nofollow">Nested Set</a> (a.k.a Modified Preorder Tree Traversal)
<ul>
	<li>First described by Joe Celko - covered in depth in his book <a href="http://rads.stackoverflow.com/amzn/click/1558609202" rel="nofollow">Trees and Hierarchies in SQL for Smarties</a></li>
	<li>Columns: Left, Right</li>
	<li>Cheap level, ancestry, descendants</li>
	<li>Compared to Adjacency List, moves, inserts, deletes more expensive.</li>
	<li>Requires a specific sort order (e.g. created). So sorting all descendants in a different order requires additional work.</li>
</ul>
</li>
	<li><a href="http://communities.bmc.com/communities/docs/DOC-9902" rel="nofollow">Nested Intervals</a>
<ul>
	<li>Combination of Nested Sets and Materialized Path where left/right columns are floating point decimals instead of integers and encode the path information. In the later development of this idea nested intervals gave rise to <a href="http://vadimtropashko.files.wordpress.com/2011/07/ch5.pdf" rel="nofollow">matrix encoding</a>.</li>
</ul>
</li>
	<li><a href="http://intelligent-enterprise.informationweek.com/showArticle.jhtml;jsessionid=MRBJR2LLRV1ANQE1GHPSKH4ATMY32JVN?articleID=219400252" rel="nofollow">Bridge Table</a> (a.k.a. <a href="http://dirtsimple.org/2010/11/simplest-way-to-do-tree-based-queries.html" rel="nofollow">Closure Table</a>: some good ideas about how to use triggers for maintaining this approach)
<ul>
	<li>Columns: ancestor, descendant</li>
	<li>Stands apart from table it describes.</li>
	<li>Can include some nodes in more than one hierarchy.</li>
	<li>Cheap ancestry and descendants (albeit not in what order)</li>
	<li>For complete knowledge of a hierarchy needs to be combined with another option.</li>
</ul>
</li>
	<li><a href="http://evolt.org/node/4047/" rel="nofollow">Flat Table</a>
<ul>
	<li>A modification of the Adjacency List that adds a Level and Rank (e.g. ordering) column to each record.</li>
	<li>Expensive move and delete</li>
	<li>Cheap ancestry and descendants</li>
	<li>Good Use: threaded discussion - forums / blog comments</li>
</ul>
</li>
	<li><a href="http://www.ferdychristant.com/blog//articles/DOMM-7QJPM7" rel="nofollow">Lineage Column</a> (a.k.a. <a href="http://communities.bmc.com/communities/docs/DOC-9902" rel="nofollow">Materialized Path</a>, Path Enumeration)
<ul>
	<li>Column: lineage (e.g. /parent/child/grandchild/etc...)</li>
	<li>Limit to how deep the hierarchy can be.</li>
	<li>Descendants cheap (e.g. <code>LEFT(lineage, #) = '/enumerated/path'</code>)</li>
	<li>Ancestry tricky (database specific queries)</li>
</ul>
</li>
</ol>
<strong>Database Specific Notes</strong>

<em>MySQL</em>
<ul>
	<li><a href="http://explainextended.com/2009/09/29/adjacency-list-vs-nested-sets-mysql/" rel="nofollow">Use session variables for Adjacency List</a></li>
</ul>
<em>Oracle</em>
<ul>
	<li>Use <a href="http://www.ypl.com/oracle/sql/hierarchical_queries/html_deep/index.html" rel="nofollow">CONNECT BY</a> to traverse Adjacency Lists</li>
</ul>
<em>PostgreSQL</em>
<ul>
	<li><a href="http://www.postgresql.org/docs/current/static/ltree.html" rel="nofollow">ltree datatype</a> for Materialized Path</li>
</ul>
<em>SQL Server</em>
<ul>
	<li><a href="http://msdn.microsoft.com/en-us/magazine/cc794278.aspx" rel="nofollow">General summary</a></li>
	<li>2008 offers <a href="http://msdn.microsoft.com/en-us/library/bb677290.aspx" rel="nofollow">HierarchyId</a> data type appears to help with Lineage Column approach and expand the depth that can be represented.</li>
</ul>
Some articles from my blog on the subject:
<ul>
	<li><a href="http://explainextended.com/2009/09/29/adjacency-list-vs-nested-sets-mysql/" rel="nofollow"><strong>Adjacency list vs. nested sets: MySQL</strong></a></li>
	<li><a href="http://explainextended.com/2009/09/24/adjacency-list-vs-nested-sets-postgresql/" rel="nofollow"><strong>Adjacency list vs. nested sets: PostgreSQL</strong></a></li>
	<li><a href="http://explainextended.com/2009/09/28/adjacency-list-vs-nested-sets-oracle/" rel="nofollow"><strong>Adjacency list vs. nested sets: Oracle</strong></a></li>
	<li><a href="http://explainextended.com/2009/09/25/adjacency-list-vs-nested-sets-sql-server/" rel="nofollow"><strong>Adjacency list vs. nested sets: SQL Server</strong></a></li>
	<li><a href="http://explainextended.com/2009/03/17/hierarchical-queries-in-mysql/" rel="nofollow"><strong>Hierarchical queries in MySQL</strong></a> (querying adjacency lists in <code>MySQL</code>)</li>
</ul>
