---
layout: post
title: "在 Cloud Foundry 上使用 JRuby for Rails 应用程序"
date: 2012-09-10 22:28
comments: true
categories: [ruby,web]
tags: [Cloud Foundry,JRuby for Rails]
---
## 在 Cloud Foundry 上使用 JRuby for Rails 应用程序
<p align="left">[译注]本文翻译自Cloud Foundry英文博客站点，原文题为“<a href="http://blog.cloudfoundry.com/post/13481011043/deploying-jruby-on-rails-applications-on-cloud-foundry"><strong>Using JRuby for Rails Applications on Cloud Foundry</strong></a>”，文章发表时间是 2012 年 4 月 19 日。</p>
<p align="left">如今，只需进行一些简单的配置更改，即可将 JRuby Rails 应用程序部署到 CloudFoundry.com。JRuby 应用程序常常通过创建一个包含 Rails 应用程序的 .war 文件来部署到 servlet 容器中。对于 Cloud Foundry，我们将采取同样的做法，同时对数据库配置进行一些更改，以便应用程序还可以访问 CloudFoundry.com 上的数据库服务。</p>

<h4 align="left">将 JRuby on Rails 应用程序部署到 Cloud Foundry 上时需进行的更改</h4>
<p align="left">要使 JRuby 应用程序在 CloudFoundry.com 上运行，我们需要完成两项任务。首先，我们需通过修改 configuration 目录中的 database.yml 文件将该应用程序配置为连接到 CloudFoundry.com 上的数据库服务。当我们部署该应用程序时，我们还需要运行“rake db:migrate”的等效命令，以便创建数据库表。我们可以通过在 config/initializers 目录中添加一个初始化程序来做到这一点。</p>
<p align="left">环境变量 VCAP_SERVICES 中提供了我们在配置数据库连接时所需的信息。我们可以通过编程方式分析该变量，也可以使用方便的 Cloud Foundry 运行时 gem（请参见<a href="http://cnblog.cloudfoundry.com/?p=234"><strong>将</strong><strong> Cloud Foundry </strong><strong>服务与</strong><strong> Ruby </strong><strong>搭配使用：第</strong><strong> 2 </strong><strong>部分</strong><strong> – </strong><strong>对</strong><strong> Ruby </strong><strong>应用程序的实时支持</strong></a>博文），在本篇博文中我们将采用后者。要使用该 gem，我们需要将它包含在我们的 Gemfile 中：</p>

<div>
<div id="highlighter_85231">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>...</code></div>
<div><code>gem  </code><code>'cf-runtime'</code></div>
<div><code>...</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p align="left">既然我们已经添加了该 gem，我们就可以向 database.yml 文件中添加一些代码段，用以访问生产环境的数据库服务信息。下面是一个 database.yml 文件中的 production 部分，在这个文件中我们使用的是 MySQL 数据库：</p>
<p align="left"><strong>config/database.yml</strong></p>

<div>
<div id="highlighter_84359">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>production:</code></div>
<div><code>    </code><code>adapter: mysql</code></div>
<div><code>    </code><code>&lt;% require </code><code>'cfruntime/properties'</code> <code>%&gt;</code></div>
<div><code>    </code><code>&lt;% db_svc = CFRuntime::CloudApp.service_props(</code><code>'mysql'</code><code>) %&gt;</code></div>
<div><code>    </code><code>database: &lt;%= db_svc[:database] rescue </code><code>'bookshelf_production'</code> <code>%&gt;</code></div>
<div><code>    </code><code>username: &lt;%= db_svc[:username] rescue </code><code>'root'</code> <code>%&gt;</code></div>
<div><code>    </code><code>password: &lt;%= db_svc[:password] rescue </code><code>''</code> <code>%&gt;</code></div>
<div><code>    </code><code>host: &lt;%= db_svc[:host] rescue </code><code>'localhost'</code> <code>%&gt;</code></div>
<div><code>    </code><code>port: &lt;%= db_svc[:port] rescue </code><code>'3306'</code> <code>%&gt;</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p align="left">正如您可以看到的那样，我们添加了一个 require 语句来获取 cfruntime/properties，然后我们通过调用 service_props 方法并在调用时传入我们所使用的服务类型来获取服务属性的哈希值。如果仅有一项属于该类型的服务绑定到该应用程序，则无需指定此服务的实际名称。如果您将多项属于同一类型的服务绑定到您的应用程序，您将需要指定实际服务名称。服务属性的哈希值存储在一个名为 db_svc 的变量中，代码会将对应的值提取出来以用作数据库、用户名、密码、主机和端口。其中的每一条语句都有一个 rescue 子句，如果我们并非在 Cloud Foundry 环境中操作，则该语句将提供要使用的值，这种情况下 db_svc 将为 Nil。</p>
<p align="left">另外，如果使用的是 PostgreSQL，则该 database.yml 文件的 production 部分将大致如下：</p>

<div>
<div id="highlighter_152958">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>production:</code></div>
<div><code>   </code><code>adapter: postgresql</code></div>
<div><code>   </code><code>encoding: unicode</code></div>
<div><code>   </code><code>&lt;% require </code><code>'cfruntime/properties'</code> <code>%&gt;</code></div>
<div><code>   </code><code>&lt;% db_svc = CFRuntime::CloudApp.service_props(</code><code>'postgresql'</code><code>) %&gt;</code></div>
<div><code>   </code><code>database: &lt;%= db_svc[:database] rescue </code><code>'bookshelf_production'</code> <code>%&gt;</code></div>
<div><code>   </code><code>username: &lt;%= db_svc[:username] rescue </code><code>'bookshelf'</code> <code>%&gt;</code></div>
<div><code>   </code><code>password: &lt;%= db_svc[:password] rescue </code><code>''</code> <code>%&gt;</code></div>
<div><code>   </code><code>host: &lt;%= db_svc[:host] rescue </code><code>'localhost'</code> <code>%&gt;</code></div>
<div><code>   </code><code>port: &lt;%= db_svc[:port] rescue </code><code>'5432'</code> <code>%&gt;</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p align="left">接下来，我们将注意力转向创建供我们的应用程序使用的表上面。为此，我们在部署该应用程序时需向 config/initializers 目录中添加以下初始化程序。我将该初始化程序命名为 cf_db_migrate.rb：</p>
<p align="left"><strong>config/initializers/cf_db_migrate.rb</strong></p>

<div>
<div id="highlighter_908259">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>require </code><code>'cfruntime/properties'</code></div>
<div></div>
<div><code># Run the equivalent of rake db:migrate on startup</code></div>
<div><code>if</code> <code>CFRuntime::CloudApp.running_in_cloud?</code></div>
<div><code>  </code><code>migrations = Rails.root.join(</code><code>'db'</code><code>,</code><code>'migrate'</code><code>)</code></div>
<div><code>  </code><code>if</code> <code>migrations.directory?</code></div>
<div><code>    </code><code>ActiveRecord::Migrator.migrate(migrations)</code></div>
<div><code>  </code><code>end</code></div>
<div><code>end</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p align="left">我们再次使用 cfruntime/properties 来检查我们当前是否在云中运行。接下来，我们将检查 db/migrate 目录是否存在；如果它存在，我们将使用该目录中的迁移文件来运行数据库迁移 (ActiveRecord::Migrator.migrate(migrations))。</p>
<p align="left">我们还必须进行的一项更改就是对 warble 配置的更改。默认情况下此配置在生成的 war 文件中不包含 db/migrate 目录，因此我们需要通过指定 config.includes = FileList["db/migrate/*"] 将此目录添加到此配置中。下面是 config/warble.rb 文件的相关内容：</p>
<p align="left"><strong>config/warble.rb</strong></p>

<div>
<div id="highlighter_236881">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code># Warbler web application assembly configuration file</code></div>
<div><code>Warbler::Config.</code><code>new</code> <code>do</code> <code>|config|</code></div>
<div></div>
<div><code>  </code><code># Application directories to be included in the webapp.</code></div>
<div><code>  </code><code>config.dirs = %w(app config lib log vendor tmp)</code></div>
<div></div>
<div><code>  </code><code># Additional files/directories to include, above those in config.dirs</code></div>
<div><code>  </code><code>config.includes = FileList[</code><code>"db/migrate/*"</code><code>]</code></div>
<div></div>
<div><code>end</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<h4 align="left">一个完整示例</h4>
<a href="http://jhjguxin.sinaapp.com/?attachment_id=244" rel="attachment wp-att-244"><img title="Example" src="http://cnblog.cloudfoundry.com/wp-content/uploads/2012/07/Example.tif" alt="" /></a>
<p align="left">我们从上文中已经了解到需做出哪些更改，那我们就来快速生成一个 Rails 应用程序并做出所需的更改，然后将该应用程序部署到 CloudFoundry.com。如果您尚未安装 JRuby，建议首先参阅 <a href="http://jruby.org/getting-started"><strong>JRuby </strong><strong>入门</strong></a>。</p>

<h3 align="left"><strong>创建</strong><strong> JRuby Rails </strong><strong>应用程序</strong><strong></strong></h3>
<p align="left">首先，我们创建新应用程序，并创建具有完整基架的第一个域对象。</p>

<pre>&lt;code&gt;jruby -S rails new bookshelf -d mysql cd bookshelf jruby -S rails generate scaffold Book title:string category:string published:integer price:decimal{10.2} isbn:string &lt;/code&gt;</pre>
<p align="left">接下来，我们删除生成的 public/index.html，然后修改 config/routes.rb 以使用“books”作为根目录：</p>

<pre>&lt;code&gt;rm public/index.html vi config/routes.rb&lt;/code&gt;</pre>
<p align="left">下面是我在 config/routes.rb 中添加的路由：</p>

<div>
<div id="highlighter_226813">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>Bookshelf::Application.routes.draw </code><code>do</code></div>
<div><code>  </code><code>resources :books</code></div>
<div></div>
<div><code>  </code><code># You can have the root of your site routed with </code><code>"root"</code></div>
<div><code>  </code><code># just remember to delete </code><code>public</code><code>/index.html.</code></div>
<div><code>  </code><code># root :to =&gt; </code><code>'welcome#index'</code></div>
<div><code>  </code><code>root :to =&gt; </code><code>'books#index'</code></div>
<div></div>
<div><code>  </code><code># See how all your routes lay out with </code><code>"rake routes"</code></div>
<div></div>
<div><code>end</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p align="left">现在，我们将在本地运行此应用程序以确保它正常运行：</p>

<pre>&lt;code&gt;jruby -S rake db:create jruby -S rake db:migrate jruby -S rails server&lt;/code&gt;</pre>
<p align="left"><a href="http://jhjguxin.sinaapp.com/?attachment_id=237" rel="attachment wp-att-237"><img title="1" src="http://cnblog.cloudfoundry.com/wp-content/uploads/2012/07/1.jpg" alt="" width="743" height="475" /></a></p>
<p align="left">Book 实体的 Rails 空列表视图表明它确实正常运行。现在，我可以向我的书架添加新书。</p>

<h3 align="left"><strong>修改</strong><strong> JRuby Rails </strong><strong>应用程序以便部署到</strong><strong> CloudFoundry</strong></h3>
<p align="left">我们来首先进行我们在上文中提到的以下更改：</p>

<ul>
	<li>将 gem cf-runtime 添加到 Gemfile</li>
	<li>修改 config/database.yml 文件的“production:”节，具体见<a href="http://cnblog.cloudfoundry.com/?p=236/#database.yml"><strong>上文</strong></a></li>
	<li>添加一个名为 config/initializers/cf_db_migrate.rb 的文件，文件内容见<a href="http://cnblog.cloudfoundry.com/?p=236/#cf_db_migrate.rb"><strong>上文</strong></a></li>
</ul>
<p align="left">接下来我们需要生成 Warbler 配置文件，因此我们将运行：</p>

<pre>&lt;code&gt;jruby -S warble config &lt;/code&gt;</pre>
<p align="left">现在，我们就可以：</p>

<ul>
	<li>修改 config/warble.rb 以添加 db/migrate 目录，具体见<a href="http://cnblog.cloudfoundry.com/?p=236/#warble.rb"><strong>上文</strong></a></li>
</ul>
<p align="left">这些便是需要做出的全部更改，我们现在就已万事俱备，可以打包和部署此应用程序了。</p>

<h3 align="left"><strong>打包</strong><strong> JRuby Rails </strong><strong>应用程序并将其部署到</strong><strong> CloudFoundry</strong></h3>
<p align="left">我们将使用 <a href="http://kenai.com/projects/warbler/pages/Home"><strong>Warbler</strong></a> 来将此应用程序打包成一个 war，并使用 <a href="http://start.cloudfoundry.com/tools/vmc/installing-vmc.html"><strong>CloudFoundry vmc</strong></a> 命令行实用程序来部署此应用程序。</p>
<p align="left">我们用来将此应用程序打包成 war 文件的流程十分简单：捆绑，预编译资产，然后运行 Warbler：</p>

<pre>&lt;code&gt;jruby -S bundle install jruby -S rake assets:precompile jruby -S warble&lt;/code&gt;</pre>
<p align="left">这会在我们的 Rails 应用程序的根目录中创建一个 bookshelf.war。目前，将 vmc 命令与 JRuby 一起运行时会存在一些问题，不过我们正在致力于加以修复。同时，我们还可以将此 war 文件移至其他目录，这样我就可以更轻松地改用常规的“C”Ruby。我将创建一个“deploy”目录，并将该目录配置为使用 Ruby 1.9.2-p290（我当前使用的是 rbenv，但您也可以使用 RVM）：</p>

<pre>&lt;code&gt;mkdir deploy mv bookshelf.war deploy/. cd deploy rbenv local 1.9.2-p290 # (if you use RVM the command should be &#039;rvm ruby-1.9.2-p290&#039;)&lt;/code&gt;</pre>
<p align="left">现在，我们已准备就绪，可以登录 CloudFoundry 并部署我们的应用程序了。对于此部分，您需要安装<a href="http://start.cloudfoundry.com/tools/vmc/installing-vmc.html"><strong>vmc</strong></a>。</p>

<pre>&lt;code&gt;vmc target api.cloudfoundry.com vmc login cloud@mycompany.com Password: ***** Successfully logged into [http://api.cloudfoundry.com] vmc push bookshelf Would you like to deploy from the current directory? [Yn]: Y Application Deployed URL [bookshelf.cloudfoundry.com]: mybookshelf.cloudfoundry.com Detected a Java Web Application, is this correct? [Yn]: Y Memory reservation (128M, 256M, 512M, 1G, 2G) [512M]: 512M How many instances? [1]: 1 Bind existing services to &#039;bookshelf&#039;? [yN]: N Create services to bind to &#039;bookshelf&#039;? [yN]: Y 1: mongodb 2: mysql 3: postgresql 4: rabbitmq 5: redis What kind of service?: 2 Specify the name of the service [mysql-a4fd7]: mysql-books Create another? [yN]: N Would you like to save this configuration? [yN]: N Creating Application: OK Creating Service [mysql-books]: OK Binding Service [mysql-books]: OK Uploading Application: Checking for available resources: OK Processing resources: OK Packing application: OK Uploading (707K): OK Push Status: OK Staging Application &#039;bookshelf&#039;: OK Starting Application &#039;bookshelf&#039;: OK&lt;/code&gt;</pre>
<p align="left">上面已将 vmc 命令突出显示出来。除 URL 以及是否应创建服务外，已接受大部分默认设置。我使用的 URL 为“mybookshelf.cloudfoundry.com”而非默认 URL，以免与现有的书架应用程序冲突。对于是否创建新服务的问题，我回答“Y”；此外我还选择了 (2) mysql 并将其命名为“mysql-books”。</p>
<p align="left">现在我们应看到此应用程序正在运行：</p>

<pre>&lt;code&gt;vmc apps +-------------+----+---------+---------------------------------+---------------+ | Application | # | Health | URLS | Services | +-------------+----+---------+---------------------------------+---------------+ | bookshelf | 1 | RUNNING | mybookshelf.cloudfoundry.com | mysql-books | +-------------+----+---------+---------------------------------+---------------+&lt;/code&gt;</pre>
<p align="left">因此，我们现在输入“http://mybookshelf.cloudfoundry.com/”，就可以看到此 Bookshelf 应用程序惟妙惟肖地展示出来，我们可以添加一些书籍。</p>
<p align="left"><a href="http://jhjguxin.sinaapp.com/2012/02/14/rvm%e4%b8%8b%e5%ae%89%e8%a3%85gem%e7%bc%96%e8%af%91%e5%a4%b1%e8%b4%a5-linecache19/237-revision/" rel="attachment wp-att-238"><img title="2" src="http://cnblog.cloudfoundry.com/wp-content/uploads/2012/07/2.jpg" alt="" width="743" height="475" /></a></p>
<p align="left">您可以在 <a href="https://github.com/cloudfoundry-samples/jruby-rails-bookshelf"><strong>cloudfoundry-samples/jruby-rails-bookshelf</strong></a> 位置查看和下载用于此示例的完整源代码；如果您只想了解为在 Cloud Foundry 中部署而需进行的更改，请查看此<a href="https://github.com/cloudfoundry-samples/jruby-rails-bookshelf/commit/b568eac02b1cf50f47d62de0d0c63219c3198fc0"><strong>提交</strong></a>。</p>

<h4 align="left">结论</h4>
<p align="left">我们已经证明了可以将一个简单的 JRuby on Rails 应用程序部署到 cloudfoundry 并使用 MySQL 服务作为支持它的数据存储。只需对数据库的应用程序配置进行一些修改，即可做到。</p>
<p align="left">我们将发布一篇博文来阐述对于使用 DataMapper 实现持久保留的 JRuby Sinatra 应用程序我们需要进行的类似更改。</p>
<p align="left"><em>- Cloud Foundry </em><em>团队</em>
<em></em><em>Thomas Risberg</em></p>
