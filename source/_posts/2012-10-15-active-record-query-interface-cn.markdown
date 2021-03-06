---
layout: post
title: "Active Record-Query Interface-CN"
date: 2011-12-02 17:54
comments: true
categories: [rails]
tags: [active record,CN,interface,query,rails,ruby,syntax]
---
## Active Record-Query Interface-CN
<h1>Active Record</h1>
Active Record<span style="font-family: DejaVu Sans;">（中文名：活动记录）是一种领域模型模式，特点是一个模型类对应关系型数据库中的一个表，而模型类的一个实例对应表中的一行记录。</span>Active Record <span style="font-family: DejaVu Sans;">和</span>Row Gateway <span style="font-family: DejaVu Sans;">（行记录入口）十分相似，但前者是领域模型，后者是一种数据源模式。关系型数据库往往通过外键来表述实体关系，</span>Active Record <span style="font-family: DejaVu Sans;">在数据源层面上也将这种关系映射为对象的关联和聚集。</span>

<span style="font-family: DejaVu Sans;">　　</span>Active Record <span style="font-family: DejaVu Sans;">适合非常简单的领域需求，尤其在领域模型和数据库模型十分相似的情况下。如果遇到更加复杂的领域模型结构（例如用到继承、策略的领域模型），往往需要使用分离数据源的领域模型，结合</span>Data Mapper <span style="font-family: DejaVu Sans;">（数据映射器）使用。</span>

<span style="font-family: DejaVu Sans;">　　</span>Active Record <span style="font-family: DejaVu Sans;">驱动框架一般兼有</span>ORM <span style="font-family: DejaVu Sans;">框架的功能，但</span>ActivActive Recorde Record <span style="font-family: DejaVu Sans;">不是简单的</span>ORM<span style="font-family: DejaVu Sans;">，正如和</span>Row Gateway <span style="font-family: DejaVu Sans;">的区别。著名的例子是全栈（</span>Full Stack<span style="font-family: DejaVu Sans;">）</span>Web <span style="font-family: DejaVu Sans;">开发框架</span>Ruby on Rails <span style="font-family: DejaVu Sans;">，其默认使用一个纯</span>Ruby <span style="font-family: DejaVu Sans;">写成的</span><strong>Active</strong><strong> </strong><strong>Record</strong><strong> </strong><span style="font-family: DejaVu Sans;"><strong>框架</strong>来驱动</span>MVC <span style="font-family: DejaVu Sans;">中的模型层。</span>

<span style="font-family: DejaVu Sans;">　　对象关系映射（</span>ORM<span style="font-family: DejaVu Sans;">）提供了概念性的、易于理解的模型化数据的方法。</span>ORM<span style="font-family: DejaVu Sans;">方法论基于三个核心原则：简单：以最基本的形式建模数据。传达性：数据库结构被任何人都能理解的语言文档化。精确性：基于数据模型创建正确标准化了的结构。</span>

<span style="font-family: DejaVu Sans;">　　在</span>Martin Fowler <span style="font-family: DejaVu Sans;">的《企业应用架构模式》一书中曾详细叙述了本模式。</span>

<span style="font-family: DejaVu Sans;">　　以下是著名的</span>Active Record <span style="font-family: DejaVu Sans;">驱动框架：</span>

<span style="font-family: DejaVu Sans;">　　</span>SQLObject(Python)

<span style="font-family: DejaVu Sans;">　　</span>Ruby on Rails ActiveRecord (Ruby)

<span style="font-family: DejaVu Sans;">　　</span>Yii Framework ActiveRecord (PHP)

<span style="font-family: DejaVu Sans;">　　</span>Castle ActiveRecord (.NET)
<h2>Migrations</h2>
Migrations are a convenient way for you to alter<span style="font-family: DejaVu Sans;">移动</span>your database in a structured and organized manner.Migrations<span style="font-family: DejaVu Sans;">是一种很便捷的方法让你能够以一种结构化的和有组织的方式来迁移你的数据库。</span>You could edit fragments of SQL by hand but you would then be responsible for telling other developers that they need to go and run them.<span style="font-family: DejaVu Sans;">你可以手动编辑</span>SQL<span style="font-family: DejaVu Sans;">片段，而且你有责任把这些告诉其他的开发人员，因为他们需要开发和使用它们。</span>You’d also have to keep track of which changes need to be run against the production machines next time you deploy.<span style="font-family: DejaVu Sans;">你也可以跟踪对你部署的代码在接下来的</span>production<span style="font-family: DejaVu Sans;">机器（将会）发生的变化。</span>

Active Record tracks which migrations have already been run so all you have to do is update your source and run <tt>rake</tt><tt> </tt><tt>db:migrate</tt>.Active Record<span style="font-family: DejaVu Sans;">跟踪并迁移你已经运行过的（代码和数据），而你只需要在更新了你的源代码的时候执行</span><tt>rake</tt><tt> </tt><tt>db:migrate</tt><span style="font-family: DejaVu Sans;"><tt>。</tt></span>Active Record will work out which migrations should be run.Active Recor<span style="font-family: DejaVu Sans;">将会计算出那些迁移需要被执行。</span>It will also update your <tt>db/schema.rb</tt> file to match the structure of your database.<span style="font-family: DejaVu Sans;">它还会更新你的</span><tt>db/schema.rb</tt><span style="font-family: DejaVu Sans;"><tt>文件使其于你的数据库结构相匹配。</tt></span>

Rails<span style="font-family: DejaVu Sans;">使用的是</span><strong>Active</strong><strong> </strong><strong>Record</strong><strong> </strong><span style="font-family: DejaVu Sans;"><strong>框架</strong>来处理数据迁移，这里笔者把</span>Active Record <span style="font-family: DejaVu Sans;">框架放在一个地方学习了，如需了解</span>Migration<span style="font-family: DejaVu Sans;">部分需要直接阅读</span>Migration<span style="font-family: DejaVu Sans;">部分。</span>

&nbsp;
<h2>Active Record Validations and Callbacks <span style="font-family: WenQuanYi Micro Hei;">活动记录验证和回调</span></h2>
This guide teaches you how to hook<span style="font-family: DejaVu Sans;">勾子</span>into the life cycle of your Active Record objects.<span style="font-family: DejaVu Sans;">这个教程指导你怎样挂接到你的</span>Active Record objects<span style="font-family: DejaVu Sans;">的生存周期。</span>You will learn how to validate the state of objects before they go into the database, and how to perform custom operations at certain points in the object life cycle.<span style="font-family: DejaVu Sans;">你将会学习到在将数据对象存入数据库之前怎样验证它们的状态，以及在对象生存周期的一些点上怎样执行定制操作。</span>

Rails<span style="font-family: DejaVu Sans;">使用的是</span><strong>Active</strong><strong> </strong><strong>Record</strong><strong> </strong><span style="font-family: DejaVu Sans;"><strong>框架</strong>来处理验证和回调，这里笔者把</span>Active Record <span style="font-family: DejaVu Sans;">框架放在一个地方学习了，如需了解</span>Migration<span style="font-family: DejaVu Sans;">部分需要直接阅读</span>Validations and Callbacks <span style="font-family: DejaVu Sans;">部分。</span>
<h2><strong>A</strong><strong> </strong><strong>Guide</strong><strong> </strong><strong>to</strong><strong> </strong><strong>Active</strong><strong> </strong><strong>Record</strong><strong> </strong><strong>Associations</strong></h2>
This guide covers the association features of Active Record. By referring to this guide, you will be able to:<span style="font-family: DejaVu Sans;">本教程涵盖了</span>Active Record<span style="font-family: DejaVu Sans;">的关系部分的特性。（通过）这个教程提及的，你将能够：</span>
<ul>
	<li>Declare associations between Active Record models <span style="font-family: DejaVu Sans;">在</span>Active Record<span style="font-family: DejaVu Sans;">的</span>models<span style="font-family: DejaVu Sans;">中声明（它们的）关系</span></li>
	<li>Understand the various types of Active Record associations <span style="font-family: DejaVu Sans;">明白各种类型的</span>Active Record<span style="font-family: DejaVu Sans;">关系</span></li>
	<li>Use the methods added to your models by creating associations <span style="font-family: DejaVu Sans;">通过添加方法到</span>models<span style="font-family: DejaVu Sans;">（的形式）来创建关系</span></li>
</ul>
<h2>Active Record Query Interface<span style="font-family: WenQuanYi Micro Hei;">（基于）活动记录的查询接口</span></h2>
This guide covers different ways to retrieve data from the database using Active Record. By referring to this guide, you will be able to:

<span style="font-family: DejaVu Sans;">这个教程涵盖了使用基于</span>Active Record <span style="font-family: DejaVu Sans;">的不同方式从数据库检索数据。同过参考这个教程，你可以掌握：</span>
<ul>
	<li>Find records using a variety of methods and conditions <span style="font-family: DejaVu Sans;">使用各种方法和条件查找记录</span></li>
	<li>Specify the order, retrieved attributes, grouping, and other properties of the found records<span style="font-family: DejaVu Sans;">对查找的记录指定顺序，检索属性，组，和其他属性</span></li>
	<li>Use eager<span style="font-family: DejaVu Sans;">急于</span>loading to reduce the number of database queries needed for data retrieval <span style="font-family: DejaVu Sans;">使用预先加载，以减少数据检索所需的数据库查询数量</span></li>
	<li>Use dynamic finders methods <span style="font-family: DejaVu Sans;">使用多元搜索方法</span></li>
	<li>Check for the existence of particular records <span style="font-family: DejaVu Sans;">在特定的记录部分检查存在的记录</span></li>
	<li>Perform various calculations on Active Record models <span style="font-family: DejaVu Sans;">在</span>Active Record <span style="font-family: DejaVu Sans;">模型中执行各种计算</span></li>
</ul>
<span style="color: #800000;">This</span><span style="color: #800000;">Guide</span><span style="color: #800000;">is</span><span style="color: #800000;">based</span><span style="color: #800000;">on</span><span style="color: #800000;">Rails</span><span style="color: #800000;">3.0.</span><span style="color: #800000;">Some</span><span style="color: #800000;">of</span><span style="color: #800000;">the</span><span style="color: #800000;">code</span><span style="color: #800000;">shown</span><span style="color: #800000;">here</span><span style="color: #800000;">will</span><span style="color: #800000;">not</span><span style="color: #800000;">work</span><span style="color: #800000;">in</span><span style="color: #800000;">other</span><span style="color: #800000;">versions</span><span style="color: #800000;">of</span><span style="color: #800000;">Rails.</span>

If you’re used to using raw SQL to find database records then, generally, you will find that there are better ways to carry out<span style="font-family: DejaVu Sans;">进行</span>the same operations in Rails. Active Record insulates you from the need to use SQL in most cases.<span style="font-family: DejaVu Sans;">如果你使用过原生的</span>SQL<span style="font-family: DejaVu Sans;">（语句）来查询数据库，那么，一般情况下，你将会发现（对数据库）进行同样的操作在</span>Rails<span style="font-family: DejaVu Sans;">中会有这更好的方法。</span>Active Record<span style="font-family: DejaVu Sans;">在大多数情况下会让你远离你（以前）需要使用的</span>SQL<span style="font-family: DejaVu Sans;">查询语句。</span>

Code examples throughout this guide will refer to one or more of the following models:<span style="font-family: DejaVu Sans;">贯穿这个教材代码示例将会参考一个或多个下面的模型：</span>

All of the following models use <tt>id</tt> as the primary key, unless specified otherwise.<span style="font-family: DejaVu Sans;">所有的模型都会使用</span>id<span style="font-family: DejaVu Sans;">作为主键，除非指定了其他主键。</span>

<em>class Client &lt; ActiveRecord::Base</em>

<em>has_one :address</em>

<em>has_many :orders</em>

<em>has_and_belongs_to_many :roles</em>

<em>end</em>

&nbsp;

<em>class Address &lt; ActiveRecord::Base</em>

<em>belongs_to :client</em>

<em>end</em>

&nbsp;

<em>class Order &lt; ActiveRecord::Base</em>

<em>belongs_to :client, :counter_cache =&gt; true</em>

<em>end</em>

&nbsp;

<em>class Role &lt; ActiveRecord::Base</em>

<em>has_and_belongs_to_many :clients</em>

<em>end</em>

Active Record will perform queries on the database for you and is compatible<span style="font-family: DejaVu Sans;">兼容</span>with most database systems (MySQL, PostgreSQL and SQLite to name a few). Regardless of which database system you’re using, the Active Record method format will always be the same.

Active Record<span style="font-family: DejaVu Sans;">将会为你在数据库中执行查询并且它兼容大多数的数据库系统（</span>MySQL, PostgreSQL and SQLite<span style="font-family: DejaVu Sans;">这里仅仅列举这些）。不管你使用的是何种数据库系统，</span>Active Record<span style="font-family: DejaVu Sans;">的方法格式通常是相同的。</span>
<h3><a name="retrieving-objects-from-the-database"></a> 1 Retrieving Objects from the Database<span style="font-family: WenQuanYi Micro Hei;">在数据库中检索对象</span></h3>
To retrieve objects from the database, Active Record provides several finder methods. Each finder method allows you to pass arguments into it to perform certain queries on your database without writing raw SQL.

<span style="font-family: DejaVu Sans;">为了从数据库中检索对象，</span>Active Record<span style="font-family: DejaVu Sans;">提供了一些查询方法。每个查询方法都运行你带入参数并在数据库中执行查询而不用写</span>SQL<span style="font-family: DejaVu Sans;">自身的语句。</span>

The methods are:
<ul>
	<li><tt>where</tt></li>
	<li><tt>select</tt></li>
	<li><tt>group</tt></li>
	<li><tt>order</tt></li>
	<li><tt>reorder</tt></li>
	<li><tt>reverse_order</tt> <span style="font-family: DejaVu Sans;">逆向排序</span></li>
	<li><tt>limit</tt></li>
	<li><tt>offset</tt> <span style="font-family: DejaVu Sans;">偏移</span></li>
	<li><tt>joins</tt></li>
	<li><tt>includes</tt></li>
	<li><tt>lock</tt></li>
	<li><tt>readonly</tt></li>
	<li><tt>from</tt></li>
	<li><tt>having</tt></li>
</ul>
All of the above methods return an instance of <tt>ActiveRecord::Relation</tt>.<span style="font-family: DejaVu Sans;">所有以上方法会返回一个</span><tt>ActiveRecord::Relation</tt><span style="font-family: DejaVu Sans;"><tt>的实例。</tt></span>

Primary operation of <tt>Model.find(options)</tt> can be summarized as:

<tt>Model.find(options)</tt><span style="font-family: DejaVu Sans;"><tt>主要的操作可以被概括为：</tt></span>
<ul>
	<li>Convert the supplied options to an equivalent SQL query. <span style="font-family: DejaVu Sans;">转换提供的（查询）选项为等同的</span>SQL<span style="font-family: DejaVu Sans;">查询</span></li>
	<li>Fire the SQL query and retrieve the corresponding results from the database. <span style="font-family: DejaVu Sans;">开始</span>SQL<span style="font-family: DejaVu Sans;">查询并且检索从数据库相应的结果</span></li>
	<li>Instantiate the equivalent Ruby object of the appropriate model for every resulting row. <span style="font-family: DejaVu Sans;">把每个（数据库中原生的）结果实例化等同的</span>Ruby<span style="font-family: DejaVu Sans;">对象</span></li>
	<li>Run <tt>after_find</tt> callbacks if any. <span style="font-family: DejaVu Sans;">运行</span><tt>after_find</tt><span style="font-family: DejaVu Sans;"><tt>进行回调如果需要</tt></span></li>
</ul>
<h4><a name="retrieving-a-single-object"></a>1.1 Retrieving a Single Object<span style="font-family: WenQuanYi Micro Hei;">检索单个对象</span></h4>
Active Record lets you retrieve a single object using five different ways.

Active Record<span style="font-family: DejaVu Sans;">让你可以使用五种不同的方法检索单个对象。</span>
<h5><a name="using-a-primary-key"></a>1.1.1 Using a Primary Key<span style="font-family: WenQuanYi Micro Hei;">使用主键查询</span></h5>
Using <tt>Model.find(primary_key)</tt>, you can retrieve the object corresponding to the supplied <em>primary</em><em> </em><em>key</em> and matching the supplied options (if any). For example:

<span style="font-family: DejaVu Sans;"><tt>使用</tt></span><tt>Model.find(primary_key)</tt><span style="font-family: DejaVu Sans;"><tt>，你可以检索对象通过提供相应的主键或者匹配提供的其他选项（如果存在）。例如：</tt></span>

<tt><em>#</em></tt><tt><em> </em></tt><tt><em>Find</em></tt><tt><em> </em></tt><tt><em>the</em></tt><tt><em> </em></tt><tt><em>client</em></tt><tt><em> </em></tt><tt><em>with</em></tt><tt><em> </em></tt><tt><em>primary</em></tt><tt><em> </em></tt><tt><em>key</em></tt><tt><em> </em></tt><tt><em>(id)</em></tt><tt><em> </em></tt><tt><em>1.</em></tt>

<tt><em>rails</em></tt><tt><em> </em></tt><tt><em>console</em></tt>

<tt><em>Loading development environment (Rails 3.1.1)</em></tt>

<tt><em>irb(main):001:0&gt; p=Post.find(1)</em></tt>

<tt> </tt><tt><em>Post Load (9.0ms) SELECT "posts".* FROM "posts" WHERE "posts"."id" = ? LIMIT 1 [["id", 1]]</em></tt>

<tt><em>=&gt; #&lt;Post id: 1, name: "</em></tt><span style="font-family: DejaVu Sans;"><tt><em>阿飞姐姐</em></tt></span><tt><em>", title: "</em></tt><span style="font-family: DejaVu Sans;"><tt><em>接口姐姐</em></tt></span><tt><em>", content: "12342424", created_at: "2011-11-05 15:10:41", updated_at: "2011-11-05 15:10:41"&gt;</em></tt>

<tt>SQL</tt><tt> </tt><tt>equivalent</tt><tt> </tt><tt>of</tt><tt> </tt><tt>the</tt><tt> </tt><tt>above</tt><tt> </tt><tt>is:</tt>

<tt><em>SELECT * FROM posts WHERE (posts.id = 1)</em></tt>

<tt>Model.find(primary_key)</tt> will raise an <tt>ActiveRecord::RecordNotFound</tt> exception if no matching record is found.

<tt>Model.find(primary_key)</tt><span style="font-family: DejaVu Sans;"><tt>如果没有记录匹配则会抛出一个</tt><tt></tt></span><tt>ActiveRecord::RecordNotFound</tt><span style="font-family: DejaVu Sans;"><tt>异常。</tt></span>
<h5><a name="first"></a>1.1.2 <tt>first</tt></h5>
<tt>Model.first</tt> finds the first record matched by the supplied options. For example:

<tt>Model.first</tt><span style="font-family: DejaVu Sans;"><tt>找到与提供的选项匹配的第一条记录。例如：</tt></span>

<tt><em>client = Client.first</em></tt>

<tt><em>=&gt; #&lt;Client id: 1, first_name: "Lifo"&gt;</em></tt>

<tt><em>irb(main):018:0&gt; Post.first</em></tt>

<tt> </tt><tt><em>Post Load (0.4ms) SELECT "posts".* FROM "posts" LIMIT 1</em></tt>

<tt><em>=&gt; #&lt;Post id: 2, name: "jhjguxin", title: "test console", content: "A new post to test console", created_at: "2011-11-05 15:55:17", updated_at: "2011-11-05 15:55:17"</em></tt>

SQL equivalent of the above is:

<tt><em>SELECT * FROM clients LIMIT 1</em></tt>

<tt>Model.first</tt> returns <tt>nil</tt> if no matching record is found. No exception will be raised.

<span style="font-family: DejaVu Sans;"><tt>如果没有记录匹配</tt></span><tt>Model.first</tt><span style="font-family: DejaVu Sans;"><tt>会返回</tt></span><tt>nil</tt><span style="font-family: DejaVu Sans;"><tt>。不会抛出异常。</tt></span>
<h5><a name="last"></a>1.1.3 <tt>last</tt></h5>
<tt>Model.last</tt> finds the last record matched by the supplied options. For example:

<tt><em>client = Client.last</em></tt>

<tt><em>=&gt; #&lt;Client id: 221, first_name: "Russel"&gt;</em></tt>

SQL equivalent of the above is:<span style="font-family: DejaVu Sans;">上面等同的</span>SQL<span style="font-family: DejaVu Sans;">语句是：</span>

<tt><em>SELECT * FROM clients ORDER BY clients.id DESC LIMIT 1</em></tt>

<tt><em>##SELECT "posts".* FROM "posts" ORDER BY "posts"."id" DESC LIMIT 1</em></tt>

<tt>Model.last</tt> returns <tt>nil</tt> if no matching record is found. No exception will be raised.

<span style="font-family: DejaVu Sans;">如果没有记录匹配</span><tt>Model.last</tt><span style="font-family: DejaVu Sans;"><tt>会返回</tt></span><tt>nil</tt><span style="font-family: DejaVu Sans;"><tt>。不会抛出异常。</tt></span>
<h4><a name="retrieving-multiple-objects"></a>1.2 Retrieving Multiple Objects<span style="font-family: WenQuanYi Micro Hei;">检索多个目标</span></h4>
<h5><a name="using-multiple-primary-keys"></a>1.2.1 Using Multiple Primary Keys<span style="font-family: WenQuanYi Micro Hei;">使用多个主键</span></h5>
<tt>Model.find(array_of_primary_key)</tt> also accepts an array of <em>primary</em><em> </em><em>keys</em>. An array of all the matching records for the supplied <em>primary</em><em> </em><em>keys</em> is returned. For example:

<tt>Model.find(array_of_primary_key)</tt><span style="font-family: DejaVu Sans;"><tt>也接受一个主键数组。将会返回一个由所有与提供的主键匹配的记录组成的数组。例如：</tt></span>

<tt><em># Find the clients with primary keys 1 and 10.</em></tt>

<tt><em>client = Client.find(1, 10) # Or even Client.find([1, 10])</em></tt>

<tt><em>=&gt; [#&lt;Client id: 1, first_name: =&gt; "Lifo"&gt;, #&lt;Client id: 10, first_name: =&gt; "Ryan"&gt;]</em></tt>

SQL equivalent of the above is:

<tt><em>SELECT * FROM clients WHERE (clients.id IN (1,10))</em></tt>

<tt><span style="color: #800000;">Model.find(array_of_primary_key)</span></tt><span style="color: #800000;">will</span><span style="color: #800000;">raise</span><span style="color: #800000;">an</span><tt><span style="color: #800000;">ActiveRecord::RecordNotFound</span></tt><span style="color: #800000;">exception</span><span style="color: #800000;">unless</span><span style="color: #800000;">a</span><span style="color: #800000;">matching</span><span style="color: #800000;">record</span><span style="color: #800000;">is</span><span style="color: #800000;">found</span><span style="color: #800000;">for</span><strong><span style="color: #800000;">all</span></strong><span style="color: #800000;">of</span><span style="color: #800000;">the</span><span style="color: #800000;">supplied</span><span style="color: #800000;">primary</span><span style="color: #800000;">keys.</span>

<tt><span style="color: #800000;">Model.find(array_of_primary_key)</span></tt><span style="font-family: DejaVu Sans;"><tt><span style="color: #800000;">只要有一条记录没有找到就会抛出</span></tt><tt></tt></span><tt><span style="color: #800000;">ActiveRecord::RecordNotFound</span></tt><span style="font-family: DejaVu Sans;"><tt><span style="color: #800000;">异常</span></tt></span>

<tt><span style="color: #800000;">ActiveRecord::RecordNotFound:</span></tt><tt></tt><tt><span style="color: #800000;">Couldn't</span></tt><tt></tt><tt><span style="color: #800000;">find</span></tt><tt></tt><tt><span style="color: #800000;">all</span></tt><tt></tt><tt><span style="color: #800000;">Posts</span></tt><tt></tt><tt><span style="color: #800000;">with</span></tt><tt></tt><tt><span style="color: #800000;">IDs</span></tt><tt></tt><tt><span style="color: #800000;">(2,</span></tt><tt></tt><tt><span style="color: #800000;">4,</span></tt><tt></tt><tt><span style="color: #800000;">5)</span></tt><tt></tt><tt><span style="color: #800000;">(found</span></tt><tt></tt><tt><span style="color: #800000;">2</span></tt><tt></tt><tt><span style="color: #800000;">results,</span></tt><tt></tt><tt><span style="color: #800000;">but</span></tt><tt></tt><tt><span style="color: #800000;">was</span></tt><tt></tt><tt><span style="color: #800000;">looking</span></tt><tt></tt><tt><span style="color: #800000;">for</span></tt><tt></tt><tt><span style="color: #800000;">3)</span></tt>
<h4><a name="retrieving-multiple-objects-in-batches"></a> 1.3 Retrieving Multiple Objects in Batches<span style="font-family: WenQuanYi Micro Hei;">分批次的检索多个目标</span></h4>
Sometimes you need to iterate<span style="font-family: DejaVu Sans;">反复重复</span>over a large set of records. For example to send a newsletter to all users, to export some data, etc.

<span style="font-family: DejaVu Sans;">有时候你需要遍历大量的记录，例如发送一条业务通讯给所有的用户，输出一些数据，等等。</span>

The following may seem very straight forward at first:

<span style="font-family: DejaVu Sans;">首先（通过）以下内容看起来会非常直观</span>

<tt><em># Very inefficient when users table has thousands of rows.</em></tt>

<tt><em>User.all.each do |user|</em></tt>

<tt> </tt><tt><em>NewsLetter.weekly_deliver(user)</em></tt>

<tt><em>end</em></tt>

But if the total number of rows in the table is very large, the above approach may vary from being under performant to just plain<span style="font-family: DejaVu Sans;">平原</span>impossible.

<span style="font-family: DejaVu Sans;">但是如果（数据）表单的行有非常大的量，上面的方法在执行（的时候）不可能性能（还是那么）平稳。</span>

This is because <tt>User.all.each</tt> makes Active Record fetch <em>the</em><em> </em><em>entire</em><em> </em><em>table</em>, build a model object per row, and keep the entire array in the memory. Sometimes that is just too many objects and demands too much memory.

<span style="font-family: DejaVu Sans;">这是因为</span><tt>User.all.each</tt><span style="font-family: DejaVu Sans;"><tt>使得</tt><tt></tt></span><tt>Active</tt><tt> </tt><tt>Record</tt><span style="font-family: DejaVu Sans;"><tt>获取整个表单，给每一行数据创建一个</tt></span><tt>object</tt><tt> </tt><tt>model</tt><span style="font-family: DejaVu Sans;"><tt>，并且保留整个数组在内存中。有时会有太多的对象并且需要太多的内存。</tt></span>

&nbsp;

<strong>Configuring</strong><strong> </strong><strong>the</strong><strong> </strong><strong>batch</strong><strong> </strong><strong>size<span style="font-family: DejaVu Sans;">配置批次大小</span></strong>

Behind the scenes <tt>find_each</tt> fetches rows in batches of <tt>1000</tt> and yields them one by one. The size of the underlying batches is configurable via the <tt>:batch_size</tt> option.<span style="font-family: DejaVu Sans;">在使用</span><tt>find_each</tt><span style="font-family: DejaVu Sans;"><tt>获取</tt></span><tt>1000</tt><span style="font-family: DejaVu Sans;"><tt>次记录行并且一个接一个的</tt></span><tt>yield</tt><span style="font-family: DejaVu Sans;"><tt>它们的情况中。（确定）下面（查找）批次的大小是通过配置</tt></span><tt>:batch_size</tt><span style="font-family: DejaVu Sans;"><tt>选项。</tt></span>

To fetch <tt>User</tt> records in batch size of <tt>5000</tt>:

<tt><em>User.find_each(:batch_size =&gt; 5000) do |user|</em></tt>

<tt> </tt><tt><em>NewsLetter.weekly_deliver(user)</em></tt>

<tt><em>end</em></tt>

&nbsp;

<strong>Starting</strong><strong> </strong><strong>batch</strong><strong> </strong><strong>find</strong><strong> </strong><strong>from</strong><strong> </strong><strong>a</strong><strong> </strong><strong>specific</strong><strong> </strong><strong>primary</strong><strong> </strong><strong>key<span style="font-family: DejaVu Sans;">通过一个指定的主键开始批次查找</span></strong>

Records are fetched in ascending order on the primary key, which must be an integer. The <tt>:start</tt> option allows you to configure the first ID of the sequence if the lowest is not the one you need. This may be useful for example to be able to resume an interrupted batch process if it saves the last processed ID as a checkpoint.<span style="font-family: DejaVu Sans;">这会非常有用比如能够减少因为设置最后处理的</span>ID<span style="font-family: DejaVu Sans;">作为</span>checkpoint<span style="font-family: DejaVu Sans;">引起的中断。</span>

<span style="font-family: DejaVu Sans;">（这里）是按照主键值的升序排列获取记录的，主键值必须是整数。</span><tt>:start</tt><span style="font-family: DejaVu Sans;"><tt>选项允许你配置序列的开始</tt></span><tt>ID</tt><span style="font-family: DejaVu Sans;"><tt>如果排序最低的（记录）不是你需要的。</tt></span>

To send newsletters only to users with the primary key starting from <tt>2000</tt>:

User.find_each(:batch_size =&gt; 5000, :start =&gt; 2000) do |user|

NewsLetter.weekly_deliver(user)

end

<strong>Additional</strong><strong> </strong><strong>options<span style="font-family: DejaVu Sans;">其他（附加）选项</span></strong>

<tt>find_each</tt> accepts the same options as the regular <tt>find</tt> method. However, <tt>:order</tt> and <tt>:limit</tt> are needed internally and hence not allowed to be passed explicitly.

<tt>find_each</tt><span style="font-family: DejaVu Sans;"><tt>接受和正则</tt></span><tt>find</tt><span style="font-family: DejaVu Sans;"><tt>方法相同的选项。</tt></span>
<h5><a name="find_in_batches"></a>1.3.2 <tt>find_in_batches</tt></h5>
You can also work by chunks instead of row by row using <tt>find_in_batches</tt>. This method is analogous to <tt>find_each</tt>, but it yields arrays of models instead:

<span style="font-family: DejaVu Sans;"><tt>通过使用</tt></span><tt>find_in_batches</tt><span style="font-family: DejaVu Sans;">你也可以用</span>chunks<span style="font-family: DejaVu Sans;"><span style="font-family: Liberation Serif,Times New Roman,serif;">替代数据行。这个方法类似于 </span></span><tt>find_each</tt><span style="font-family: DejaVu Sans;"><tt>，但是作为替代它（会输出）到一个数组区域：</tt></span>

<tt><em># Works in chunks of 1000 invoices at a time.</em></tt>

<tt><em>Invoice.find_in_batches(:include =&gt; :invoice_lines) do |invoices|</em></tt>

<tt> </tt><tt><em>export.add_invoices(invoices)</em></tt>

<tt><em>end</em></tt>

The above will yield the supplied block with <tt>1000</tt> invoices every time.<span style="font-family: DejaVu Sans;">上面的语句每次会提供给语句</span>1000invoices<span style="font-family: DejaVu Sans;">。</span>
<h3><a name="conditions"></a>2 Conditions<span style="font-family: WenQuanYi Micro Hei;">条件</span></h3>
The <tt>where</tt> method allows you to specify conditions to limit the records returned, representing the <tt>WHERE</tt>-part of the SQL statement. Conditions can either be specified as a string, array, or hash.

where<span style="font-family: DejaVu Sans;">方法允许你指定条件限制记录返回（的内容），表示</span>SQL<span style="font-family: DejaVu Sans;">部分的</span>WHERE<span style="font-family: DejaVu Sans;">部分。条件可以指定为一个字符串，数组，或者</span>hash<span style="font-family: DejaVu Sans;">（字典）。</span>
<h4><a name="pure-string-conditions"></a>2.1 Pure String Conditions<span style="font-family: WenQuanYi Micro Hei;">纯字符串条件</span></h4>
If you’d like to add conditions to your find, you could just specify them in there, just like <tt>Client.where("orders_count</tt><tt> </tt><tt>=</tt><tt> </tt><tt>'2'")</tt>. This will find all clients <strong>where</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>orders_count</strong></tt><strong> </strong><strong>field</strong><strong>’</strong><strong>s</strong><strong> </strong><strong>value</strong><strong> </strong><strong>is</strong><strong> </strong><strong>2</strong>.

<strong>Building</strong><strong> </strong><strong>your</strong><strong> </strong><strong>own</strong><strong> </strong><strong>conditions</strong><strong> </strong><strong>as</strong><strong> </strong><strong>pure</strong><strong> </strong><strong>strings</strong><strong> </strong><strong>can</strong><strong> </strong><strong>leave</strong><strong> </strong><strong>you</strong><strong> </strong><strong>vulnerable</strong><span style="font-family: DejaVu Sans;"><strong>脆弱</strong></span><strong>to</strong><strong> </strong><strong>SQL</strong><strong> </strong><strong>injection</strong><span style="font-family: DejaVu Sans;"><strong>注入</strong></span><strong>exploits</strong><span style="font-family: DejaVu Sans;"><strong>漏洞</strong></span><strong>.</strong> For example, <tt>Client.where("first_name</tt><tt> </tt><tt>LIKE</tt><tt> </tt><tt>'%#{params[:first_name]}%'")</tt> is not safe. See the next section for the preferred way to handle conditions using an array.
<h4><a name="array-conditions"></a>2.2 Array Conditions</h4>
Now what if that number could vary, say as an argument from somewhere? The find then becomes something like:

<span style="font-family: DejaVu Sans;">现在，如果这个数字可能会有所不同，（比如说）作为某个地方的一个参数？查找会变成如下：</span>

<tt><span style="color: #000000;"><span style="font-size: small;"><em>Client.where("orders_count = ?", params[:orders])</em></span></span></tt>

Active Record will go through the first element in the conditions value and any additional elements will replace the question marks <tt>(?)</tt> in the first element.

Active Record<span style="font-family: DejaVu Sans;">将会在第一个元素中表示条件（语句）并且其他元素取代第一个元素中的问号。</span>

Or if you want to specify two conditions, you can do it like:

<tt><em>Client.where("orders_count = ? AND locked = ?", params[:orders], false)</em></tt>

In this example, the first question mark will be replaced with the value in <tt>params[:orders]</tt> and the second will be replaced with the SQL representation of <tt>false</tt>, which depends on the adapter.

The reason for doing code like:

<tt><em>Client.where("orders_count = ?", params[:orders])</em></tt>

instead of:

<tt><em>Client.where("orders_count = #{params[:orders]}")</em></tt>

is because of argument safety. Putting the variable directly into the conditions string will pass the variable to the database <strong>as-is</strong>. This means that it will be an unescaped variable directly from a user who may have malicious intent. If you do this, you put your entire database at risk because once a user finds out he or she can exploit your database they can do just about anything to it. Never ever put your arguments directly inside the conditions string.

<span style="font-family: DejaVu Sans;">（这里）是基于参数安全（考虑）。将变量直接放入条件字符串将会（原封不动的传送变量）到数据库。他的意思是这个参数有可能将会是以一个来自用户的恶意的非转义的变量。如果你这样做，你就把整个数据库放在了风险之中，因为一旦有用户找到他们或者它可以利用（漏洞）对你数据库做任何操作。永远不要直接把你的参数放在条件字符串中。</span>

For more information on the dangers of SQL injection, see the <a href="http://guides.rubyonrails.org/security.html#sql-injection"><span style="color: #000080;"><span style="text-decoration: underline;">Ruby</span></span><span style="color: #000080;"><span style="text-decoration: underline;">on</span></span><span style="color: #000080;"><span style="text-decoration: underline;">Rails</span></span><span style="color: #000080;"><span style="text-decoration: underline;">Security</span></span><span style="color: #000080;"><span style="text-decoration: underline;">Guide</span></span></a>.
<h5><a name="placeholder-conditions"></a>2.2.1 Placeholder Conditions<span style="font-family: WenQuanYi Micro Hei;">条件（参数）占位符</span></h5>
Similar to the <tt>(?)</tt> replacement style of params, you can also specify keys/values hash in your array conditions:<span style="font-family: DejaVu Sans;">最简单的是使用（？）替代参数的形式，你也可以指定</span>keys/values hash<span style="font-family: DejaVu Sans;">在你的条件语句数组中：</span>

<tt><em>Client.where("created_at &gt;= :start_date AND created_at &lt;= :end_date",</em></tt>

<tt> </tt><tt><em>{:start_date =&gt; params[:start_date], :end_date =&gt; params[:end_date]})</em></tt>

This makes for clearer readability if you have a large number of variable conditions.<span style="font-family: DejaVu Sans;">当你有大量的条件变量时这样表示更加简洁和可读性更好。</span>
<h5><a name="array-range_conditions"></a>2.2.2 Range Conditions<span style="font-family: WenQuanYi Micro Hei;">范围条件</span></h5>
If you’re looking for a range inside of a table (for example, users created in a certain timeframe) you can use the conditions option coupled with the <tt>IN</tt> SQL statement for this. If you had two dates coming in from a controller you could do something like this to look for a range:

<span style="font-family: DejaVu Sans;">如果你正在一个表中限定一个条件范围查找（例如，用户限定在一定的时间表中创建）你可以使用条件选项加上（这个参数的）</span>IN SQL<span style="font-family: DejaVu Sans;">声明。如果有来自一个</span>controller<span style="font-family: DejaVu Sans;">的两个日期，你可以做些事情查找一个范围：</span>

<tt><em>Client.where(:created_at =&gt; (params[:start_date].to_date)..(params[:end_date].to_date))</em></tt>

<tt><em>This query will generate something similar to the following SQL:</em></tt>

<tt><em>SELECT "clients".* FROM "clients" WHERE ("clients"."created_at" BETWEEN '2010-09-29' AND '2010-11-30')</em></tt>
<h4><a name="hash-conditions"></a>2.3 Hash Conditions Hash<span style="font-family: WenQuanYi Micro Hei;">字典条件</span></h4>
Active Record also allows you to pass in hash conditions which can increase the readability of your conditions syntax. With hash conditions, you pass in a hash with keys of the fields you want conditionalised and the values of how you want to conditionalise them:

Active Record<span style="font-family: DejaVu Sans;">同样也允许你通过</span>hash<span style="font-family: DejaVu Sans;">条件来增加你条件语句的可读性。在</span>hash<span style="font-family: DejaVu Sans;">条件，以键作为你的条件化参数并且相应的值则是具体的条件限制。</span>

Only equality, range and subset checking are possible with Hash conditions.
<h5><a name="equality-conditions"></a>2.3.1 Equality Conditions<span style="font-family: WenQuanYi Micro Hei;">等同条件</span></h5>
<tt><em>Client.where(:locked =&gt; true)</em></tt>

<strong>The</strong><strong> </strong><strong>field</strong><strong> </strong><strong>name</strong><strong> </strong><strong>can</strong><strong> </strong><strong>also</strong><strong> </strong><strong>be</strong><strong> </strong><strong>a</strong><strong> </strong><strong>string:</strong>

<tt><em>Client.where('locked' =&gt; true)</em></tt>
<h5><a name="hash-range_conditions"></a>2.3.2 Range Conditions</h5>
The good thing about this is that we can pass in a range for our fields without it generating a large query as shown in the preamble of this section.

<tt><em>Client.where(:created_at =&gt; (Time.now.midnight – 1.day)..Time.now.midnight)</em></tt>

This will find all clients created yesterday by using a <tt>BETWEEN</tt> SQL statement:

<span style="font-family: DejaVu Sans;">这将会通过使用一个</span><tt>BETWEEN</tt> SQL<span style="font-family: DejaVu Sans;">声明查找</span>client<span style="font-family: DejaVu Sans;">中昨天创建的记录。</span>

<tt><em>SELECT * FROM clients WHERE (clients.created_at BETWEEN '2008-12-21 00:00:00' AND '2008-12-22 00:00:00')</em></tt>

This demonstrates a shorter syntax for the examples in <a href="http://guides.rubyonrails.org/active_record_querying.html#array-conditions"><span style="color: #000080;"><span style="text-decoration: underline;">Array</span></span><span style="color: #000080;"><span style="text-decoration: underline;">Conditions</span></span></a>
<h5><a name="subset-conditions"></a>2.3.3 Subset Conditions<span style="font-family: WenQuanYi Micro Hei;">子集条件</span></h5>
If you want to find records using the <tt>IN</tt> expression you can pass an array to the conditions hash:

<span style="font-family: DejaVu Sans;">如果想使用</span>IN<span style="font-family: DejaVu Sans;">表达式查找记录你可以在条件</span>hash<span style="font-family: DejaVu Sans;">字典中加上一个数组：</span>

<tt><em>Client.where(:orders_count =&gt; [1,3,5])</em></tt>

This code will generate SQL like this:

<tt><em>SELECT * FROM clients WHERE (clients.orders_count IN (1,3,5))</em></tt>
<h3><a name="ordering"></a>3 Ordering<span style="font-family: WenQuanYi Micro Hei;">排序</span></h3>
To retrieve records from the database in a specific order, you can use the <tt><strong>order</strong></tt> method.

<span style="font-family: DejaVu Sans;">如果你想检索记录从数据库中并且以一种指定的方式排序，你可以使用</span>order<span style="font-family: DejaVu Sans;">方法。</span>

For example, if you’re getting a set of records and want to order them in ascending order by the <tt>created_at</tt> field in your table:

<tt><em>Client.order("created_at")</em></tt>

<tt><em>You could specify ASC or DESC as well:</em></tt>

<tt><em>Client.order("created_at DESC")</em></tt>

<tt><em># OR</em></tt>

<tt><em>Client.order("created_at ASC")</em></tt>

Or ordering by multiple fields:<span style="font-family: DejaVu Sans;">或者按照多个</span>fields<span style="font-family: DejaVu Sans;">排序：</span>

<tt><em>Client.order("orders_count ASC, created_at DESC")</em></tt>
<h3><a name="selecting-specific-fields"></a>4 Selecting Specific Fields</h3>
By default, <tt>Model.find</tt> selects all the fields from the result set using <tt>select</tt><tt> </tt><tt>*</tt>.

<span style="font-family: DejaVu Sans;">默认情况中，</span><tt>Model.find</tt><span style="font-family: DejaVu Sans;"><tt>会选择所有的</tt></span><tt>fields</tt><span style="font-family: DejaVu Sans;"><tt>作为结果并且（对数据库执行）</tt></span><tt>select</tt><tt> </tt><tt>*</tt><span style="font-family: DejaVu Sans;"><tt>。</tt></span>

To select only a subset of fields from the result set, you can specify the subset via the <tt>select</tt> method.

<span style="font-family: DejaVu Sans;">要想只选中</span>fields<span style="font-family: DejaVu Sans;">中的一个子集作为结果，你可以通过</span>select<span style="font-family: DejaVu Sans;">方法指定子集。</span>

<span style="color: #800000;">If</span><span style="color: #800000;">the</span><tt><span style="color: #800000;">select</span></tt><span style="color: #800000;">method</span><span style="color: #800000;">is</span><span style="color: #800000;">used,</span><span style="color: #800000;">all</span><span style="color: #800000;">the</span><span style="color: #800000;">returning</span><span style="color: #800000;">objects</span><span style="color: #800000;">will</span><span style="color: #800000;">be</span><a href="http://guides.rubyonrails.org/active_record_querying.html#readonly-objects"><span style="color: #000080;"><span style="text-decoration: underline;">read</span></span><span style="color: #000080;"><span style="text-decoration: underline;">only</span></span></a><span style="color: #800000;">.</span>

For example, to select only <tt>viewable_by</tt> and <tt>locked</tt> columns:

<tt><em>Client.select("viewable_by, locked")</em></tt>

The SQL query used by this find call will be somewhat like:

<tt><em>SELECT viewable_by, locked FROM clients</em></tt>

Be careful because this also means you’re initializing a model object with only the fields that you’ve selected. <strong>If</strong><strong> </strong><strong>you</strong><strong> </strong><strong>attempt</strong><strong> </strong><strong>to</strong><strong> </strong><strong>access</strong><strong> </strong><strong>a</strong><strong> </strong><strong>field</strong><strong> </strong><strong>that</strong><strong> </strong><strong>is</strong><strong> </strong><strong>not</strong><strong> </strong><strong>in</strong><strong> </strong><strong>the</strong><strong> </strong><strong>initialized</strong><strong> </strong><strong>record</strong><strong> </strong><strong>you</strong><strong>’</strong><strong>ll</strong><strong> </strong><strong>receive</strong>:

ActiveModel::MissingAttributeError: missing attribute: &lt;attribute&gt;

<strong>Where</strong><strong> </strong><tt><strong>&lt;attribute&gt;</strong></tt><strong> </strong><strong>is</strong><strong> </strong><strong>the</strong><strong> </strong><strong>attribute</strong><strong> </strong><strong>you</strong><strong> </strong><strong>asked</strong><strong> </strong><strong>for.</strong> The <tt>id</tt> method will not raise the <tt>ActiveRecord::MissingAttributeError</tt>, so just be careful when working with associations because they need the <tt>id</tt> method to function properly.

You can also call SQL functions within the select option. For example, if you would like to only grab a single record per unique value in a certain field by using the <tt>DISTINCT</tt> function you can do it like this:

<tt><em>Client.select("DISTINCT(name)")</em></tt>

&nbsp;
<h3><a name="limit-and-offset"></a>5 Limit and Offset</h3>
To apply <tt>LIMIT</tt> to the SQL fired by the <tt>Model.find</tt>, <strong>you</strong><strong> </strong><strong>can</strong><strong> </strong><strong>specify</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>LIMIT</strong></tt><strong> </strong><strong>using</strong><strong> </strong><tt><strong>limit</strong></tt><strong> </strong><strong>and</strong><strong> </strong><tt><strong>offset</strong></tt><strong> </strong><strong>methods</strong><strong> </strong><strong>on</strong><strong> </strong><strong>the</strong><strong> </strong><strong>relation.</strong>

You can use <tt>limit</tt> to specify the number of records to be retrieved, and use <tt>offset</tt> to specify the number of records to skip before starting to return the records. For example

<tt><em>Client.limit(5)</em></tt>

will return a maximum of 5 clients and because it specifies no offset it will return the first 5 in the table. The SQL it executes looks like this:

<tt><em>SELECT * FROM clients LIMIT 5</em></tt>

Adding <tt>offset</tt> to that

<tt><em>Client.limit(5).offset(30)</em></tt>

will return instead a maximum of 5 clients beginning with the 31st. The SQL looks like:

<span style="font-family: DejaVu Sans;">将会返回最大</span>5<span style="font-family: DejaVu Sans;">个</span>clients<span style="font-family: DejaVu Sans;">并且从第</span>31<span style="font-family: DejaVu Sans;">个开始。这个</span>SQL<span style="font-family: DejaVu Sans;">看起来是：</span>

<tt><em>SELECT * FROM clients LIMIT 5 OFFSET 30</em></tt>
<h3><a name="group"></a>6 Group</h3>
To apply a <tt>GROUP</tt><tt> </tt><tt>BY</tt> clause to the SQL fired by the finder, you can specify the <tt>group</tt> method on the find.

For example, if you want to find a collection of the dates orders were created on:

<tt><em>Order.select("date(created_at) as ordered_date, sum(price) as total_price").group("date(created_at)")</em></tt>

And this will give you a single <tt>Order</tt><span style="font-family: DejaVu Sans;"><tt>生产者</tt></span>object for each date where there are orders in the database.

The SQL that would be executed would be something like this:

<tt><em>SELECT date(created_at) as ordered_date, sum(price) as total_price FROM orders GROUP BY date(created_at)</em></tt>
<h3><a name="having"></a>7 Having</h3>
SQL uses the <tt>HAVING</tt> clause<span style="font-family: DejaVu Sans;">短语</span>to specify conditions on the <tt>GROUP</tt><tt> </tt><tt>BY</tt> fields. You can add the <tt>HAVING</tt> clause to the SQL fired by the <tt>Model.find</tt> by adding the <tt>:having</tt> option to the find.

For example:

<tt><em>Order.select("date(created_at) as ordered_date, sum(price) as total_price").group("date(created_at)").having("sum(price) &gt; ?", 100)</em></tt>

The SQL that would be executed would be something like this:

<tt><em>SELECT date(created_at) as ordered_date, sum(price) as total_price FROM orders GROUP BY date(created_at) HAVING sum(price) &gt; 100</em></tt>

This will return single order objects for each day, but only those that are ordered more than $100 in a day.
<h3><a name="overriding-conditions"></a>8 Overriding Conditions<span style="font-family: WenQuanYi Micro Hei;">覆盖条件</span></h3>
<h4><a name="except"></a>8.1 <tt>except</tt><span style="font-family: WenQuanYi Micro Hei;"><tt>排除</tt></span></h4>
You can specify certain conditions to be excepted by using the <tt>except</tt> method. For example:

<tt><em>Post.where('id &gt; 10').limit(20).order('id asc').except(:order)#</em></tt><span style="font-family: DejaVu Sans;"><tt><em>取消排序</em></tt></span>

<tt><em>The SQL that would be executed:</em></tt>

<tt><em>SELECT * FROM posts WHERE id &gt; 10 LIMIT 20</em></tt>

<tt><em>irb(main):006:0&gt; Post.where('id &gt; 1').limit(20).order('id desc')</em></tt>

<tt> </tt><tt><em>Post Load (0.4ms) SELECT "posts".* FROM "posts" WHERE (id &gt; 1) ORDER BY id desc LIMIT 20</em></tt>

<tt><em>=&gt; [#&lt;Post id: 4, name: "2134", title: "2134", content: "21343242134", created_at: "2011-11-09 10:25:38", updated_at: "2011-11-09 10:25:38"&gt;, #&lt;Post id: 2, name: "jhjguxin", title: "test console", content: "A new post to test console", created_at: "2011-11-05 15:55:17", updated_at: "2011-11-05 15:55:17"&gt;]</em></tt>

<tt><em>irb(main):007:0&gt; Post.where('id &gt; 1').limit(20).order('id desc').except(:order)</em></tt>

<tt> </tt><tt><em>Post Load (0.4ms) SELECT "posts".* FROM "posts" WHERE (id &gt; 1) LIMIT 20</em></tt>

<tt><em>=&gt; [#&lt;Post id: 2, name: "jhjguxin", title: "test console", content: "A new post to test console", created_at: "2011-11-05 15:55:17", updated_at: "2011-11-05 15:55:17"&gt;, #&lt;Post id: 4, name: "2134", title: "2134", content: "21343242134", created_at: "2011-11-09 10:25:38", updated_at: "2011-11-09 10:25:38"&gt;]</em></tt>
<h4><a name="only"></a>8.2 <tt>only</tt></h4>
You can also override conditions using the <tt>only</tt> method. For example:

<tt><em>Post.where('id &gt; 10').limit(20).order('id desc').only(:order, :where)</em></tt>

The SQL that would be executed:

<tt><em>SELECT * FROM posts WHERE id &gt; 10 ORDER BY id DESC</em></tt>
<h4><a name="reorder"></a>8.3 <tt>reorder</tt></h4>
The <tt>reorder</tt> method overrides the default scope order. For example:

<tt><em>class Post &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>..</em></tt>

<tt> </tt><tt><em>..</em></tt>

<tt> </tt><tt><em>has_many :comments, :order =&gt; 'posted_at DESC'</em></tt>

<tt><em>end</em></tt>

&nbsp;

<tt><em>Post.find(10).comments.reorder('name')</em></tt>

The SQL that would be executed:

<tt><em>SELECT * FROM posts WHERE id = 10 ORDER BY name</em></tt>

In case the <tt>reorder</tt> clause is not used, the SQL executed would be:

<tt><em>SELECT * FROM posts WHERE id = 10 ORDER BY posted_at DESC</em></tt>

If no ordering clause is specified in the query, the <tt>reverse_order</tt> orders by the primary key in reverse<span style="font-family: DejaVu Sans;">撤销</span>order.

<tt><em>Client.where("orders_count &gt; 10").reverse_order</em></tt>

The SQL that would be executed:

<tt><em>SELECT * FROM clients WHERE orders_count &gt; 10 ORDER BY clients.id DESC</em></tt>

<strong>This</strong><strong> </strong><strong>method</strong><strong> </strong><strong>accepts</strong><strong> </strong><strong><strong>no</strong></strong><strong> </strong><strong>arguments.</strong>
<h3><a name="readonly-objects"></a>9 Readonly Objects</h3>
Active Record provides <tt>readonly</tt> method on a relation to explicitly disallow modification or deletion of any of the returned object. Any attempt to alter or destroy a readonly record will not succeed, raising an <tt>ActiveRecord::ReadOnlyRecord</tt> exception.

<tt><em>client = Client.readonly.first</em></tt>

<tt><em>client.visits += 1</em></tt>

<tt><em>client.save</em></tt>

<strong>As</strong><strong> </strong><tt><strong>client</strong></tt><strong> </strong><strong>is</strong><strong> </strong><strong>explicitly</strong><strong> </strong><strong>set</strong><strong> </strong><strong>to</strong><strong> </strong><strong>be</strong><strong> </strong><strong>a</strong><strong> </strong><strong>readonly</strong><strong> </strong><strong>object</strong>, the above code will raise an <tt>ActiveRecord::ReadOnlyRecord</tt> exception when<strong> </strong><strong>calling</strong><strong> </strong><tt><strong>client.save</strong></tt><strong> </strong><strong>with</strong><strong> </strong><strong>an</strong><strong> </strong><strong>updated</strong><strong> </strong><strong>value</strong><strong> </strong><strong>of</strong><strong> </strong><em><strong>visits</strong></em>.
<h3><a name="locking-records-for-update"></a>10 Locking Records for Update</h3>
Locking is helpful for preventing race conditions when updating records in the database and ensuring atomic updates.

Active Record provides two locking mechanisms:
<ul>
	<li>Optimistic<span style="font-family: DejaVu Sans;">乐观</span>Locking</li>
	<li>Pessimistic<span style="font-family: DejaVu Sans;">悲观</span>Locking</li>
</ul>
<h4><a name="optimistic-locking"></a>10.1 Optimistic Locking</h4>
<strong>Optimistic</strong><strong> </strong><strong>locking</strong><strong> </strong><strong>allows</strong><strong> </strong><strong>multiple</strong><strong> </strong><strong>users</strong><strong> </strong><strong>to</strong><strong> </strong><strong>access</strong><strong> </strong><strong>the</strong><strong> </strong><strong>same</strong><strong> </strong><strong>record</strong><strong> </strong><strong>for</strong><strong> </strong><strong>edits</strong>, and assumes<span style="font-family: DejaVu Sans;">假定</span>a minimum of conflicts with the data. It does this by <strong>checking</strong><strong> </strong><strong>whether</strong><strong> </strong><strong>another</strong><strong> </strong><strong>process</strong><strong> </strong><strong>has</strong><strong> </strong><strong>made</strong><strong> </strong><strong>changes</strong> to a record <strong>since</strong><strong> </strong><strong>it</strong><strong> </strong><strong>was</strong><strong> </strong><strong>opened</strong>. An <tt>ActiveRecord::StaleObjectError</tt> exception is thrown if that has occurred<span style="font-family: DejaVu Sans;">发生</span>and the update is ignored.

<strong>Optimistic</strong><strong> </strong><strong>locking</strong><strong> </strong><strong>column</strong>

<strong>In</strong><strong> </strong><strong>order</strong><strong> </strong><strong>to</strong><strong> </strong><strong>use</strong><strong> </strong><strong>optimistic</strong><strong> </strong><strong>locking</strong>, the table <strong>needs</strong><strong> </strong><strong>to</strong><strong> </strong><strong>have</strong><strong> </strong><strong>a</strong><strong> </strong><strong>column</strong><strong> </strong><strong>called</strong><strong> </strong><tt><strong>lock_version</strong></tt>. Each time the record is <strong>updated</strong>, Active Record increments the <tt><strong>lock_version</strong></tt> column. If an update request is made with a lower value in the <tt>lock_version</tt> field than is currently in the <tt>lock_version</tt> column in the database, the update request will fail with an <tt>ActiveRecord::StaleObjectError</tt>. Example:

<tt><em>c1 = Client.find(1)</em></tt>

<tt><em>c2 = Client.find(1)</em></tt>

&nbsp;

<tt><em>c1.first_name = "Michael"</em></tt>

<tt><em>c1.save</em></tt>

&nbsp;

<tt><em>c2.name = "should fail"</em></tt>

<tt><em>c2.save # Raises a ActiveRecord::StaleObjectError</em></tt>

You’re then responsible<span style="font-family: DejaVu Sans;">有责任</span>for dealing<span style="font-family: DejaVu Sans;">处理</span>with the conflict by rescuing the exception and either rolling back, merging, or otherwise apply the business logic needed to resolve the conflict.

<span style="color: #800000;">You</span><span style="color: #800000;">must</span><span style="color: #800000;">ensure</span><span style="color: #800000;">that</span><span style="color: #800000;">your</span><span style="color: #800000;">database</span><span style="color: #800000;">schema</span><span style="color: #800000;">defaults</span><span style="color: #800000;">the</span><tt><span style="color: #800000;">lock_version</span></tt><span style="color: #800000;">column</span><span style="color: #800000;">to</span><tt><span style="color: #800000;">0</span></tt><span style="color: #800000;">.</span>

This behavior can be turned off by setting <tt><strong>ActiveRecord::Base.lock_optimistically</strong></tt><tt><strong> </strong></tt><tt><strong>=</strong></tt><tt><strong> </strong></tt><tt><strong>false</strong></tt><strong>.</strong>

<strong>To</strong><strong> </strong><strong>override</strong><strong> </strong><strong>the</strong><strong> </strong><strong>name</strong><strong> </strong><strong>of</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>lock_version</strong></tt><strong> </strong><strong>column</strong>, <tt>ActiveRecord::Base</tt> provides a class method called <tt>set_locking_column</tt>:

<tt><em>class Client &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>set_locking_column :lock_client_column</em></tt>

<tt><em>end</em></tt>
<h4><a name="pessimistic-locking"></a>10.2 Pessimistic Locking</h4>
Pessimistic locking uses a locking mechanism provided by the underlying<span style="font-family: DejaVu Sans;">底层</span>database. Using <tt>lock</tt> when building a relation obtains an exclusive lock on the selected rows. Relations using <tt>lock</tt> are usually wrapped inside a transaction for preventing deadlock conditions.

<span style="font-family: DejaVu Sans;">在语句块的一个流程中使用</span>lock<span style="font-family: DejaVu Sans;">通常是为了防止死锁情况出现。</span>

For example:

<tt><em>Item.transaction do</em></tt>

<tt> </tt><tt><em>i = Item.lock.first</em></tt>

<tt> </tt><tt><em>i.name = 'Jones'</em></tt>

<tt> </tt><tt><em>i.save</em></tt>

<tt><em>end</em></tt>

The above session produces the following SQL for a MySQL backend:

You can also pass raw SQL to the <tt>lock</tt> method for allowing different types of locks. For example, MySQL has an expression called <tt>LOCK</tt><tt> </tt><tt>IN</tt><tt> </tt><tt>SHARE</tt><tt> </tt><tt>MODE</tt> where you can lock a record but still allow other queries to read it. To specify this expression just pass it in as the lock option:

<tt><em>SQL (0.2ms) BEGIN</em></tt>

<tt><em>Item Load (0.3ms) SELECT * FROM `items` LIMIT 1 FOR UPDATE</em></tt>

<tt><em>Item Update (0.4ms) UPDATE `items` SET `updated_at` = '2009-02-07 18:05:56', `name` = 'Jones' WHERE `id` = 1</em></tt>

<tt><em>SQL (0.8ms) COMMIT</em></tt>

You can also pass raw SQL to the <tt>lock</tt> method for allowing different types of locks. For example, MySQL has an expression called <tt>LOCK</tt><tt> </tt><tt>IN</tt><tt> </tt><tt>SHARE</tt><tt> </tt><tt>MODE</tt> <strong>where</strong><strong> </strong><strong>you</strong><strong> </strong><strong>can</strong><strong> </strong><strong>lock</strong><strong> </strong><strong>a</strong><strong> </strong><strong>record</strong><strong> </strong><strong>but</strong><strong> </strong><strong>still</strong><strong> </strong><strong>allow</strong><strong> </strong><strong>other</strong><strong> </strong><strong>queries</strong><strong> </strong><strong>to</strong><strong> </strong><strong>read</strong><strong> </strong><strong>it</strong>. To specify this expression just pass it in as the lock option:

<tt><em>Item.transaction do</em></tt>

<tt> </tt><tt><em>i = Item.lock("LOCK IN SHARE MODE").find(1)</em></tt>

<tt> </tt><tt><em>i.increment!(:views)</em></tt>

<tt><em>end</em></tt>
<h3><a name="joining-tables"></a>11 Joining Tables</h3>
Active Record provides a finder method called <tt>joins</tt> for specifying <tt>JOIN</tt> clauses<span style="font-family: DejaVu Sans;">短语</span>on the resulting SQL. There are multiple ways to use the <tt>joins</tt> method.
<h4><a name="using-a-string-sql-fragment"></a>11.1 Using a String SQL Fragment</h4>
You can just supply the raw SQL specifying the <tt><strong>JOIN</strong></tt><strong> </strong>clause to <tt>joins</tt>:

<tt><em>Client.joins('LEFT OUTER JOIN addresses ON addresses.client_id = clients.id')</em></tt>

This will result in the following SQL:

<tt><em>SELECT clients.* FROM clients LEFT OUTER JOIN addresses ON addresses.client_id = clients.id</em></tt>
<h4><a name="using-array-hash-of-named-associations"></a> 11.2 Using Array/Hash of Named Associations</h4>
<span style="color: #800000;">This</span><span style="color: #800000;">method</span><span style="color: #800000;">only</span><span style="color: #800000;">works</span><span style="color: #800000;">with</span><tt><span style="color: #800000;">INNER</span></tt><tt></tt><tt><span style="color: #800000;">JOIN</span></tt><span style="color: #800000;">.</span>

Active Record lets you use the names of the <span style="color: #000080;"><span style="text-decoration: underline;"><a href="http://guides.rubyonrails.org/association_basics.html">associations</a></span></span> defined on the model as a shortcut for specifying <tt>JOIN</tt> clause for those associations when using the <tt>joins</tt> method.

For example, consider the following <tt>Category</tt>, <tt>Post</tt>, <tt>Comments</tt> and <tt>Guest</tt> models:

<tt><em>class Category &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>has_many :posts</em></tt>

<tt><em>end</em></tt>

&nbsp;

<tt><em>class Post &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>belongs_to :category</em></tt>

<tt> </tt><tt><em>has_many :comments</em></tt>

<tt> </tt><tt><em>has_many :tags</em></tt>

<tt><em>end</em></tt>

&nbsp;

<tt><em>class Comment &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>belongs_to :post</em></tt>

<tt> </tt><tt><em>has_one :guest</em></tt>

<tt><em>end</em></tt>

&nbsp;

<tt><em>class Guest &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>belongs_to :comment</em></tt>

<tt><em>end</em></tt>

&nbsp;

<tt><em>class Tag &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>belongs_to :post</em></tt>

<tt><em>end</em></tt>

Now all of the following will produce the expected join queries using <tt>INNER</tt><tt> </tt><tt>JOIN</tt>:
<h5><a name="joining-a-single-association"></a>11.2.1 Joining a Single Association</h5>
<tt><em>Category.joins(:posts)</em></tt>

This produces:

<tt><em>SELECT categories.* FROM categories</em></tt>

<tt> </tt><tt><em>INNER JOIN posts ON posts.category_id = categories.id</em></tt>

Or, in English: “return a Category object for <strong>all</strong><strong> </strong><strong>categories</strong><strong> </strong><strong>with</strong><strong> </strong><strong>posts</strong>”. Note that you will see duplicate categories if more than one post has the same category. If you want unique<span style="font-family: DejaVu Sans;">唯一的</span>categories, you can use Category.joins(:post).select(“distinct(categories.id)”).

<span style="font-family: DejaVu Sans;">或者，用英语说：<span style="font-family: Liberation Serif,Times New Roman,serif;">“</span>返回所有</span>posts<span style="font-family: DejaVu Sans;">的所有</span>categories<span style="font-family: DejaVu Sans;">到</span>Category<span style="font-family: DejaVu Sans;">对象<span style="font-family: Liberation Serif,Times New Roman,serif;">”</span>。请注意，如果超过一个</span>post<span style="font-family: DejaVu Sans;">具有相同的</span>categories<span style="font-family: DejaVu Sans;">，你会看到重复的</span>categories<span style="font-family: DejaVu Sans;">。如果你想要唯一的</span>categories<span style="font-family: DejaVu Sans;">，你可以使用</span>Category.joins(:post).select(“distinct(categories.id)”)<span style="font-family: DejaVu Sans;">。</span>
<h5><a name="joining-multiple-associations"></a>11.2.2 Joining Multiple Associations</h5>
<tt><em>Post.joins(:category, :comments)#</em></tt><span style="font-family: DejaVu Sans;"><tt><em>两个参数之间是与关系</em></tt></span>

This produces:

<tt><em>SELECT posts.* FROM posts</em></tt>

<tt> </tt><tt><em>INNER JOIN categories ON posts.category_id = categories.id</em></tt>

<tt> </tt><tt><em>INNER JOIN comments ON comments.post_id = posts.id</em></tt>

Or, in English: “return all posts that have a category and at<strong> </strong><strong>least</strong><strong> </strong><strong>one</strong><strong> </strong><strong>comment</strong>”. Note again that posts with multiple comments will show up multiple times.
<h5><a name="joining-nested-associations-single-level"></a> 11.2.3 Joining Nested Associations (Single Level)</h5>
&nbsp;

<tt><em>Post.joins(:comments =&gt; :guest)</em></tt>

This produces:

<tt><em>SELECT posts.* FROM posts</em></tt>

<tt> </tt><tt><em>INNER JOIN comments ON comments.post_id = posts.id</em></tt>

<tt> </tt><tt><em>INNER JOIN guests ON guests.comment_id = comments.id</em></tt>

Or, in English: “return all posts that have<strong> </strong><strong>a</strong><strong> </strong><strong>comment</strong><strong> </strong><strong>made</strong><strong> </strong><strong>by</strong><strong> </strong><strong>a</strong><strong> </strong><strong>guest</strong>.”
<h5><a name="joining-nested-associations-multiple-lev"></a> 11.2.4 Joining Nested Associations (Multiple Level)</h5>
<tt><em>Category.joins(:posts =&gt; [{:comments =&gt; :guest}, :tags])</em></tt>

This produces:

<tt><em>SELECT categories.* FROM categories</em></tt>

<tt> </tt><tt><em>INNER JOIN posts ON posts.category_id = categories.id</em></tt>

<tt> </tt><tt><em>INNER JOIN comments ON comments.post_id = posts.id</em></tt>

<tt> </tt><tt><em>INNER JOIN guests ON guests.comment_id = comments.id</em></tt>

<tt> </tt><tt><em>INNER JOIN tags ON tags.post_id = posts.id</em></tt>

<span style="font-family: DejaVu Sans;">返回</span>post<span style="font-family: DejaVu Sans;">的</span>category<span style="font-family: DejaVu Sans;">，并且这个</span>post<span style="font-family: DejaVu Sans;">至少有一个</span>commit<span style="font-family: DejaVu Sans;">是</span>guest<span style="font-family: DejaVu Sans;">提交而且这个</span>post<span style="font-family: DejaVu Sans;">至少有一个</span>tag<span style="font-family: DejaVu Sans;">。</span>
<h4><a name="specifying-conditions-on-the-joined-tabl"></a> 11.3 Specifying Conditions on the Joined Tables</h4>
You can specify conditions on the joined tables using the regular <span style="color: #000080;"><span style="text-decoration: underline;"><a href="http://guides.rubyonrails.org/active_record_querying.html#array-conditions">Array</a></span></span> and <span style="color: #000080;"><span style="text-decoration: underline;"><a href="http://guides.rubyonrails.org/active_record_querying.html#pure-string-conditions">String</a></span></span> conditions. <a href="http://guides.rubyonrails.org/active_record_querying.html#hash-conditions"><span style="color: #000080;"><span style="text-decoration: underline;">Hash</span></span><span style="color: #000080;"><span style="text-decoration: underline;">conditions</span></span></a> provides a special syntax for specifying conditions for the joined tables:

<span style="font-family: DejaVu Sans;">你可以在</span>joined tables <span style="font-family: DejaVu Sans;">的时候通过使用正则数组和字符串条件（表达式）来指定条件。</span>Hash<span style="font-family: DejaVu Sans;">条件提供了一个特殊的语法来指定</span>joined tables<span style="font-family: DejaVu Sans;">的条件：</span>

<tt><em>time_range = (Time.now.midnight - 1.day)..Time.now.midnight</em></tt>

<tt><em>Client.joins(:orders).where('orders.created_at' =&gt; time_range)</em></tt>

An alternative and cleaner syntax is to nest the hash conditions:<span style="font-family: DejaVu Sans;">一个更简洁的替代语法是将条件嵌入</span>Hash<span style="font-family: DejaVu Sans;">条件中。</span>

<tt><em>time_range = (Time.now.midnight - 1.day)..Time.now.midnight</em></tt>

<tt><em>Client.joins(:orders).where(:orders =&gt; {:created_at =&gt; time_range})</em></tt>

This will find all clients who have orders that were created yesterday, again using a <tt>BETWEEN</tt> SQL expression.<span style="font-family: DejaVu Sans;">这将会查找所有的</span>clients<span style="font-family: DejaVu Sans;">，它们都有</span>orders<span style="font-family: DejaVu Sans;">并且，</span>order<span style="font-family: DejaVu Sans;">在昨天创建，再一次使用</span>BETWEEN SQL<span style="font-family: DejaVu Sans;">语句。</span>
<h3><a name="eager-loading-associations"></a>12 Eager Loading Associations</h3>
Eager loading<span style="font-family: DejaVu Sans;">快速导入</span>is the mechanism for loading the associated records of the objects returned by <tt>Model.find</tt> using as few queries as possible.

Eager loading<span style="font-family: DejaVu Sans;">快速导入是一个导入记录的关系的机器，它通过</span><tt>Model.find</tt><span style="font-family: DejaVu Sans;"><tt>返回对象，并使用尽可能少的查询。</tt></span>

<strong>N</strong><strong> </strong><strong>+</strong><strong> </strong><strong>1</strong><strong> </strong><strong>queries</strong><strong> </strong><strong>problem</strong>

Consider the following code, which finds 10 clients and prints their postcodes:

<tt><em>clients = Client.limit(10)</em></tt>

&nbsp;

<tt><em>clients.each do |client|</em></tt>

<tt> </tt><tt><em>puts client.address.postcode</em></tt>

<tt><em>end</em></tt>

<strong>This</strong><strong> </strong><strong>code</strong><strong> </strong><strong>looks</strong><strong> </strong><strong>fine</strong><strong> </strong><strong>at</strong><strong> </strong><strong>the</strong><strong> </strong><strong>first</strong><strong> </strong><strong>sight</strong>. But the problem lies within the total number of queries executed. The above code executes 1 ( to find 10 clients ) + 10 ( one per each client to load the address ) = <strong>11</strong> queries in total.<span style="font-family: DejaVu Sans;">上面的代码执行一次（找到</span>10<span style="font-family: DejaVu Sans;">个</span>clients<span style="font-family: DejaVu Sans;">）</span>+10<span style="font-family: DejaVu Sans;">（每一个</span>client<span style="font-family: DejaVu Sans;">导入地址）</span>

<strong>Solution</strong><strong> </strong><strong>to</strong><strong> </strong><strong>N</strong><strong> </strong><strong>+</strong><strong> </strong><strong>1</strong><strong> </strong><strong>queries</strong><strong> </strong><strong>problem</strong>

Active Record lets you specify in advance all the associations that are going to be loaded. This is possible by specifying the <tt>includes</tt> method of the <tt>Model.find</tt> call. With <tt>includes</tt>, Active Record ensures that all of the specified associations are loaded using the minimum possible number of queries.

Revisiting the above case, we could rewrite <tt>Client.all</tt> to use eager load addresses:

<tt><em>clients = Client.includes(:address).limit(10)</em></tt>

&nbsp;

<tt><em>clients.each do |client|</em></tt>

<tt> </tt><tt><em>puts client.address.postcode</em></tt>

<tt><em>end</em></tt>

The above code will execute just <strong>2</strong> queries, as opposed to <strong>11</strong> queries in the previous case:

<span style="font-family: DejaVu Sans;">上面的代码将执行</span>2<span style="font-family: DejaVu Sans;">查询，而不是在以前的情况下，以</span>11<span style="font-family: DejaVu Sans;">查询：</span>

<tt><em>SELECT * FROM clients LIMIT 10</em></tt>

<tt><em>SELECT addresses.* FROM addresses</em></tt>

<tt> </tt><tt><em>WHERE (addresses.client_id IN (1,2,3,4,5,6,7,8,9,10))</em></tt>
<h4><a name="eager-loading-multiple-associations"></a> 12.1 Eager Loading Multiple Associations</h4>
Active Record lets you eager load any number of associations with a single <tt>Model.find</tt> call by using an array, hash, or a nested hash of array/hash with the <tt>includes</tt> method.

Active Record<span style="font-family: DejaVu Sans;">可以通过</span>includes<span style="font-family: DejaVu Sans;">方法加上数组，</span>hash<span style="font-family: DejaVu Sans;">或者使用数组和</span>hash<span style="font-family: DejaVu Sans;">嵌套的</span>hash<span style="font-family: DejaVu Sans;">字典调用单个</span><tt>Model.find</tt><span style="font-family: DejaVu Sans;"><tt>方法快速导入任何数目的关系。</tt></span>
<h5><a name="array-of-multiple-associations"></a>12.1.1 Array of Multiple Associations</h5>
<tt><em>Post.includes(:category, :comments)</em></tt>

This loads all the posts and the associated category and comments for each post.
<h5><a name="nested-associations-hash"></a>12.1.2 Nested Associations Hash</h5>
<tt><em>Category.includes(:posts =&gt; [{:comments =&gt; :guest}, :tags]).find(1)</em></tt>

This will find the <strong>category</strong><strong> </strong><strong>with</strong><strong> </strong><strong>id</strong><strong> </strong><strong>1</strong> and <strong>eager</strong><strong> </strong><strong>load</strong> all of the associated posts<span style="font-family: DejaVu Sans;">以及快速导入分类为</span>id<span style="font-family: DejaVu Sans;">为</span>1<span style="font-family: DejaVu Sans;">的</span>post, the associated posts’ tags and comments<span style="font-family: DejaVu Sans;">和</span>post<span style="font-family: DejaVu Sans;">的</span>tags<span style="font-family: DejaVu Sans;">和</span>comments, and every comment’s guest association<span style="font-family: DejaVu Sans;">并且每个</span>comments<span style="font-family: DejaVu Sans;">是由</span>guest<span style="font-family: DejaVu Sans;">创建的</span>.
<h4><a name="specifying-conditions-on-eager-loaded-as"></a> 12.2 Specifying Conditions on Eager Loaded Associations</h4>
Even though Active Record lets you specify conditions on the eager loaded associations just like <tt>joins</tt>, <strong>the</strong><strong> </strong><strong>recommended</strong><strong> </strong><strong>way</strong><strong> </strong><strong>is</strong><strong> </strong><strong>to</strong><strong> </strong><strong>use</strong><strong> </strong><span style="color: #000080;"><span style="text-decoration: underline;"><a href="http://guides.rubyonrails.org/active_record_querying.html#joining-tables"><strong>joins</strong></a></span></span><strong> </strong><strong>instead.</strong>

However if you must do this, you may use <tt>where</tt> as you would normally.

<tt><em>Post.includes(:comments).where("comments.visible", true)</em></tt>

This would generate a query which contains a <tt>LEFT</tt><tt> </tt><tt>OUTER</tt><tt> </tt><tt>JOIN</tt> whereas the <tt>joins</tt> method would generate one using the <tt>INNER</tt><tt> </tt><tt>JOIN</tt> function instead.

<tt><em>SELECT "posts"."id" AS t0_r0, ... "comments"."updated_at" AS t1_r5 FROM "posts" LEFT OUTER JOIN "comments" ON "comments"."post_id" = "posts"."id" WHERE (comments.visible = 1)</em></tt>

If there was no <tt>where</tt> condition, this would generate the normal set of two queries.

If, in the case of this <tt>includes</tt> query,<strong> </strong><strong>there</strong><strong> </strong><strong>were</strong><strong> </strong><strong>no</strong><strong> </strong><strong>comments</strong><strong> </strong><strong>for</strong><strong> </strong><strong>any</strong><strong> </strong><strong>posts</strong>, all the posts would <strong>still</strong><strong> </strong><strong>be</strong><strong> </strong><strong>loaded</strong>. <span style="color: #800000;">By</span><span style="color: #800000;">using</span><tt><span style="color: #800000;">joins</span></tt> (an INNER JOIN), the join conditions <strong>must</strong> match, <strong>otherwise</strong><strong> </strong><strong>no</strong><strong> </strong><strong>records</strong><strong> </strong><strong>will</strong><strong> </strong><strong>be</strong><strong> </strong><strong>returned.</strong>
<h3><a name="scopes"></a>13 Scopes<span style="font-family: WenQuanYi Micro Hei;">作用域</span></h3>
Scoping allows you to specify commonly-used ARel<span style="font-family: DejaVu Sans;">（</span>Arel is a relational algebra engine for Ruby

<span style="font-family: DejaVu Sans;">） </span>queries which can be referenced as method calls on the association objects or models. With these scopes, you can use every method previously covered such as <tt>where</tt>, <tt>joins</tt> and <tt>includes</tt>. All scope methods will return an <tt>ActiveRecord::Relation</tt> object which will allow for further methods (such as other scopes) to be called on it.

<span style="font-family: DejaVu Sans;">作用域允许你指定通常使用的</span>Arel<span style="font-family: DejaVu Sans;">（</span>Arel<span style="font-family: DejaVu Sans;">是一个用于</span>Ruby<span style="font-family: DejaVu Sans;">的关系代数引擎）查询它可以在对象或模型调用方法的时候被引用。通过这些作用域，你可以使用比如</span>where<span style="font-family: DejaVu Sans;">，</span>joins<span style="font-family: DejaVu Sans;">和</span>includes<span style="font-family: DejaVu Sans;">这些方法覆盖以前的（方法）。所有的</span>scope<span style="font-family: DejaVu Sans;">方法将会返回一个 </span><tt>ActiveRecord::Relation</tt><span style="font-family: DejaVu Sans;"><tt>对象它将允许一些以后的方法（例如其他的作用域）被其调用。</tt></span>

To define a simple scope, we use the <tt>scope</tt> method inside the class, passing the ARel query that we’d like run when this scope is called:

<tt><em>class Post &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>scope :published, where(:published =&gt; true)</em></tt>

<tt><em>end</em></tt>

<tt><em>Just like before, these methods are also chainable</em></tt><span style="font-family: DejaVu Sans;"><tt><em>可链式的</em></tt></span><tt><em>:</em></tt>

<tt><em>class Post &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>scope :published, where(:published =&gt; true).joins(:category)</em></tt>

<tt><em>end</em></tt>

Scopes are also chainable within scopes:<span style="font-family: DejaVu Sans;">作用域对作用域中也是可链式的：</span>

<tt><em>class Post &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>scope :published, where(:published =&gt; true)</em></tt>

<tt> </tt><tt><em>scope :published_and_commented, published.and(self.arel_table[:comments_count].gt(0))</em></tt>

<tt><em>end</em></tt>

To call this <tt>published</tt> scope we can call it on either the class:<span style="font-family: DejaVu Sans;">调用这个</span>published<span style="font-family: DejaVu Sans;">作用域我们可以在类中调用：</span>

<tt><em>Post.published =&gt; [published posts]</em></tt>

Or on an association consisting of <tt>Post</tt> objects:<span style="font-family: DejaVu Sans;">或者是在组成</span>Post<span style="font-family: DejaVu Sans;">对象的关系（成员）中调用：</span>

<tt><em>category = Category.first</em></tt>

<tt><em>category.posts.published =&gt; [published posts belonging to this category]</em></tt>
<h4><a name="working-with-times"></a>13.1 Working with times<span style="font-family: WenQuanYi Micro Hei;">工作中的时间（字段）</span></h4>
If you’re working with dates or times within scopes, due to how they are evaluated, you will need to use a lambda so that the scope is evaluated every time.

<span style="font-family: DejaVu Sans;">如果你在工作中遇到时间（字段）或者包含有时间（字段）的作用域，由于他们的求值方式，你将需要使用一个匿名函数来使得每次调用域都会计算时间的值。</span>

<tt><em>class Post &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>scope :last_week, lambda { where("created_at &lt; ?", Time.zone.now ) }</em></tt>

<tt><em>end</em></tt>

Without the <tt>lambda</tt>, this <tt>Time.zone.now</tt> will only be called once.
<h4><a name="passing-in-arguments"></a>13.2 Passing in arguments</h4>
When a <tt>lambda</tt> is used for a <tt>scope</tt>, it can take arguments:

<tt><em>class Post &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>scope :1_week_before, lambda { |time| where("created_at &lt; ?", time)</em></tt>

<tt><em>end</em></tt>

This may then be called using this:

<tt><em>Post.1_week_before(Time.zone.now)</em></tt>

However, this is just duplicating<span style="font-family: DejaVu Sans;">复制 </span>the functionality<span style="font-family: DejaVu Sans;">功能 </span>that would be provided to you by a class method.<span style="font-family: DejaVu Sans;">然而，这样仅仅通过一个类方法提供给来复制（使用）这个功能。</span>

<tt><em>class Post &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>def self.1_week_before(time)</em></tt>

<tt> </tt><tt><em>where("created_at &lt; ?", time)</em></tt>

<tt> </tt><tt><em>end</em></tt>

<tt><em>end</em></tt>

Using a class method is the preferred way to accept arguments for scopes. These methods will still be accessible<span style="font-family: DejaVu Sans;">访问 </span>on the association objects:

<span style="font-family: DejaVu Sans;">使用一个类方法是一个完美的方法来让</span>scopes<span style="font-family: DejaVu Sans;">获取参数。这个方法仍然访问</span>objects<span style="font-family: DejaVu Sans;">的</span>association<span style="font-family: DejaVu Sans;">。</span>

<tt><em>category.posts.1_week_before(time)</em></tt>
<h4><a name="working-with-scopes"></a>13.3 Working with scopes<span style="font-family: WenQuanYi Micro Hei;">使用作用域来工作</span></h4>
Where a relational object is required, the <tt>scoped</tt> method may come in handy. This will return an <tt>ActiveRecord::Relation</tt> object which can have further scoping applied to it afterwards. A place where this may come in handy is on associations

<tt><em>client = Client.find_by_first_name("Ryan")</em></tt>

<tt><em>orders = client.orders.scoped</em></tt>

With this new <tt>orders</tt> object, we are able to ascertain that this object can have more scopes applied to it. For instance,<strong> if we wanted to return orders only in the last 30 days at a later point</strong>.

<tt><em>orders.where("created_at &gt; ?", 30.days.ago)</em></tt>
<h4><a name="applying-a-default-scope"></a>13.4 Applying a default scope</h4>
If we wish for <strong>a scope</strong> to be <strong>applied</strong> across <strong>all queries</strong><span style="font-family: DejaVu Sans;"><strong>所有查询</strong> </span>to the model we can use the <tt><strong>default_scope</strong></tt> method within the model itself.

<tt><em>class Client &lt; ActiveRecord::Base</em></tt>

<tt> </tt><tt><em>default_scope where("removed_at IS NULL")</em></tt>

<tt><em>end</em></tt>

When queries are executed on this model, the SQL query will now look something like this:

<tt><em>SELECT * FROM clients WHERE removed_at IS NULL</em></tt>
<h4><a name="removing-all-scoping"></a>13.5 Removing all scoping</h4>
If we wish to remove scoping for any reason we can use the <tt>unscoped</tt> method. This is especially useful if a <tt>default_scope</tt> is specified in the model and should not be applied for this particular query.

<tt><em>Client.unscoped.all</em></tt>

This method removes all scoping and will do a normal query on the table.
<h3><a name="dynamic-finders"></a>14 Dynamic Finders<span style="font-family: WenQuanYi Micro Hei;">动态查询</span></h3>
For every field (also known as an attribute) you define in your table, Active Record provides a finder method. If you have a field called <tt>first_name</tt> on your <tt>Client</tt> model for example, you get <tt>find_by_first_name</tt> and <tt>find_all_by_first_name</tt> for free from Active Record. If you have a <tt>locked</tt> field on the <tt>Client</tt> model, you also get <tt>find_by_locked</tt> and <tt>find_all_by_locked</tt> methods.

<span style="font-family: DejaVu Sans;">在你表中定义的每个</span>field<span style="font-family: DejaVu Sans;">（通常被看着一个属性）， </span>Active Record<span style="font-family: DejaVu Sans;">都提供了一个</span>finder<span style="font-family: DejaVu Sans;">方法。例如，如果你有一个</span>field<span style="font-family: DejaVu Sans;">名叫</span>first_name<span style="font-family: DejaVu Sans;">在你的</span>Client<span style="font-family: DejaVu Sans;">模型中，你会免费的从</span>Active Record <span style="font-family: DejaVu Sans;">得到</span>find_by_first_name<span style="font-family: DejaVu Sans;">和</span>find_all_by_first_name<span style="font-family: DejaVu Sans;">方法。如果你在</span>Client<span style="font-family: DejaVu Sans;">模型中有一个</span>locked field<span style="font-family: DejaVu Sans;">，你也会获得</span>find_by_locked<span style="font-family: DejaVu Sans;">和</span>find_all_by_locked<span style="font-family: DejaVu Sans;">方法。</span>

You can also use <tt>find_last_by_*</tt> methods which will find the last record matching your argument.

<span style="font-family: DejaVu Sans;"><strong>你也可以使用</strong></span><strong>find_last_by_*</strong><span style="font-family: DejaVu Sans;"><strong>方法，它将会查找在机器中匹配你的参数的最后的记录。</strong></span>

You can specify an exclamation point (<tt>!</tt>) on the end of the dynamic finders to get them to raise an <tt>ActiveRecord::RecordNotFound</tt> error if they do not return any records, like <tt>Client.find_by_name!("Ryan")</tt>

<span style="font-family: DejaVu Sans;">你也可以在动态</span>finders<span style="font-family: DejaVu Sans;">末尾指定一个感叹号来来的到查询结果或者如果他们没有返回任何参数则抛出一个 </span><tt>ActiveRecord::RecordNotFound</tt><span style="font-family: DejaVu Sans;"><tt>错误。</tt></span>

<strong>If you want to find both by name and locked</strong>, you can chain these finders together by simply typing <tt>and</tt> between the fields. For example, <tt>Client.find_by_first_name_and_locked("Ryan", true).</tt>

<span style="color: #800000;">Up to and including Rails 3.1, when the number of arguments passed to a dynamic finder method is lesser than the number of fields, say </span><tt><span style="color: #800000;">Client.find_by_name_and_locked(“Ryan”)</span></tt><span style="color: #800000;">, the behavior is to pass </span><tt><span style="color: #800000;">nil</span></tt><span style="color: #800000;"> as the missing argument.<span style="font-family: DejaVu Sans;">如果参数少于动态</span></span><span style="color: #800000;">finder<span style="font-family: DejaVu Sans;">的</span></span><span style="color: #800000;">fields<span style="font-family: DejaVu Sans;">的数目，习惯上是对缺少的</span></span><span style="color: #800000;">fields<span style="font-family: DejaVu Sans;">传递一个</span></span><span style="color: #800000;">nil<span style="font-family: DejaVu Sans;">。 </span></span><span style="color: #800000;">This is </span><strong><span style="color: #800000;">unintentional</span></strong><span style="color: #800000;"> and this behavior will be changed in Rails 3.2 to throw an </span><tt><span style="color: #800000;">ArgumentError</span></tt><span style="color: #800000;">.<span style="font-family: DejaVu Sans;">这样做是没有意义的并且这个约定将会在</span></span><span style="color: #800000;">Rails 3.2<span style="font-family: DejaVu Sans;">中修改成抛出一个 </span></span><tt><span style="color: #800000;">ArgumentError</span></tt><span style="font-family: DejaVu Sans;"><tt><span style="color: #800000;">错误。</span></tt></span>

There’s another set of dynamic finders that let you find or create/initialize objects if they aren’t found. These work in a similar fashion to the other finders and can be used like <tt>find_or_create_by_first_name(params[:first_name])</tt>. Using this will first perform a find and then create if the find returns <tt>nil</tt>. The SQL looks like this for <tt>Client.find_or_create_by_first_name("Ryan")</tt>:

&nbsp;

<tt><em>SELECT * FROM clients WHERE (clients.first_name = 'Ryan') LIMIT 1</em></tt>

<tt><em>BEGIN</em></tt>

<tt><em>INSERT INTO clients (first_name, updated_at, created_at, orders_count, locked)</em></tt>

<tt> </tt><tt><em>VALUES('Ryan', '2008-09-28 15:39:12', '2008-09-28 15:39:12', 0, '0')</em></tt>

<tt><em>COMMIT</em></tt>

<tt><em>irb(main):134:0&gt; p=Post.find_or_create_by_name_and_title_and_content('1111111','111111111','11111111')</em></tt>

<tt> </tt><tt><em>Post Load (0.4ms) SELECT "posts".* FROM "posts" WHERE "posts"."title" = '111111111' AND "posts"."content" = '11111111' AND "posts"."name" = '1111111' LIMIT 1</em></tt>

<tt> </tt><tt><em>SQL (429.0ms) INSERT INTO "posts" ("content", "created_at", "name", "title", "updated_at") VALUES (?, ?, ?, ?, ?) [["content", "11111111"], ["created_at", Fri, 02 Dec 2011 08:42:39 UTC +00:00], ["name", "1111111"], ["title", "111111111"], ["updated_at", Fri, 02 Dec 2011 08:42:39 UTC +00:00]]</em></tt>

<tt><em>=&gt; #&lt;Post id: 5, name: "1111111", title: "111111111", content: "11111111", created_at: "2011-12-02 08:42:39", updated_at: "2011-12-02 08:42:39"&gt;</em></tt>

&nbsp;

<tt>find_or_create</tt>’s sibling, <tt>find_or_initialize</tt>, will find an object and if it does not exist will act similarly to calling <tt>new</tt> with the arguments you passed in. For example:

client = Client.find_or_initialize_by_first_name('Ryan')

will either assign an existing client object with the name “Ryan” to the client local variable, or initialize a new object similar to calling <tt>Client.new(:first_name =&gt; 'Ryan')</tt>. From here, you can modify other fields in client by calling the attribute setters on it: <tt>client.locked = true</tt> and when you want to write it to the database just call <tt>save</tt> on it.
<h3><a name="finding-by-sql"></a>15 Finding by SQL</h3>
If you’d like to use your own SQL to find records in a table you can use <tt>find_by_sql</tt>. The <tt>find_by_sql</tt> method will return an array of objects even if the underlying query returns just a single record. For example you could run this query:

<tt><em>Client.find_by_sql("SELECT * FROM clients</em></tt>

<tt> </tt><tt><em>INNER JOIN orders ON clients.id = orders.client_id</em></tt>

<tt> </tt><tt><em>ORDER clients.created_at desc")</em></tt>

<tt>find_by_sql</tt> provides you with a simple way of making custom calls to the database and retrieving instantiated objects.
<h3><a name="select_all"></a>16 <tt>select_all</tt></h3>
<tt>find_by_sql</tt> has a close relative called <tt>connection#select_all</tt>. <tt>select_all</tt> will retrieve objects from the database using custom SQL just like <tt>find_by_sql</tt> but will not instantiate them. Instead, you will get an array of hashes where each hash indicates<span style="font-family: DejaVu Sans;">指示 </span>a record.

<tt><em>Client.connection.select_all("SELECT * FROM clients WHERE id = '1'")</em></tt>
<h3><tt><span style="color: #000000;">15</span></tt><span style="font-family: WenQuanYi Micro Hei;"><tt><span style="color: #000000;">和</span></tt></span><tt><span style="color: #000000;">16 </span></tt><span style="font-family: WenQuanYi Micro Hei;"><tt><span style="color: #000000;">综合比较</span></tt></span></h3>
<tt><em>irb(main):174:0&gt; p=Post.find_by_sql("SELECT * FROM posts WHERE (posts.id = 1)") Post Load (1.7ms) SELECT * FROM posts WHERE (posts.id = 1)</em></tt>

<tt><em>=&gt; [#&lt;Post id: 1, name: "name111111111111111", title: "title111111111111111", content: "content111111111111111", created_at: "2011-12-02 08:56:33", updated_at: "2011-12-02 08:56:33"&gt;] </em></tt>

<tt><em>irb(main):175:0&gt; puts p</em></tt>

<tt><em>#&lt;Post:0xb6bf4d64&gt;</em></tt>

<tt><em>=&gt; nil</em></tt>

<tt><em>irb(main):176:0&gt; p=Post.connection.select_all("SELECT * FROM posts WHERE id = '1'")</em></tt>

<tt> </tt><tt><em>(0.6ms) SELECT * FROM posts WHERE id = '1'</em></tt>

<tt><em>=&gt; [{"name"=&gt;"name111111111111111", "created_at"=&gt;"2011-12-02 08:56:33.397313", "title"=&gt;"title111111111111111", "updated_at"=&gt;"2011-12-02 08:56:33.397313", "id"=&gt;1, "content"=&gt;"content111111111111111"}]</em></tt>

<tt><em>irb(main):177:0&gt; puts p</em></tt>

<tt><em>namename111111111111111created_at2011-12-02 08:56:33.397313titletitle111111111111111updated_at2011-12-02 08:56:33.397313id1contentcontent111111111111111</em></tt>

<tt><em>=&gt; nil</em></tt>
<h3><a name="existence-of-objects"></a>17 Existence of Objects<span style="font-family: WenQuanYi Micro Hei;">目标是否存在</span></h3>
If you simply want to check for the existence of the object there’s a method called <tt>exists?</tt>. This method will query the database using the same query as <tt>find</tt>, but instead of returning an object or collection of objects it will return either <tt>true</tt> or <tt>false</tt>.

<tt><em>Client.exists?(1)</em></tt>

The <tt>exists?</tt> method also takes multiple ids, but the catch is that it will return true if any one of those records exists.

<tt><em>Client.exists?(1,2,3)</em></tt>

<tt><em># or</em></tt>

<tt><em>Client.exists?([1,2,3])</em></tt>

It’s even possible to use <tt>exists?</tt> without any arguments on a model or a relation.

<tt><em>Client.where(:first_name =&gt; 'Ryan').exists?</em></tt>

The above returns <tt>true</tt> if there is at least one client with the <tt>first_name</tt> ‘Ryan’ and <tt>false</tt> otherwise.

<tt><em>Client.exists?</em></tt>

<strong>The above returns </strong><tt><strong>false</strong></tt><strong> if the </strong><tt><strong>clients</strong></tt><strong> table is empty and </strong><tt><strong>true</strong></tt><strong> otherwise.</strong>

You can also use <tt>any?</tt> and <tt>many?</tt> to check for existence on a model or relation.

<tt><em># via a model</em></tt>

<tt><em>Post.any?</em></tt>

<tt><em>Post.many?</em></tt>

&nbsp;

<tt><em># via a named scope</em></tt>

<tt><em>Post.recent.any?</em></tt>

<tt><em>Post.recent.many?</em></tt>

&nbsp;

<tt><em># via a relation</em></tt>

<tt><em>Post.where(:published =&gt; true).any?</em></tt>

<tt><em>Post.where(:published =&gt; true).many?</em></tt>

&nbsp;

<tt><em># via an association</em></tt>

<tt><em>Post.first.categories.any?#</em></tt><span style="font-family: DejaVu Sans;"><tt><em>第一个</em></tt></span><tt><em>post</em></tt><span style="font-family: DejaVu Sans;"><tt><em>的</em></tt></span><tt><em>category</em></tt><span style="font-family: DejaVu Sans;"><tt><em>是否存在</em></tt></span>

<tt><em>Post.first.categories.many?</em></tt>
<h3><a name="calculations"></a>18 Calculations</h3>
This section uses count as an example method in this preamble, but the options described apply to all sub-sections.

All calculation methods work directly on a model:

<tt><em>Client.count</em></tt>

<tt><em># SELECT count(*) AS count_all FROM clients</em></tt>

Or on a relation:

<tt><em>Client.where(:first_name =&gt; 'Ryan').count</em></tt>

<tt><em># SELECT count(*) AS count_all FROM clients WHERE (first_name = 'Ryan')</em></tt>

<tt><em>Post.first.tags.count</em></tt>

You can also use various finder methods on a relation for performing complex calculations:

<tt><em>Client.includes("orders").where(:first_name =&gt; 'Ryan', :orders =&gt; {:status =&gt; 'received'}).count</em></tt>

<tt><em>#Post.includes('tags').where(:id=&gt;"2",:tags=&gt;{:name=&gt;'123'}).count</em></tt>

Which will execute:

<tt><em>SELECT count(DISTINCT clients.id) AS count_all FROM clients</em></tt>

<tt> </tt><tt><em>LEFT OUTER JOIN orders ON orders.client_id = client.id WHERE</em></tt>

<tt> </tt><tt><em>(clients.first_name = 'Ryan' AND orders.status = 'received')</em></tt>
<h4><a name="count"></a>18.1 Count</h4>
If you want to see how many records are in your model’s table you could call <tt>Client.count</tt> and that will return the number. If you want to be more specific and find all the clients with their age present in the database you can use <tt>Client.count(:age)</tt>.

For options, please see the parent section, <a href="http://guides.rubyonrails.org/active_record_querying.html#calculations">Calculations</a>.
<h4><a name="average"></a>18.2 Average<span style="font-family: WenQuanYi Micro Hei;">平均值</span></h4>
If you want to see the average of a certain number in one of your tables you can call the <tt>average</tt> method on the class that relates to the table. This method call will look something like this:

<tt><em>Client.average("orders_count")</em></tt>

<tt><em>irb(main):215:0&gt; puts Post.average('created_at')</em></tt>

<tt> </tt><tt><em>(1.4ms) SELECT AVG("posts"."created_at") AS avg_id FROM "posts" </em></tt>

<tt><em>2011.0</em></tt>
<h4><a name="minimum"></a>18.3 Minimum</h4>
If you want to find the minimum value of a field in your table you can call the <tt>minimum</tt> method on the class that relates to the table. This method call will look something like this:

<tt><em>Client.minimum("age")</em></tt>

For options, please see the parent section, <a href="http://guides.rubyonrails.org/active_record_querying.html#calculations">Calculations</a>.
<h4><a name="maximum"></a>18.4 Maximum</h4>
If you want to find the maximum value of a field in your table you can call the <tt>maximum</tt> method on the class that relates to the table. This method call will look something like this:

<tt><em>Client.maximum("age")</em></tt>

For options, please see the parent section, <a href="http://guides.rubyonrails.org/active_record_querying.html#calculations">Calculations</a>.
<h4><a name="sum1"></a>18.5 Sum</h4>
If you want to find the sum of a field for all records in your table you can call the <tt>sum</tt> method on the class that relates to the table. This method call will look something like this:

<a name="sum"></a><tt><em>Client.sum("orders_count")</em></tt>

&nbsp;
