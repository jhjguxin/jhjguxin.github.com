---
layout: post
title: "跨越边界: Ajax on Rails"
date: 2012-03-02 13:26
comments: true
categories: [rails,ruby]
tags: [ajax,rails,ruby]
---
## 跨越边界: Ajax on Rails
<em>跨越边界</em> 系列之前的两篇文章（参见 <a href="http://www.ibm.com/developerworks/cn/java/j-cb12056/index.html#resources">参考资料</a>）全面介绍了 Streamlined，这是 Rails 的辅助框架，该框架有效地利用 scaffolding 来快速生成简单的、使用 Ajax 的用户界面。除非您一直与世隔绝，不然您一定会知道 Ajax 是这样一种编程技术，它使用 XML、JavaScript 和 Web 标准来创建高度交互性的 Web 页面，正如您在 Google Maps 和大量其他站点上所看到的页面那样。许多读过 Streamlined 文章的读者都要求我描述一下 Ajax 在 Ruby on Rails 上的运行方式。本文全面介绍了两个简单的 Ajax 例子，延着这个思路介绍了 Ruby/Ajax 这一组合如此成功的原因。在本系列的下篇文章中，我将探究 JavaScript 这门编程语言。

<a name="N10062"></a>Ajax 定义

Ajax 代表 Asynchronous JavaScript + XML。信息架构师 Jesse James Garrett 于 2005 年提出这一术语，该术语用来描述一门在夹缝中生存了近二十年的技术（参见 <a href="http://www.ibm.com/developerworks/cn/java/j-cb12056/index.html#resources">参考资料</a>）。Ajax 的使用随即爆增，不论在图书馆、流行网站还是文献作品中都保持同步增长。

Ajax 重新定义了基本的浏览器使用模型。原模型一次呈现一个页面。Ajax 允许浏览器在页面更新的间隔同服务器进行交流。这样做的好处是带来更加丰富的客户体验，但却以增加复杂度为代价。Ajax 是这样运行的：使用 JavaScript 客户端库在客户机和服务器间发送 XML。Ajax 开发人员可以在任何时刻从客户机发送异步请求，因而在服务器处理这些请求时，用户交互可以继续进行。下面就是 Ajax 请求的流程：
<div>
<h2>关于本系列</h2>
<div>

在 <a href="http://www.ibm.com/developerworks/cn/java/j-cb/">跨越边界系列</a> 文章中，作者 Bruce Tate 提出这样一种观点，即当今的 Java 程序员们可以通过学习其他方法和语言很好地武装自己。自从 Java 技术明显成为所有开发项目最好的选择以来，编程前景已经发生了改变。其他框架影响着 Java 框架的构建方式，从其他语言学到的概念也可以影响 Java 编程。您编写的 Python（或 Ruby、Smalltalk 等语言）代码可以改变编写 Java 代码的方式。

本系列介绍与 Java 开发完全不同的编程概念和技术，但是这些概念和技术也可以直接应用于 Java 开发。在某些情况下，需要集成这些技术来利用它们。在其他情况下，可以直接应用概念。具体的工具并不重要，重要的是其他语言和框架可以影响 Java 社区中的开发人员、框架，甚至是基本方式。

</div>
</div>
<ol>
	<li>一个事件（如用户的鼠标点击或编程计时器的触发）启动一个 JavaScript 函数。</li>
	<li>JavaScript 函数为部分页面而不是整个页面创建一个请求。JavaScript 随后通过 HTTP 将该请求发送到 Web 服务器。</li>
	<li>此 HTTP 请求调用服务器上的一个脚本，如 Rails 控制器方法或 Java™ servlet。</li>
	<li>该服务器脚本创建一个 XML 文档并将其返回给服务器。</li>
	<li>在接收结果的同时，客户机异步处理创建、更新或删除部分 Web 页面，如列表元素、<code>div</code> 标记或图像。</li>
</ol>
所有 Ajax 应用程序都使用类似这种顺序的一种方法。例如，某个应用程序允许将字典中的单词与其定义一起保存。旧式的应用程序会强迫您用一个新的页面视图来编辑定义。Ajax 允许原地编辑，它用一个条目字段替换定义文本，然后用更新的定义来替换该表单。

Ajax 解决方案的组件是：
<ul>
	<li>客户端 JavaScript 库，用来管理异步请求。</li>
	<li>服务器端 JavaScript 库，用来处理进来的请求，并构造一个 XML 响应。</li>
	<li>客户端 JavaScript 库，用来处理生成的 XML。</li>
	<li>称作文档对象模型（DOM）的库，允许对现有 Web 页面进行更新。</li>
	<li>辅助例程，用来处理不可避免的 UI 和集成问题。</li>
</ul>
事件/请求/响应/替换模型是大多数 Ajax 应用程序的核心模型，但如果您刚接触 Ajax，您一定会对 Ajax 中大量的可用库和这些库之间巨大的差别感到惊讶不已。该领域中有许多 Ajax 框架，它们的功能常常重叠且没有确定的胜出者。单就 Java 市场而言，有许多库可用，包括 Echo、Dojo、DWR、Google Web Toolkit（GWT）、Java Web Parts、AjaxAnywhere、AjaxTags、Scriptaculous 和 Prototype。这些框架使用截然不同的方法。一些框架试图通过生成 JavaScript 代码的 Java 库来隐藏 JavaScript，如 GWT。另一些框架致力于使 JavaScript 更易使用。一些相当地全面，如 Dom4J，而另一些则仅着力于解决好一个小问题。由于有许多流行的新技术，解决方案之间互相割据的场面有时会很难驾驭，调试工具、UI 实践（如 Back 按钮）和明智的开发实践的实现非常缓慢。Java 平台上的 Ajax 库的力量源自其多样性。这也正是其缺点所在，因为多样性导致了难以决断、集成方面的顾虑和复杂性。

有了 Ruby on Rails，开发体验就显著不同了，这是由于两个原因。首先，Ruby on Rails 有一个核心的 Web 开发平台：Ruby on Rails。其次，到目前为止，大多数在 Rails 上的 Ajax 开发体验都围绕着两个核心框架：Scriptaculous 和 Prototype（参见 <a href="http://www.ibm.com/developerworks/cn/java/j-cb12056/index.html#resources">参考资料</a>）。Rails 方法使用运行时代码生成和定制标记，这使您不必理会复杂的 JavaScript。是时候自己来实践了。如果您想要在学习本文的过程中编写代码的话，需要下载 Rails，也要下载必要的 Ajax 框架（参见 <a href="http://www.ibm.com/developerworks/cn/java/j-cb12056/index.html#resources">参考资料</a>）。打开您的 Rails 环境，跟我一起来吧。
<div>

<hr />

</div>
<a href="http://www.ibm.com/developerworks/cn/java/j-cb12056/index.html#ibm-pcon">回页首</a>

<a name="N100D4"></a>没有 Ajax 的简单的 Rails 应用程序

要使用 Rails 和 Ajax，就要创建一个空项目，并生成一个有两个方法的控制器。一个控制器控制简单的页面，另一个控制器建立一个 Ajax 响应。键入下列代码：
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>rails ajax
cd ajax
script/generate controller ajax show time</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

第一行和第二行代码生成一个 Rails 项目，并切换到新目录。第三行代码生成一个叫做 <code>ajax</code> 的控制器，并查看两个动作：<code>show</code> 和 <code>time</code>。清单 1 显示了该控制器的代码：
<a name="N100F5"></a><strong>清单 1. 有两个空方法的控制器 </strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>class AjaxController &lt; ApplicationController

   def show
   end

   def time
   end
end</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

首先在不使用 Ajax 的情况下构建两个简单视图，然后用 Ajax 将这两个视图绑定到一起。编辑 app/views/ajax 中的 show.rhtml 视图，使它和清单 2 类似：
<a name="N10103"></a><strong>清单 2. 简单视图</strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>&lt;h1&gt;Ajax show&lt;/h1&gt;
Click this link to show the current &lt;%= link_to "time", :action =&gt; "time" %&gt;.</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

清单 1 和清单 2 中的代码不支持 Ajax，但我还是会仔细分析该代码。首先，看清单 1 中的控制器。两个空的控制器方法处理进来的 HTTP 请求。如果不明确地呈现一个视图（使用 <code>render</code> 方法），Rails 会呈现和该方法同名的视图。由于 Scriptaculous 和 Prototype 库也使用 HTTP，Rails 不需要对标准 HTTP 方法和 Ajax 方法进行区分。

现在将注意力转移到清单 2 中的视图。大多数代码都是简单的 HTML 代码，只有第二行的 <code>link_to</code> 辅助例程例外：<code>&lt;%= link_to "time", :action =&gt; "time" %&gt;</code>。

正如在<em>跨越边界</em> 之前的文章中所看到的那样，Ruby 用其表达式的值替代 <code>&lt;%=h</code> 和 <code>%&gt;</code> 之间的代码。在这个示例中，<code>link-to</code> 方法是一个生成简单 HTML 链接的辅助例程。可以通过执行该代码看到该链接。通过键入 <code>script/server</code> 启动服务器，然后将浏览器指向 http://localhost:3000/ajax/show 。您将看到图 1 中的视图：
<a name="figure1"></a><strong>图 1. 不涉及 Ajax 的简单用户界面</strong>
<img src="http://www.ibm.com/developerworks/cn/java/j-cb12056/figure-1.jpg" alt="不涉及 Ajax 的简单用户界面" width="296" height="211" />

在浏览器中，单击菜单项来查看页面源代码（在 Internet Explorer 为 <strong>View &gt; Source</strong> ，在 Firefox 中为 <strong>View &gt; Page Source</strong>）。您将看到清单 3 中的代码：
<a name="N1014E"></a><strong>清单 3. 由 show.rhtml 生成的视图</strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>&lt;h1&gt;Ajax show&lt;/h1&gt;
Click this link to show the current &lt;a href="/ajax/time"&gt;time&lt;/a&gt;.</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

请注意清单 3 中的链接代码。该模板让 Rails 用户不必面对冗长且容易出错的 HTML 句法。（Ajax 代码也是这样运行：使用辅助方法插入 JavaScript 代码，该代码替您管理远程请求和 HTML 替换。）如果单击该链接，将看到针对 <code>time</code> 方法的默认视图，但我还没有实现它。为加以补救，请用清单 4 中的代码替换 app/controllers/ajax_controller.rb 中的 <code>time</code> 方法。为保持简单，我直接从控制器中呈现视图。稍后，我会把一切处理好并呈现视图。
<a name="N10164"></a><strong>清单 4. 呈现时间</strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>def time
  render_text "The current time is #{Time.now.to_s}"
end</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

现在，当单击该链接时，会得到图 2 中的视图：
<a name="figure2"></a><strong>图 2. 不涉及 Ajax 的视图</strong>
<img src="http://www.ibm.com/developerworks/cn/java/j-cb12056/figure-2.jpg" alt="不涉及 Ajax 的简单视图" width="394" height="215" />

很快就能看到这个 UI 的一个问题。这两个视图不从属于单独的页面。该应用程序表示一个单一概念：单击一个链接来显示时间。为反复更新时间，每次都需要单击该链接和 Back 按钮。将该链接和时间放到相同的页面中也许可以解决这个问题。但如果该页面变得非常大或非常复杂，重新显示整个页面会很浪费，也会很复杂。
<div>

<hr />

</div>
<a href="http://www.ibm.com/developerworks/cn/java/j-cb12056/index.html#ibm-pcon">回页首</a>

<a name="N10180"></a>添加 Ajax

Ajax 让您可以只更新 Web 页面的一个片段。Rails 库为您处理大部分的工作。要将 Ajax 添加到这个应用程序中，需要以下四个步骤：
<ol>
	<li>配置 Rails 以使用 JavaScript。</li>
	<li>更改时间链接来提交 JavaScript Ajax 请求，而不是仅呈现一个 HTML 链接。</li>
	<li>指定要更新的 HTML 片断。</li>
	<li>为更新的 HTML 内容准备一个位置。</li>
	<li>构建一个控制器方法，或者一个视图来呈现 Ajax 响应。</li>
</ol>
首先，更改 app/views/ajax/show.rhtml 中的代码，使其与清单 5 类似：
<a name="N101A2"></a><strong>清单 5. 更改显示视图来使用 Ajax</strong>
<pre id="best-answer-content">rails3 中取消了link_to_remote方法
取而代之的是
使用的 remote参数
例如：
link_to image_tag("delete.png"), { :controller =&gt; 'products', :action =&gt; 'destroy', :id =&gt; product }, :method =&gt; :delete, :confirm =&gt; "Are you sure?", :remote =&gt; true</pre>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>&lt;%= javascript_include_tag :defaults %&gt;
&lt;h1&gt;Ajax show&lt;/h1&gt;
Click this link to show the current
&lt;%= link_to_remote "time",
    :update =&gt; 'time_div',
    :url =&gt; {:action =&gt; "time"} %&gt;.&lt;br/&gt;
&lt;div id='time_div'&gt;
&lt;/div&gt;</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

我做了一些更改。首先，为处理配置，简单地将必要的 JavaScript 库直接包含在视图中。通常，还会有更多的视图，为避免重复，我将 JavaScript 文件包含在一个公共的 Rails 组件中，如 Rails 布局。本例只有一个视图，所以一切从简。

其次，我改变了链接标记来使用 <code>link_to_remote</code>。您一会儿就能看到这个链接的作用。请注意下列三个参数：
<ul>
	<li>链接文本：从非 Ajax 的例子中照搬过来。</li>
	<li><code>:update</code> 参数。如果您以前没见过这种语法，那么就把 <code>:update =&gt; 'time_div'</code> 当作是一个已命名的参数，其中的 <code>:update</code> 是名称，<code>update_div</code> 是值。此代码告诉 Prototype 库：此链接中的结果将用 <code>time_div</code> 这一名称更新 HTML 组件。</li>
	<li>代码 <code>:url =&gt; {:action =&gt; "time"}</code> 指定该链接将调用的 URL。<code>:url</code> 从一个哈希映射表中获取值。在实际中，该哈希映射表只有一个针对控制器动作的元素：<code>:time</code>。理论上，该 URL 也可以包含控制器的名称和控制器需要的任何可选参数。</li>
</ul>
在清单 5 中，还可以看到空的 <code>div</code>，Rails 将用当前时间更新它。

在浏览器中，装载页面 http://localhost:3000/ajax/show。单击该链接，将看到图 3 中的结果。
<a name="figure3"></a><strong>图 3. 含 Ajax 的视图</strong>
<img src="http://www.ibm.com/developerworks/cn/java/j-cb12056/figure-3.jpg" alt="含 Ajax 的简单视图" width="443" height="197" />

为了很好地了解这里发生的情况，请查看该 Web 页面的源代码。清单 6 显示了该代码：
<a name="N10207"></a><strong>清单 6. 显示模板的结果（在启用 Ajax 的情况下）</strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>&lt;script src="/javascripts/prototype.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="/javascripts/effects.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="/javascripts/dragdrop.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="/javascripts/controls.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="/javascripts/application.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;h1&gt;Ajax show&lt;/h1&gt;
Click this link to show the current
&lt;a href="#" onclick="new Ajax.Updater(
   'time_div', '/ajax/time', {asynchronous:true, evalScripts:true});
   return false;"&gt;time&lt;/a&gt;.&lt;br/&gt;
&lt;div id='time_div'&gt;

&lt;/div&gt;</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

请注意包含的 JavaScript 列表。Rails 辅助方法（<code>include_javascript_tags :defaults</code>）为您构建了此列表。接下来，看到一个用来构建新的 <code>Ajax.Updater</code> 对象的 JavaScript 函数调用，而不是一个 HTML 链接。正如您所料想的那样，名为 <code>asynchronous</code> 的参数被设置为 true。最后，在 HTML <code>div</code> 标记中看不到值，这是由于初始页面在那里没有值。
<div>

<hr />

</div>
<a href="http://www.ibm.com/developerworks/cn/java/j-cb12056/index.html#ibm-pcon">回页首</a>

<a name="N10221"></a>使用其他 Ajax 选项

Ajax 能生成强大的动作，甚至能生成一些预料不到的动作。例如，用户也许没注意到更新的时间链接。<code>link_to_remote</code> 选项让您能够轻易地将特殊效果应用到该条目上，从而让用户注意到该结果。现在将应用一些效果。请更改 show.rhtml 中的 <code>link_to_remote</code> 辅助方法，使它与清单 7 类似：
<a name="N10236"></a><strong>清单 7. 添加效果</strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>&lt;%= link_to_remote "time",
    :update =&gt; 'time_div',
    :url =&gt; {:action =&gt; "time"},
    :complete =&gt; "new Effect.Highlight('time_div')" %&gt;</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

最佳 Ajax 效果会使您的更改获得临时的关注，但却不会永久持续。您的目标应该是把更改提示给用户，而不打断他们的工作流。像这种用黄色来弱化强调的技术，或用滑入内容或淡出内容来让用户注意的技术都不是长久之计。

到目前为止，链接是您见到的惟一触发器。Ajax 还有许多其他的可用武器，一些由用户驱动，而另一些由程序事件驱动，如时钟。它是一个像闹钟一样并不需要用户干预的东西。可以用 Ajax 的 <code>periodically_call_remote</code> 方法定期更新时钟。请按照清单 8 编辑 show.rhtml 中的代码：
<a name="N1024B"></a><strong>清单 8. 定期调用远程方法</strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>&lt;%= javascript_include_tag :defaults %&gt;
&lt;h1&gt;Ajax show&lt;/h1&gt;
&lt;%= periodically_call_remote :update =&gt; 'time_div',
                             :url =&gt; {:action =&gt; "time"},
                             :frequency =&gt; 1.0 %&gt;
&lt;div id='time_div'&gt;
&lt;/div&gt;</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

图 4 显示了结果：不需要用户干预，每隔一秒钟进行更新的时钟：
<a name="figure4"></a><strong>图 4. 用 Ajax 定期更新的时钟</strong>
<img src="http://www.ibm.com/developerworks/cn/java/j-cb12056/figure-4.jpg" alt="用 Ajax 定期更新的时钟" width="367" height="223" />

尽管 Rails 视图中的代码和不含 Ajax 的版本相似，但背后的代码却很不同：此版本使用 JavaScript 代替了 HTML。可以通过在浏览器中查看源代码看到清单 9 中的代码：
<a name="N1026B"></a><strong>清单 9. periodically_call_remote 的源代码</strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>&lt;script src="/javascripts/prototype.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="/javascripts/effects.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="/javascripts/dragdrop.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="/javascripts/controls.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="/javascripts/application.js?1159113688" type="text/javascript"&gt;&lt;/script&gt;
&lt;h1&gt;Ajax show&lt;/h1&gt;
&lt;script type="text/javascript"&gt;
//&lt;![CDATA[
new PeriodicalExecuter(function() {new Ajax.Updater(
   'time_div', '/ajax/time', {asynchronous:true, evalScripts:true})}, 1.0)
//]]&gt;
&lt;/script&gt;
&lt;div id='time_div'&gt;
&lt;/div&gt;</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

请一定注意这里发生的情况。您正在一个更高层的抽象之上有效地工作，而不是使用小块的自定义 JavaScript 片段，Ruby on Rails 模板系统使使用模型变得相当自然。

正如之前提到的那样，我正从控制器中直接呈现文本。这一简化使开始编程变得很容易，但却不能一直持续下去。视图应该处理显示，控制器应该在视图和模型间调度数据。这项设计技术叫做模型-视图-控制器（MVC），它使对视图或模型的更改更容易分隔开。为使这个应用程序符合 MVC，可以让 Rails 呈现默认视图，正如预料的那样，该内容将替代 <code>time-div</code> 之前的内容。请按照清单 10 更改 app/controllers/ajax_controller.rb 中的 <code>time</code> 方法：
<a name="N10284"></a><strong>清单 10. 重构</strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>def time
  @time = Time.now
end</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

请按照清单 11 更改 app/views/ajax/time.rhtml 中的视图：
<a name="N10292"></a><strong>清单 11. 使用视图呈现 Ajax 内容</strong>
<table width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<pre>&lt;p&gt;The current time is &lt;%=h @time %&gt;&lt;/p&gt;</pre>
</td>
</tr>
</tbody>
</table>
&nbsp;

控制器方法设置一个名为 <code>@time</code> 的实例变量。由于控制器什么都没明确地呈现出来，Rails 将 time.rhtml 视图呈现出来。<em>这种使用模型和呈现一个不含 Ajax 的视图完全一致。</em> 可以再一次看到，Rails 使开发人员不必考虑使用 Ajax 和不使用 Ajax 的应用程序间的区别。从传统的 Web 应用程序到 Ajax，该使用模型都惊人地相似。由于使用 Ajax 的成本如此之低，越来越多的 Rails 应用程序都开始利用 Ajax。
<div>

<hr />

</div>
<a href="http://www.ibm.com/developerworks/cn/java/j-cb12056/index.html#ibm-pcon">回页首</a>

<a name="N102A3"></a>Rails 中 Ajax 的其他用法

Rails Ajax 体验领域宽广且内容深刻 —— 我无法用单篇文章甚至一系列文章来概括其深刻的内容。我只能指出 Rails Ajax 支持可以解决其他一些问题。下面是 Rails 中 Ajax 的一些通常用法：
<ul>
	<li><strong>提交远程表单。</strong> 除了必须异步提交以外，Rails 中的 Ajax 表单和传统表单的执行方式完全一样。这意味着 Rails 中的 <code>Forms</code> 辅助标记让您必须指定一个要更新的 URL，执行可视化的效果，正如使用 <code>link_to_remote</code> 一样。正如 <code>link-to-remote</code> 扩展了 <code>link_to</code> 辅助方法一样，Rails <code>submit_to_remote</code> 扩展了一个 Rails <code>submit</code> 辅助方法。</li>
	<li><strong>执行复杂脚本。</strong> Rails 开发人员常常需要执行复杂的脚本，远不止更新单个 <code>div</code> 和执行效果那么简单。为此，Rails 提供 JavaScript 模板。用 JavaScript 模板，可以将任意 JavaScript 脚本作为 Ajax 请求的结果来执行。这些模板的一些常见用法（叫作 RJS 模板）为更新多个 <code>div</code>、处理表单验证和管理 Ajax 错误场景。</li>
	<li><strong>拼写补全。</strong> 您一定想基于数据库中的条目为您的用户提供拼写补全服务。例如，如果用户键入 <code>Bru</code>，我想让我的应用程序注意到数据库中 “Bruce Tate” 这个值。可以使用 Ajax 定期检查字段的更改，并根据用户键入的内容发送拼写补全建议。</li>
	<li><strong>动态构建复杂表单。</strong> 在业务领域里，常常需要查看部分已完成表单，然后才能知道用户应该完成哪个字段。例如，如果用户拥有一些特定种类的收入或费用，那么 1040EZ 纳税单是无效的。可以在这个过程中用 Ajax 更新表单。</li>
	<li><strong>拖放。</strong> 可以用 Rails 快速实现拖放支持，这比大多数其他框架要省力得多。</li>
</ul>
&nbsp;
<div>

<hr />

</div>
<a href="http://www.ibm.com/developerworks/cn/java/j-cb12056/index.html#ibm-pcon">回页首</a>

<a name="N10301"></a>结束语

Ajax 并不是没有问题。当 Ajax 运行良好时，整个体验会是激动人心的。但当运行不顺利时，您也许会发现对其进行调试是一个全新的领域，调试技术和工具还没有其他语言中那么成熟。Ruby on Rails 的确有一个核心优势：简单。Rails 包装器（加上很棒的社区支持）使得进入这一新环境变得简单，而且最初的投资非常低。但 Rails 支持也仅限于此。这两个框架还没有覆盖 Ajax 的全部内容，无法满足每个用户的需求。

Java 语言有更多的 Ajax 框架和方法可供选择。可以找到更具灵活性的，也可以找到具有很棒的支持基础的。但灵活性是要付出代价的。您不仅需要一个强大的 Ajax 框架，也需要一个 Web 开发框架。例如，集成 JSF 框架和集成 Struts 是两种截然不同的体验。新技术通常追求简单。对于那些在 UI 中需要 Ajax 的出色特性，却又不需要由 Java 提供的高级企业集成特性的问题，Ajax on Rails 也许正合适。下一次，我将更深入地介绍 JavaScript。请继续关注跨越边界。

&nbsp;

<a name="resources"></a>参考资料

<strong>学习</strong>
<ul>
	<li>您可以参阅本文在 developerWorks 全球站点上的 <a href="http://www.ibm.com/developerworks/java/library/j-cb12056/?S_TACT=105AGX52&amp;S_CMP=cn-a-j" target="_blank">英文原文</a> 。</li>
	<li><a href="http://www.pragmaticprogrammer.com/titles/fr_j2r/index.html"><em>Java To Ruby: Things Every Manager Should Know</em> </a>（Pragmatic Bookshelf，2006 年）：这是本文作者编著的一本书，讲述何时何处从 Java 编程转到 Ruby on Rails 以及如何完成这种转变。</li>
	<li><a href="http://www.oreilly.com/catalog/beyondjava/index.html"><em>Beyond Java</em> </a>（O’Reilly，2005 年）：本文作者编写的一本书，讲述 Java 语言的提高和稳步发展，以及在某些方面能够挑战 Java 平台的技术。</li>
	<li>“<a href="http://www.ibm.com/developerworks/rational/library/dec05/reader/torpey.html?S_TACT=105AGX52&amp;S_CMP=cn-a-j">Book review: <em>Agile Web Development with Rails</em> </a>”（Darren Torpey，developerWorks，2005 年 5 月）：这里介绍的这本书可以加深读者对 Rails 及敏捷开发方式原理的理解。</li>
	<li>“<a href="http://adaptivepath.com/publications/essays/archives/000385.php">Ajax: A New Approach to Web Applications</a>”（Jesse James Garrett，Adaptive Path，2005 年 2 月）：“Ajax” 这一术语的发明者 Garrett 描述了其基本结构和用户体验特征。</li>
	<li><a href="http://script.aculo.us/">Scriptaculous</a> 和 <a href="http://prototype.conio.net/">Prototype</a>：增强 Rails Ajax 的两个 JavaScript 框架。</li>
	<li><a href="http://wiki.rubyonrails.org/rails/pages/How+to+use+the+Ajax+helpers">Rails Ajax helpers</a>：这些辅助方法让 Rails 开发人员不必考虑低层 Ajax 编程的复杂性。</li>
	<li><a href="http://www.ibm.com/developerworks/cn/ajax">Ajax 技术资源中心</a>：developerWorks Ajax 的门户，包含了专注于 Ajax 编程的内容、blog、论坛和其他资源。</li>
	<li><a href="http://www.ibm.com/developerworks/cn/web">Web 开发专区</a>：developerWorks Web 开发方面的资源中心，包含了专注于 Web 和 Web 2.0 开发的文章、教程和其他资源。</li>
	<li><a href="http://ajaxian.com/">Ajaxian</a>：这是一个很棒的 Ajax 门户，它提供对所有 Ajax 相关内容的讨论。</li>
	<li><a href="http://java.sun.com/blueprints/ajax.html">Ajax BluePrints</a>：对 Ajax 框架和 Java 编程语言设计技术的讨论。</li>
	<li><a href="http://www.pragmaticprogrammer.com/titles/ruby/index.html"><em>Programming Ruby</em> </a>（Dave Thomas et al.，Pragmatic Bookshelf，2005 年）：一本流行的关于 Ruby 编程的书。</li>
	<li><a href="http://www.ibm.com/developerworks/cn/java/">Java 技术专区</a>：数百篇关于 Java 编程各方面的文章。</li>
</ul>
<strong>获得产品和技术</strong>
<ul>
	<li><a href="http://rubyonrails.org/">Ruby on Rails</a>：下载开放源码的 Ruby on Rails Web 框架。</li>
	<li><a href="http://www.ruby-lang.org/en/20020102.html">Ruby</a>：从该项目的 Web 站点获取 Ruby。</li>
</ul>
