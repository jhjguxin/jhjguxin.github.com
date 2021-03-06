---
layout: post
title: "Layouts and Rendering in Rails"
date: 2011-12-08 21:12
comments: true
categories: [rails]
tags: [guide,layouts,rails,rendering,templates,view]
---
## Layouts and Rendering in Rails
<h2>Layouts and Rendering in Rails</h2>
This guide covers the basic layout features of Action Controller and Action View. By referring to this guide, you will be able to:

<span style="font-family: DejaVu Sans;">这个教程涵盖了</span>Action Controller and Action View<span style="font-family: DejaVu Sans;">的基础布局。通过这个教程，你可以了解到如下内容：</span>
<ul>
	<li>Use the various rendering methods built into Rails <span style="font-family: DejaVu Sans;">使用</span>Rails<span style="font-family: DejaVu Sans;">内建的各种渲染方法</span></li>
	<li>Create layouts with multiple content sections <span style="font-family: DejaVu Sans;">使用多个</span>content sections<span style="font-family: DejaVu Sans;">创建</span>layouts</li>
	<li>Use partials to DRY up your views <span style="font-family: DejaVu Sans;">在你的</span>view<span style="font-family: DejaVu Sans;">里面使用</span>partials</li>
	<li>Use nested layouts (sub-templates) <span style="font-family: DejaVu Sans;">使用嵌套</span>layouts<span style="font-family: DejaVu Sans;">（</span>sub-templates<span style="font-family: DejaVu Sans;">）</span></li>
</ul>
<h3><a name="overview-how-the-pieces-fit-together"></a> 1 Overview: How the Pieces Fit Together<span style="font-family: WenQuanYi Micro Hei;">概述：如何完整的组合在一起</span></h3>
This guide focuses on the interaction between Controller and View in the Model-View-Controller triangle. As you know, the Controller is responsible for orchestrating the whole process of handling a request in Rails, though it normally hands off any heavy code to the Model. But then, when it’s time to send a response back to the user, the Controller hands things off to the View. It’s that handoff that is the subject of this guide.

<span style="font-family: DejaVu Sans;">这个教程主要着重于在</span>model-view-controller<span style="font-family: DejaVu Sans;">三角（关系）中的</span>controller<span style="font-family: DejaVu Sans;">和</span>view<span style="font-family: DejaVu Sans;">之间的交互。众所周知，</span>controller<span style="font-family: DejaVu Sans;">是负责编排在</span>Rails<span style="font-family: DejaVu Sans;">处理请求的全过程，通过这样它一般可以让</span>model<span style="font-family: DejaVu Sans;">从沉重的代码中解放出来。但是接下来，什么时候是发回一个响应给用户呢，</span>controller<span style="font-family: DejaVu Sans;">把事情交给</span>view.<span style="font-family: DejaVu Sans;">这就是</span>handoff<span style="font-family: DejaVu Sans;">这也是这个教程的主题。</span>

In broad strokes, this involves deciding what should be sent as the response and calling an appropriate method to create that response. If the response is a full-blown view, Rails also does some extra work to wrap<span style="font-family: DejaVu Sans;">包裹</span>the view in a layout and possibly to pull in partial views. You’ll see all of those paths later in this guide.

<span style="font-family: DejaVu Sans;">在广泛的划分情况下，这涉及到决定应该发送什么作为响应或者调用一个适当的方法来创建一个响应。如果这个响应是一个</span>full-blown<span style="font-family: DejaVu Sans;">的</span>view<span style="font-family: DejaVu Sans;">，</span>Rails<span style="font-family: DejaVu Sans;">同样做一些额外的工作来在</span>layout<span style="font-family: DejaVu Sans;">中包装视图或者有可能的话调入其他部分</span>view<span style="font-family: DejaVu Sans;">。在后部分你将会看到完整的教程。</span>
<h3><a name="creating-responses"></a>2 Creating Responses<span style="font-family: WenQuanYi Micro Hei;">创建一个回应</span></h3>
From the controller’s point of view, there are three ways to create an HTTP response:

<span style="font-family: DejaVu Sans;">基于</span>controller<span style="font-family: DejaVu Sans;">的</span>view<span style="font-family: DejaVu Sans;">观点，这里有三种方法来创建一个</span>HTTP response<span style="font-family: DejaVu Sans;">：</span>
<ul>
	<li>Call <tt>render</tt> to create a full response to send back to the browser <span style="font-family: DejaVu Sans;">调用一个</span>render<span style="font-family: DejaVu Sans;">来创建以个完全回应</span>full response<span style="font-family: DejaVu Sans;">回传给浏览器。</span></li>
	<li>Call <tt>redirect_to</tt> to send an HTTP redirect status code to the browser <span style="font-family: DejaVu Sans;">调用一个</span>redirect_to<span style="font-family: DejaVu Sans;">来发送一个</span>HTTP<span style="font-family: DejaVu Sans;">重定向状态码给浏览器</span></li>
	<li>Call <tt>head</tt> to create a response consisting solely of HTTP headers to send back to the browser <span style="font-family: DejaVu Sans;">调用</span>head<span style="font-family: DejaVu Sans;">创建一个响应，只包括</span>HTTP<span style="font-family: DejaVu Sans;">头并将其发送回浏览器</span></li>
</ul>
I’ll cover each of these methods in turn. But first, a few words about the very easiest thing that the controller can do to create a response: nothing at all.

<span style="font-family: DejaVu Sans;">我将会依次（介绍）每种方法。但在此之前，用少量的文字说明最简单的事情<span style="font-family: Liberation Serif,Times New Roman,serif;">——</span></span>controller<span style="font-family: DejaVu Sans;">创建一个回应：什么都没有。</span>
<h4><a name="rendering-by-default-convention-over-con"></a> 2.1 Rendering by Default: Convention Over Configuration in Action<span style="font-family: WenQuanYi Micro Hei;">以默认方式渲染：</span>Action<span style="font-family: WenQuanYi Micro Hei;">约定优于配置</span></h4>
You’ve heard that Rails promotes “convention over configuration”. Default rendering is an excellent example of this. By default, controllers in Rails automatically render views with names that correspond to valid routes. For example, if you have this code in your <tt>BooksController</tt> class:

<span style="font-family: DejaVu Sans;">你已经听过了</span>Rails<span style="font-family: DejaVu Sans;">的原则<span style="font-family: Liberation Serif,Times New Roman,serif;">“</span>约定优于配置<span style="font-family: Liberation Serif,Times New Roman,serif;">”</span>。在这里默认的渲染是一个优秀的例子。<strong>默认情况中，</strong></span><strong>Rails</strong><span style="font-family: DejaVu Sans;"><strong>中的</strong></span><strong>controllers</strong><span style="font-family: DejaVu Sans;"><strong>自动渲染视图通过在</strong></span><strong>routes</strong><span style="font-family: DejaVu Sans;"><strong>中验证对应的视图名字</strong>。例如，如果你的（视图代码）在你的</span><tt>BooksController</tt><span style="font-family: DejaVu Sans;"><tt>类</tt>中：</span>

###rails generate scaffold Book title:string content:text

&nbsp;

class BooksController &lt; ApplicationController

end

And the following in your routes file:<span style="font-family: DejaVu Sans;">并且将下面的代码放入你的</span>routes<span style="font-family: DejaVu Sans;">文件中：</span>

resources :books

And you have a view file <tt>app/views/books/index.html.erb</tt>:

Rails will automatically render <tt>app/views/books/index.html.erb</tt> when you navigate to <tt>/books</tt> and you will see “Books are coming soon!” on your screen.

Rails<span style="font-family: DejaVu Sans;">将会自动渲染</span>render <tt>app/views/books/index.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>当你导航到</tt></span><tt>/books</tt><span style="font-family: DejaVu Sans;"><tt>并且你将会看到在你的屏幕上会出现</tt><tt><span style="font-family: Liberation Serif,Times New Roman,serif;">“</span></tt></span><tt>Books</tt><tt> </tt><tt>are</tt><tt> </tt><tt>coming</tt><tt> </tt><tt>soon!</tt><tt>”</tt><span style="font-family: DejaVu Sans;"><tt>。</tt></span>

<tt>However</tt><tt> </tt><tt>a</tt><tt> </tt><tt>coming</tt><tt> </tt><tt>soon</tt><tt> </tt><tt>screen</tt><tt> </tt><tt>is</tt><tt> </tt><tt>only</tt><tt> </tt><tt>minimally</tt><tt> </tt><tt>useful,</tt><tt> </tt><tt>so</tt><tt> </tt><tt>you</tt><tt> </tt><tt>will</tt><tt> </tt><tt>soon</tt><tt> </tt><tt>create</tt><tt> </tt><tt>your</tt><tt> </tt><tt>Book</tt><tt> </tt><tt>model</tt><tt> </tt><tt>and</tt><tt> </tt><tt>add</tt><tt> </tt><tt>the</tt><tt> </tt><tt>index</tt><tt> </tt><tt>action</tt><tt> </tt><tt>to</tt><tt> </tt><tt>BooksController:</tt>

<span style="font-family: DejaVu Sans;"><tt>无论如何</tt><tt></tt></span><tt>a</tt><tt> </tt><tt>coming</tt><tt> </tt><tt>soon</tt><tt> </tt><tt>screen</tt><span style="font-family: DejaVu Sans;"><tt>只是一个很小的用途，因此接着很快你将会创建你的</tt></span><tt>Book</tt><span style="font-family: DejaVu Sans;"><tt>模型并且添加</tt></span><tt>index</tt><tt> </tt><tt>action</tt><span style="font-family: DejaVu Sans;"><tt>到</tt><tt></tt></span><tt>BooksController</tt><span style="font-family: DejaVu Sans;"><tt>：</tt></span>

<tt>class</tt><tt> </tt><tt>BooksController</tt><tt> </tt><tt>&lt;</tt><tt> </tt><tt>ApplicationController</tt>

<tt> </tt><tt>def</tt><tt> </tt><tt>index</tt>

<tt> </tt><tt>@books</tt><tt> </tt><tt>=</tt><tt> </tt><tt>Book.all</tt>

<tt> </tt><tt>end</tt>

<tt>end</tt>

<tt>Note</tt><tt> </tt><tt>that</tt><tt> </tt><tt>we</tt><tt> </tt><tt>don</tt><tt>’</tt><tt>t</tt><tt> </tt><tt>have</tt><tt> </tt><tt>explicit</tt><tt> </tt><tt>render</tt><tt> </tt><tt>at</tt><tt> </tt><tt>the</tt><tt> </tt><tt>end</tt><tt> </tt><tt>of</tt><tt> </tt><tt>the</tt><tt> </tt><tt>index</tt><tt> </tt><tt>action</tt><tt> </tt><tt>in</tt><tt> </tt><span style="font-family: DejaVu Sans;"><tt>按照</tt></span><tt>accordance</tt><tt> </tt><tt>with</tt><tt> “</tt><tt>convention</tt><tt> </tt><tt>over</tt><tt> </tt><tt>configuration</tt><tt>” </tt><tt>principle.</tt><tt> </tt><tt>The</tt><tt> </tt><tt>rule</tt><tt> </tt><tt>is</tt><tt> </tt><tt>that</tt><tt> </tt><tt>if</tt><tt> </tt><tt>you</tt><tt> </tt><tt>do</tt><tt> </tt><tt>not</tt><tt> </tt><tt>explicitly</tt><tt> </tt><tt>render</tt><tt> </tt><tt>something</tt><tt> </tt><tt>at</tt><tt> </tt><tt>the</tt><tt> </tt><tt>end</tt><tt> </tt><tt>of</tt><tt> </tt><tt>a</tt><tt> </tt><tt>controller</tt><tt> </tt><tt>action,</tt><tt> </tt><tt>Rails</tt><tt> </tt><tt>will</tt><tt> </tt><tt>automatically</tt><tt> </tt><tt>look</tt><tt> </tt><tt>for</tt><tt> </tt><tt>the</tt><tt> </tt><tt>action_name.html.erb</tt><tt> </tt><tt>template</tt><tt> </tt><tt>in</tt><tt> </tt><tt>the</tt><tt> </tt><tt>controller</tt><tt>’</tt><tt>s</tt><tt> </tt><tt>view</tt><tt> </tt><tt>path</tt><tt> </tt><tt>and</tt><tt> </tt><tt>render</tt><tt> </tt><tt>it.</tt><tt> </tt><tt>So</tt><tt> </tt><tt>in</tt><tt> </tt><tt>this</tt><tt> </tt><tt>case,</tt><tt> </tt><tt>Rails</tt><tt> </tt><tt>will</tt><tt> </tt><tt>render</tt><tt> </tt><tt>the</tt><tt> </tt><tt>app/views/books/index.html.erb</tt><tt> </tt><tt>file.</tt>

<span style="font-family: DejaVu Sans;">注意按照<span style="font-family: Liberation Serif,Times New Roman,serif;">“</span>约定优于配置<span style="font-family: Liberation Serif,Times New Roman,serif;">”</span>原则，在</span>index action<span style="font-family: DejaVu Sans;">结束的时候，我们没有明确的渲染。这里的规则是如果你在使用一个</span>controller action<span style="font-family: DejaVu Sans;">结束的时候没有明确的</span>render<span style="font-family: DejaVu Sans;">一些事物，</span>Rails<span style="font-family: DejaVu Sans;">将会自动的在</span>controller<span style="font-family: DejaVu Sans;">的视图目录中找到</span><tt>action_name.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>文件并渲染它。因此在这个例子中，</tt></span><tt>Rails</tt><span style="font-family: DejaVu Sans;"><tt>将会渲染</tt><tt></tt></span><tt>app/views/books/index.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>文件。</tt></span>

If we want to display the properties of all the books in our view, we can do so with an ERB template like this:

<span style="font-family: DejaVu Sans;">如果我们想在我们的视图中显示所有</span>books<span style="font-family: DejaVu Sans;">的属性，我们可以这样做使用一个如下的</span>ERB<span style="font-family: DejaVu Sans;">模板：</span>

&lt;h1&gt;Listing Books&lt;/h1&gt;

&nbsp;

&lt;table&gt;

&lt;tr&gt;

&lt;th&gt;Title&lt;/th&gt;

&lt;th&gt;Summary&lt;/th&gt;

&lt;th&gt;&lt;/th&gt;

&lt;th&gt;&lt;/th&gt;

&lt;th&gt;&lt;/th&gt;

&lt;/tr&gt;

&nbsp;

&lt;% @books.each do |book| %&gt;

&lt;tr&gt;

&lt;td&gt;&lt;%= book.title %&gt;&lt;/td&gt;

&lt;td&gt;&lt;%= book.content %&gt;&lt;/td&gt;

&lt;td&gt;&lt;%= link_to 'Show', book %&gt;&lt;/td&gt;

&lt;td&gt;&lt;%= link_to 'Edit', edit_book_path(book) %&gt;&lt;/td&gt;

&lt;td&gt;&lt;%= link_to 'Remove', book, :confirm =&gt; 'Are you sure?', :method =&gt; :delete %&gt;&lt;/td&gt;

&lt;/tr&gt;

&lt;% end %&gt;

&lt;/table&gt;

&nbsp;

&lt;br /&gt;

&nbsp;

&lt;%= link_to 'New book', new_book_path %&gt;

&nbsp;

The actual rendering is done by subclasses of <tt>ActionView::TemplateHandlers</tt>. This guide does not dig into that process, but it’s important to know that the file extension on your view controls the choice of template handler. In Rails 2, the standard extensions are <tt>.erb</tt> for ERB (HTML with embedded Ruby), and <tt>.builder</tt> for Builder (XML generator).

actual<span style="font-family: DejaVu Sans;">渲染是通过</span><tt>ActionView::TemplateHandlers</tt><span style="font-family: DejaVu Sans;"><tt>的子类完成的。这个教程不会（深入）挖掘那个过程，但是知道一些文件存在在你的</tt></span><tt>view</tt><tt> </tt><tt>controls</tt><span style="font-family: DejaVu Sans;"><tt>，被选择用来做</tt></span><tt>template</tt><tt> </tt><tt>handler</tt><span style="font-family: DejaVu Sans;"><tt>这很重要。在</tt></span><tt>Rails2</tt><span style="font-family: DejaVu Sans;"><tt>中</tt></span><tt>ERB</tt><span style="font-family: DejaVu Sans;"><tt>（</tt></span><tt>HTML</tt><tt> </tt><tt>with</tt><tt> </tt><tt>embedded</tt><tt> </tt><tt>Ruby</tt><span style="font-family: DejaVu Sans;"><tt>）标准的扩展名是</tt></span><tt>.erb</tt><span style="font-family: DejaVu Sans;"><tt>，并且</tt></span><tt>.builder</tt><span style="font-family: DejaVu Sans;"><tt>是</tt></span><tt>Builder</tt><span style="font-family: DejaVu Sans;"><tt>（</tt></span><tt>XML</tt><span style="font-family: DejaVu Sans;"><tt>生成器）的扩展名。</tt></span>
<h4><a name="using-render"></a>2.2 Using <tt>render</tt></h4>
In most cases, the <tt>ActionController::Base#render</tt> method does the heavy lifting of rendering your application’s content for use by a browser. There are a variety of ways to customize the behaviour of <tt>render</tt>. You can render the default view for a Rails template, or a specific template, or a file, or inline code, or nothing at all. You can render text, JSON, or XML. You can specify the content type or HTTP status of the rendered response as well.

<span style="font-family: DejaVu Sans;">在大多数例子中，</span><tt>ActionController::Base#render</tt><span style="font-family: DejaVu Sans;"><tt>方法通过浏览器将你的应用程序的内容生动的</tt></span><tt>rendering</tt><span style="font-family: DejaVu Sans;"><tt>给用户。这里有多种方法来定制</tt></span><tt>render</tt><span style="font-family: DejaVu Sans;"><tt>的习惯。你可以</tt></span><tt>render</tt><span style="font-family: DejaVu Sans;"><tt>一个</tt></span><tt>Rails</tt><tt> </tt><tt>template</tt><span style="font-family: DejaVu Sans;"><tt>为默认的视图，或者指定一个</tt></span><tt>template</tt><span style="font-family: DejaVu Sans;"><tt>，或者一个文件，或者一个内联代码，或者什么都没有。你可以</tt></span><tt>render</tt><tt> </tt><tt>text</tt><span style="font-family: DejaVu Sans;"><tt>，</tt></span><tt>JSON</tt><span style="font-family: DejaVu Sans;"><tt>，或者</tt></span><tt>XML</tt><span style="font-family: DejaVu Sans;"><tt>。你可以指定上下文类型或者</tt></span><tt>HTTP</tt><span style="font-family: DejaVu Sans;"><tt>回应</tt></span><tt>rendered</tt><span style="font-family: DejaVu Sans;"><tt>的状态。</tt></span>

If you want to see the exact results of a call to <tt>render</tt> without needing to inspect<span style="font-family: DejaVu Sans;">检查</span>it in a browser, you can call <tt>render_to_string</tt>. This method takes exactly the same options as <tt>render</tt>, but it returns a string instead of sending a response back to the browser.

<span style="font-family: DejaVu Sans;">如果你想查看调用一个</span>render<span style="font-family: DejaVu Sans;">在浏览器中不需要检查的那些额外的结果。你可以调用</span>render_to_string<span style="font-family: DejaVu Sans;">。这个方法获取和</span>render<span style="font-family: DejaVu Sans;">完全的</span>options<span style="font-family: DejaVu Sans;">，但是它返回的一个字符串而不是发送一个</span>response<span style="font-family: DejaVu Sans;">回浏览器。</span>
<h5><a name="rendering-nothing"></a>2.2.1 Rendering Nothing</h5>
<strong>Perhaps</strong><strong> </strong><strong>the</strong><strong> </strong><strong>simplest</strong><strong> </strong><strong>thing</strong><strong> </strong><strong>you</strong><strong> </strong><strong>can</strong><strong> </strong><strong>do</strong><strong> </strong><strong>with</strong><strong> </strong><tt><strong>render</strong></tt><strong> </strong><strong>is</strong><strong> </strong><strong>to</strong><strong> </strong><strong>render</strong><strong> </strong><strong>nothing</strong><strong> </strong><strong>at</strong><strong> </strong><strong>all:</strong>

render :nothing =&gt; true

If you look at the response for this using cURL, you will see the following:

<span style="font-family: DejaVu Sans;">如果你使用</span>cURL<span style="font-family: DejaVu Sans;">查看回应，你将会看到如下内容：</span>

#curl -I 127.0.0.1:3000/books#<span style="font-family: DejaVu Sans;">此命令就是访问</span>HTTP status<span style="font-family: DejaVu Sans;">的</span>

$ curl -i 127.0.0.1:3000/books

HTTP/1.1 200 OK

Connection: close

Date: Sun, 24 Jan 2010 09:25:18 GMT

Transfer-Encoding: chunked

Content-Type: */*; charset=utf-8

X-Runtime: 0.014297

Set-Cookie: _blog_session=...snip...; path=/; HttpOnly

Cache-Control: max-age=0, private, must-revalidate

We see there is an empty response (no data after the <tt>Cache-Control</tt> line), but the request was successful because Rails has set the response to 200 OK. <strong>You</strong><strong> </strong><strong>can</strong><strong> </strong><strong>set</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>:status</strong></tt><strong> </strong><strong>option</strong><strong> </strong><strong>on</strong><strong> </strong><strong>render</strong><strong> </strong><strong>to</strong><strong> </strong><strong>change</strong><strong> </strong><strong>this</strong><strong> </strong><strong>response.</strong><strong> </strong><strong>Rendering</strong><strong> </strong><strong>nothing</strong><strong> </strong><strong>can</strong><strong> </strong><strong>be</strong><strong> </strong><strong>useful</strong><strong> </strong><strong>for</strong><strong> </strong><strong>AJAX</strong><strong> </strong><strong>requests</strong><strong> </strong><strong>where</strong><strong> </strong><strong>all</strong><strong> </strong><strong>you</strong><strong> </strong><strong>want</strong><strong> </strong><strong>to</strong><strong> </strong><strong>send</strong><strong> </strong><strong>back</strong><strong> </strong><strong>to</strong><strong> </strong><strong>the</strong><strong> </strong><strong>browser</strong><strong> </strong><strong>is</strong><strong> </strong><strong>an</strong><strong> </strong><strong>acknowledgment</strong><strong> </strong><strong>that</strong><strong> </strong><strong>the</strong><strong> </strong><strong>request</strong><strong> </strong><strong>was</strong><strong> </strong><strong>completed.</strong>

def index

&nbsp;

render :nothing =&gt; true,:status=&gt;500

&nbsp;

end

jhjguxin@jhjguxin-virtual-machine:~/blog$ curl -I 127.0.0.1:3000/books

&nbsp;

HTTP/1.1 300 Multiple Choices

HTTP/1.1 500 Internal Server Error

&nbsp;

You should probably be using the <tt>head</tt> method, discussed later in this guide, instead of <tt>render</tt><tt> </tt><tt>:nothing</tt>. This provides additional<span style="font-family: DejaVu Sans;">额外</span>flexibility<span style="font-family: DejaVu Sans;">灵活的</span>and makes it <strong>explicit</strong><strong> </strong><strong>that</strong><strong> </strong><strong>you</strong><strong>’</strong><strong>re</strong><strong> </strong><strong>only</strong><strong> </strong><strong>generating</strong><strong> </strong><strong>HTTP</strong><strong> </strong><strong>headers.</strong><span style="font-family: DejaVu Sans;"><strong>（使用</strong></span><strong>render</strong><strong> </strong><strong>:nothing</strong><span style="font-family: DejaVu Sans;"><strong>提供了额外的灵活性它能够准确的只创建</strong></span><strong>HTTP</strong><strong> </strong><strong>headers</strong><span style="font-family: DejaVu Sans;"><strong>）</strong></span>
<h5><a name="rendering-an-action-s-view"></a>2.2.2 Rendering an Action’s View<span style="font-family: WenQuanYi Micro Hei;">渲染一个动作的视图</span></h5>
If you want to render the view that corresponds to a different action within the same template, you can use <tt>render</tt> with the name of the view:

<span style="font-family: DejaVu Sans;">如果你想</span>render<span style="font-family: DejaVu Sans;">的视图对应着不同的</span>action<span style="font-family: DejaVu Sans;">在一个相同的</span>template<span style="font-family: DejaVu Sans;">中，你可以使用</span>render<span style="font-family: DejaVu Sans;">加上视图的名字。</span>

def update

@book = Book.find(params[:id])

if @book.update_attributes(params[:book])

redirect_to(@book)

else

render "edit"

end

end

If the call to <tt>update_attributes</tt> fails, calling the <tt>update</tt> action in this controller will render the <tt>edit.html.erb</tt> template belonging to the same controller.

If you prefer<span style="font-family: DejaVu Sans;">如果你喜欢</span>, you can use <strong>a</strong><strong> </strong><strong>symbol</strong><strong> </strong><strong>instead</strong><strong> </strong><strong>of</strong><strong> </strong><strong>a</strong><strong> </strong><strong>string</strong><strong> </strong><strong>to</strong><strong> </strong><strong>specify</strong><strong> </strong><strong>the</strong><strong> </strong><strong>action</strong><strong> </strong><strong>to</strong><strong> </strong><strong>render</strong>:

def update

@book = Book.find(params[:id])

if @book.update_attributes(params[:book])

redirect_to(@book)

else

render :edit

end

end

To be explicit, you can use <tt>render</tt> with the <tt>:action</tt> option (though this is no longer necessary in Rails 3.0):

def update

@book = Book.find(params[:id])

if @book.update_attributes(params[:book])

redirect_to(@book)

else

render :action =&gt; "edit"

end

end

Using <tt>render</tt> with <tt>:action</tt> is a frequent source of confusion for Rails newcomers. The specified action is used to determine which view to render, but Rails does not run any of the code for that action in the controller. Any instance variables that you require in the view must be set up in the current action before calling <tt>render</tt>.

<span style="font-family: DejaVu Sans;">使用</span>render<span style="font-family: DejaVu Sans;">和</span>:action<span style="font-family: DejaVu Sans;">是来（处理）来自</span>Rails<span style="font-family: DejaVu Sans;">的新访客的频繁混乱的（请求）。指定的</span>action<span style="font-family: DejaVu Sans;">是用来决定渲染哪个视图，但是</span>Rails<span style="font-family: DejaVu Sans;">并没有在</span>controller<span style="font-family: DejaVu Sans;">中为那个</span>action<span style="font-family: DejaVu Sans;">运行任何代码。不管在视图中的你请求的何种实例必须在调用</span>render<span style="font-family: DejaVu Sans;">之前被</span>set up<span style="font-family: DejaVu Sans;">到当前</span>action<span style="font-family: DejaVu Sans;">中。</span>
<h5><a name="rendering-an-action-s-template-from-anot"></a> 2.2.3 Rendering an Action’s Template from Another Controller<span style="font-family: WenQuanYi Micro Hei;">从另一个</span>controller<span style="font-family: WenQuanYi Micro Hei;">渲染一个</span>actions<span style="font-family: WenQuanYi Micro Hei;">的</span>template</h5>
What if you want to render a template from an entirely different controller from the one that contains the action code? You can also do that with <tt>render</tt>, which accepts the full path (relative to <tt>app/views</tt>) of the template to render. For example, if you’re running code in an <tt>AdminProductsController</tt> that lives in <tt>app/controllers/admin</tt>, you can render the results of an action to a template in <tt>app/views/products</tt> this way:

<span style="font-family: DejaVu Sans;">如果你想</span>render<span style="font-family: DejaVu Sans;">一个</span>template<span style="font-family: DejaVu Sans;">它的</span>action<span style="font-family: DejaVu Sans;">来自完全不同的</span>controller<span style="font-family: DejaVu Sans;">？你也可以使用</span>render<span style="font-family: DejaVu Sans;">来（达到目的），它接收</span>template<span style="font-family: DejaVu Sans;">的全路径（相对于</span>app/views#<span style="font-family: DejaVu Sans;">项目路径为根目录）来</span>render<span style="font-family: DejaVu Sans;">。</span>

<span style="font-family: DejaVu Sans;">例如，你正在运行的代码在</span><tt>AdminProductsController</tt><span style="font-family: DejaVu Sans;"><tt>存放在</tt><tt></tt></span><tt>app/controllers/admin</tt><span style="font-family: DejaVu Sans;"><tt>中你可以</tt></span><tt>render</tt><span style="font-family: DejaVu Sans;"><tt>一个</tt></span><tt>action</tt><span style="font-family: DejaVu Sans;"><tt>的结果到</tt></span><tt>app/views/products</tt><tt> </tt><span style="font-family: DejaVu Sans;"><tt>的一个</tt></span><tt>template</tt><span style="font-family: DejaVu Sans;"><tt>中用这样的方法：</tt></span><tt>
render</tt><tt> </tt><tt>'products/show'</tt>

Rails knows that this view belongs to a different controller because of the embedded slash character in the string. If you want to be explicit<span style="font-family: DejaVu Sans;">如果你想准确的表达</span>, you can use the <tt>:template</tt> option (which was required on Rails 2.2 and earlier):

render :template =&gt; 'products/show'
<h5><a name="rendering-an-arbitrary-file"></a>2.2.4 Rendering an Arbitrary File<span style="font-family: WenQuanYi Micro Hei;">渲染任意文件</span></h5>
The <tt>render</tt> method can also use a view that’s entirely outside of your application (perhaps you’re sharing views between two Rails applications):

render<span style="font-family: DejaVu Sans;">方法也可以使用一个完全脱离你的</span>application<span style="font-family: DejaVu Sans;">的视图（可能你在两个</span>Rails<span style="font-family: DejaVu Sans;">之间共享视图）</span>

render "/u/apps/warehouse_app/current/app/views/products/show"

Rails determines that this is a file render because of the leading slash character<span style="font-family: DejaVu Sans;">斜线字符</span>. To be explicit, you can use the <tt>:file</tt> option (which was required on Rails 2.2 and earlier):

Rails<span style="font-family: DejaVu Sans;">认为这个是一个文件</span>render<span style="font-family: DejaVu Sans;">是因为开头的斜线字符。更准确的（表示），你可以使用</span>:file<span style="font-family: DejaVu Sans;">选项（它至少需要</span>Rails2.2<span style="font-family: DejaVu Sans;">或者更高）。</span>

render :file =&gt;

"/u/apps/warehouse_app/current/app/views/products/show"

The <tt>:file</tt> option takes an absolute file-system path. Of course, you need to have rights to the view that you’re using to render the content.

:file<span style="font-family: DejaVu Sans;">选项获取一个完全文件系统路径。当然，你需要有权限（查看）你正在渲染的视图。</span>

By default, the file is rendered without using the current layout. If you want Rails to put the file into the current layout, you need to add the <tt>:layout</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt> option.

<span style="font-family: DejaVu Sans;">默认情况下，这个文件没有使用当前的</span>layout<span style="font-family: DejaVu Sans;">来</span>render<span style="font-family: DejaVu Sans;">。如果你想</span>Rails<span style="font-family: DejaVu Sans;">把这个文件渲染到当前</span>layout<span style="font-family: DejaVu Sans;">，你需要添加</span>:layout=&gt;true<span style="font-family: DejaVu Sans;">选项。</span>

&nbsp;

If you’re running Rails on Microsoft Windows, you should use the <tt>:file</tt> option to render a file, because Windows filenames do not have the same format as Unix filenames.<span style="font-family: DejaVu Sans;">如果你正在</span>Microsoft Windows<span style="font-family: DejaVu Sans;">中运行</span>Rails<span style="font-family: DejaVu Sans;">，你应该使用</span>:file<span style="font-family: DejaVu Sans;">选项来</span>render<span style="font-family: DejaVu Sans;">一个文件，因为</span>Windows<span style="font-family: DejaVu Sans;">文件名称与</span>Unix<span style="font-family: DejaVu Sans;">文件名称并不相同（这里的文件名称应该包含了路径）。</span>
<h5><a name="wrapping-it-up"></a>2.2.5 Wrapping it up<span style="font-family: WenQuanYi Micro Hei;">包装起来</span></h5>
The above three ways of rendering (rendering another template within the controller, rendering a template within another controller and rendering an arbitrary file on the file system) are actually variants of the same action.

<span style="font-family: DejaVu Sans;">这里总共有三种</span>rendering<span style="font-family: DejaVu Sans;">的方法（在</span>controller<span style="font-family: DejaVu Sans;">中</span>rendering<span style="font-family: DejaVu Sans;">另一个</span>template<span style="font-family: DejaVu Sans;">，使用另一个</span>controller render<span style="font-family: DejaVu Sans;">一个</span>template<span style="font-family: DejaVu Sans;">或者渲染一个文件系统上的任意类型的文件）实际上它们是相同</span>action<span style="font-family: DejaVu Sans;">的变种。</span>

In fact, in the BooksController class, inside of the update action where we want to render the edit template if the book does not update successfully, all of the following render calls would all render the <tt>edit.html.erb</tt> template in the <tt>views/books</tt> directory:

<span style="font-family: DejaVu Sans;">事实上，在</span>BooksController<span style="font-family: DejaVu Sans;">类中，在</span>update action<span style="font-family: DejaVu Sans;">中如果</span>book<span style="font-family: DejaVu Sans;">没有更新成功的话我们希望</span>render edit template<span style="font-family: DejaVu Sans;">，所有的后续</span>render<span style="font-family: DejaVu Sans;">调用将会全部</span>render<span style="font-family: DejaVu Sans;">在</span><tt>views/books</tt><span style="font-family: DejaVu Sans;"><tt>目录中的</tt></span><tt>edit.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>：</tt></span><tt>(edit</tt><span style="font-family: DejaVu Sans;"><tt>的各种</tt></span><tt>render</tt><span style="font-family: DejaVu Sans;"><tt>方式</tt></span><tt>)</tt>

render :edit

render :action =&gt; :edit

render 'edit'

render 'edit.html.erb'

render :action =&gt; 'edit'

render :action =&gt; 'edit.html.erb'

render 'books/edit'

render 'books/edit.html.erb'

render :template =&gt; 'books/edit'

render :template =&gt; 'books/edit.html.erb'

render '/path/to/rails/app/views/books/edit'

render '/path/to/rails/app/views/books/edit.html.erb'

render :file =&gt; '/path/to/rails/app/views/books/edit'

render :file =&gt; '/path/to/rails/app/views/books/edit.html.erb'

Which one you use is really a matter of style and convention, but the rule of thumb is to use the simplest one that makes sense for the code you are writing.

<span style="font-family: DejaVu Sans;">你实际使用的格式与风格和公约有关，但一般规则是使用最简单的一个，使你正在编写的代码的意义。</span>
<h5><a name="using-render-with-inline"></a>2.2.6 Using <tt>render</tt> with <tt>:inline</tt><span style="font-family: WenQuanYi Micro Hei;"><tt>（使用）</tt></span><tt>:inline</tt><span style="font-family: WenQuanYi Micro Hei;"><tt>和</tt></span><tt>render</tt></h5>
The <tt>render</tt> method can do without a view completely, if you’re willing to use the <tt>:inline</tt> option to supply ERB as part of the method call. This is perfectly valid:

render<span style="font-family: DejaVu Sans;">方法可以完全没有视图而（单独）使用，如果你使用</span>:inline<span style="font-family: DejaVu Sans;">选项来提供</span>ERB<span style="font-family: DejaVu Sans;">（</span>ERB<span style="font-family: DejaVu Sans;">标签）作为方法调用（的参数）。这完全合法的：</span>

render :inline =&gt;

"&lt;% products.each do |p| %&gt;&lt;p&gt;&lt;%= p.name %&gt;&lt;/p&gt;&lt;% end %&gt;"

&nbsp;

There is seldom<span style="font-family: DejaVu Sans;">很少</span>any good reason to use this option. Mixing ERB into your controllers defeats the MVC orientation of Rails and will make it harder for other developers to follow the logic of your project. Use a separate<span style="font-family: DejaVu Sans;">单独</span>erb view instead.

<span style="font-family: DejaVu Sans;">（很少有好的理由来使用这个选项）。在你的</span>controllers<span style="font-family: DejaVu Sans;">中复杂的</span>ERB<span style="font-family: DejaVu Sans;">使得你的</span>Rails<span style="font-family: DejaVu Sans;">（应用程序）在</span>MVC<span style="font-family: DejaVu Sans;">方面很失败，并且使得其他开发人员很难跟随你项目的逻辑。（最好）使用单个</span>erb<span style="font-family: DejaVu Sans;">视图替代。</span>

&nbsp;

By default, inline rendering uses ERB. You can force it to use Builder instead with the <tt>:type</tt> option:

<span style="font-family: DejaVu Sans;">默认情况下，</span>inline rendering<span style="font-family: DejaVu Sans;">使用</span>ERB<span style="font-family: DejaVu Sans;">。你可以使用创建器强制替代它通过</span>:type<span style="font-family: DejaVu Sans;">选项：</span>

render :inline =&gt;

"xml.p {'Horrid coding practice!'}", :type =&gt; :builder
<h5><a name="rendering-text"></a>2.2.7 Rendering Text<span style="font-family: WenQuanYi Micro Hei;">渲染文本</span></h5>
You can send plain text – with no markup at all – back to the browser by using the <tt>:text</tt> option to <tt>render</tt>:

<span style="font-family: DejaVu Sans;">你可以发送纯文本<span style="font-family: Liberation Serif,Times New Roman,serif;">——</span>没有做任何标记<span style="font-family: Liberation Serif,Times New Roman,serif;">——</span>返回到浏览器通过在</span>render<span style="font-family: DejaVu Sans;">中使用</span>:text<span style="font-family: DejaVu Sans;">选项：</span>

render :text =&gt; "OK"

Rendering pure text is most useful when you’re responding to AJAX or web service requests that are expecting something other than proper HTML.

Rendering<span style="font-family: DejaVu Sans;">纯文本在你回应到</span>AJAZ<span style="font-family: DejaVu Sans;">或者</span>web service<span style="font-family: DejaVu Sans;">请求的时候非常有用（因为）此时用户期待适当的</span>HTML<span style="font-family: DejaVu Sans;">以外的东西。</span>

&nbsp;

By default, if you use the <tt>:text</tt> option, the text is rendered without using the current layout. If you want Rails to put the text into the current layout, you need to add the <tt>:layout</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt> option.

<span style="font-family: DejaVu Sans;">作为默认的，如果你是用</span>:text<span style="font-family: DejaVu Sans;">选项，</span>text<span style="font-family: DejaVu Sans;">并没有被</span>render<span style="font-family: DejaVu Sans;">到当前</span>layout<span style="font-family: DejaVu Sans;">。如果你希望</span>Rails<span style="font-family: DejaVu Sans;">放置</span>text<span style="font-family: DejaVu Sans;">到当前的</span>layout<span style="font-family: DejaVu Sans;">，你需要添加</span>:layout=&gt;true<span style="font-family: DejaVu Sans;">选项。</span>
<h5><a name="rendering-json"></a>2.2.8 Rendering JSON<span style="font-family: WenQuanYi Micro Hei;">渲染</span>JSON</h5>
JSON is a JavaScript data format used by many AJAX libraries. Rails has built-in support for converting objects to JSON and rendering that JSON back to the browser:

JSON<span style="font-family: DejaVu Sans;">是</span>JavaScript<span style="font-family: DejaVu Sans;">数据格式它被很多</span>AJAX<span style="font-family: DejaVu Sans;">库使用。</span>Rails<span style="font-family: DejaVu Sans;">内建支持转换对象成</span>JSON<span style="font-family: DejaVu Sans;">并且</span>rendering<span style="font-family: DejaVu Sans;">这些</span>JSON<span style="font-family: DejaVu Sans;">反馈给浏览器：</span>

render :json =&gt; @product

You don’t need to call <tt>to_json</tt> on the object that you want to render. If you use the <tt>:json</tt> option, <tt>render</tt> will automatically call <tt>to_json</tt> for you.

<span style="font-family: DejaVu Sans;">你不必对你</span>render<span style="font-family: DejaVu Sans;">的</span>object<span style="font-family: DejaVu Sans;">调用</span>to_json<span style="font-family: DejaVu Sans;">。如果你使用</span>:json<span style="font-family: DejaVu Sans;">选项，</span>render<span style="font-family: DejaVu Sans;">将会自动为你调用</span>to_json<span style="font-family: DejaVu Sans;">。</span>
<h5><a name="rendering-xml"></a>2.2.9 Rendering XML</h5>
Rails also has built-in support for converting objects to XML and rendering that XML back to the caller:

render :xml =&gt; @product

You don’t need to call <tt>to_xml</tt> on the object that you want to render. If you use the <tt>:xml</tt> option, <tt>render</tt> will automatically call <tt>to_xml</tt> for you.
<h5><a name="rendering-vanilla-javascript"></a>2.2.10 Rendering Vanilla JavaScript<span style="font-family: WenQuanYi Micro Hei;">渲染香草味的的</span>JavaScript</h5>
Rails can render vanilla JavaScript:

render :js =&gt; "alert('Hello Rails');"

<strong>This</strong><strong> </strong><strong>will</strong><strong> </strong><strong>send</strong><strong> </strong><strong>the</strong><strong> </strong><strong>supplied</strong><strong> </strong><strong>string</strong><strong> </strong><strong>to</strong><strong> </strong><strong>the</strong><strong> </strong><strong>browser</strong><strong> </strong><strong>with</strong><strong> </strong><strong>a</strong><strong> </strong><strong>MIME</strong><strong> </strong><strong>type</strong><strong> </strong><strong>of</strong><strong> </strong><tt><strong>text/javascript</strong></tt><strong>.</strong>
<h5><a name="options-for-render"></a>2.2.11 Options for <tt>render</tt></h5>
Calls to the <tt>render</tt> method generally accept four options:
<ul>
	<li><tt>:content_type</tt></li>
	<li><tt>:layout</tt></li>
	<li><tt>:status</tt></li>
	<li><tt>:location</tt></li>
</ul>
<h6><a name="the-content_type-option"></a>2.2.11.1 The <tt>:content_type</tt> Option</h6>
By default, Rails will serve the results of a rendering operation with the MIME content-type of <tt>text/html</tt> (or <tt>application/json</tt> if you use the <tt>:json</tt> option, or <tt>application/xml</tt> for the <tt>:xml</tt> option.). There are times when you might like to change this, and you can do so by setting the <tt>:content_type</tt> option:

<span style="font-family: DejaVu Sans;">默认情况下，</span>Rails<span style="font-family: DejaVu Sans;">将会帮助渲染操作通过使用</span><tt>text/html</tt><span style="font-family: DejaVu Sans;"><tt>的</tt><tt></tt></span><tt>MIME</tt><tt> </tt><tt>content-type</tt><span style="font-family: DejaVu Sans;"><tt>（或者如果你使用</tt></span><tt>:json</tt><span style="font-family: DejaVu Sans;"><tt>选项则是</tt></span><tt>application/json</tt><span style="font-family: DejaVu Sans;"><tt>，或者如果是</tt></span><tt>:xml</tt><span style="font-family: DejaVu Sans;"><tt>选项</tt></span><tt>application/xml</tt><span style="font-family: DejaVu Sans;"><tt>）。有时你可以改成这样，你可以通过设置</tt></span><tt>:content_type</tt><span style="font-family: DejaVu Sans;"><tt>选项达成：</tt></span>

render :file =&gt; filename, :content_type =&gt; 'application/rss'
<h6><a name="the-layout-option"></a>2.2.11.2 The <tt>:layout</tt> Option</h6>
With most of the options to <tt>render</tt>, the rendered content is displayed as part of the current layout. You’ll learn more about layouts and how to use them later in this guide.

<span style="font-family: DejaVu Sans;">它是</span>render<span style="font-family: DejaVu Sans;">的常见的选项，通过这个选项被</span>rendered<span style="font-family: DejaVu Sans;">的内容被显示为当前</span>layout<span style="font-family: DejaVu Sans;">的一部分。在本教程的随后部分你将了解更多的</span>layouts<span style="font-family: DejaVu Sans;">的知识以及如何使用它们。</span>

You can use the <tt>:layout</tt> option to tell Rails to use a specific file as the layout for the current action:

render :layout =&gt; 'special_layout'

You can also tell Rails to render with no layout at all:

render :layout =&gt; false
<h6><a name="the-status-option"></a>2.2.11.3 The <tt>:status</tt> Option</h6>
Rails will automatically generate a response with the correct HTTP status code (in most cases, this is <tt>200</tt><tt> </tt><tt>OK</tt>). You can use the <tt>:status</tt> option to change this:

Rails<span style="font-family: DejaVu Sans;">将会自动生成（包含）正确的</span>HTTP<span style="font-family: DejaVu Sans;">状态码的响应（在大多数情况，</span>200 <span style="font-family: DejaVu Sans;">是</span>OK<span style="font-family: DejaVu Sans;">）。你可以使用</span>:status<span style="font-family: DejaVu Sans;">选项来更改这些</span>:

render :status =&gt; 500

#or

render :status =&gt; :forbidden

<strong>Rails</strong><strong> </strong><strong>understands</strong><strong> </strong><strong>both</strong><strong> </strong><strong>numeric</strong><strong> </strong><strong>status</strong><strong> </strong><strong>codes</strong><strong> </strong><strong>and</strong><strong> </strong><strong>symbols</strong><strong> </strong><strong>for</strong><strong> </strong><strong>status</strong><strong> </strong><strong>codes.</strong>
<h6><a name="the-location-option"></a>2.2.11.4 The <tt>:location</tt> Option</h6>
<strong>You</strong><strong> </strong><strong>can</strong><strong> </strong><strong>use</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>:location</strong></tt><strong> </strong><strong>option</strong><strong> </strong><strong>to</strong><strong> </strong><strong>set</strong><strong> </strong><strong>the</strong><strong> </strong><strong>HTTP</strong><strong> </strong><tt><strong>Location</strong></tt><strong> </strong><strong>header:</strong>

render :xml =&gt; photo, :location =&gt; photo_url(photo)
<h5><a name="finding-layouts"></a>2.2.12 Finding Layouts</h5>
To find the current layout, Rails first looks for a file in <tt>app/views/layouts</tt> with the same base name as the controller. For example, rendering actions from the <tt>PhotosController</tt> class will use <tt>app/views/layouts/photos.html.erb</tt> (or <tt>app/views/layouts/photos.builder</tt>). If there is no such controller-specific layout, Rails will use <tt>app/views/layouts/application.html.erb</tt> or <tt>app/views/layouts/application.builder</tt>. If there is no <tt>.erb</tt> layout, Rails will use a <tt>.builder</tt> layout if one exists. Rails also provides several ways to more precisely assign specific layouts to individual controllers and actions.

<span style="font-family: DejaVu Sans;">要查找正确的</span>layout<span style="font-family: DejaVu Sans;">，</span>Rails<span style="font-family: DejaVu Sans;">首先查找</span><tt>app/views/layouts</tt><span style="font-family: DejaVu Sans;"><tt>中与</tt></span><tt>controller</tt><tt> </tt><tt>base</tt><tt> </tt><tt>name</tt><span style="font-family: DejaVu Sans;"><tt>相同的文件。</tt></span><tt>For</tt><tt> </tt><tt>example,</tt><tt> </tt><tt>rendering</tt><tt> </tt><tt>actions</tt><tt> </tt><tt>from</tt><tt> </tt><tt>the</tt><tt> </tt><tt>PhotosController</tt><tt> </tt><tt>class</tt><tt> </tt><tt>will</tt><tt> </tt><tt>use</tt><tt> </tt><tt>app/views/layouts/photos.html.erb</tt><tt> </tt><tt>(or</tt><tt> </tt><tt>app/views/layouts/photos.builder).</tt><tt> </tt><tt>If</tt><tt> </tt><tt>there</tt><tt> </tt><tt>is</tt><tt> </tt><tt>no</tt><tt> </tt><tt>such</tt><tt> </tt><tt>controller-specific</tt><tt> </tt><tt>layout,</tt><tt> </tt><tt>Rails</tt><tt> </tt><tt>will</tt><tt> </tt><tt>use</tt><tt> </tt><tt>app/views/layouts/application.html.erb</tt><tt> </tt><tt>or</tt><tt> </tt><tt>app/views/layouts/application.builder.</tt><tt> </tt><span style="font-family: DejaVu Sans;"><tt>如果没有找到</tt></span><tt>.reb</tt><span style="font-family: DejaVu Sans;"><tt>的</tt></span><tt>layout</tt><span style="font-family: DejaVu Sans;"><tt>，</tt></span><tt>Rails</tt><span style="font-family: DejaVu Sans;"><tt>将会使用一个</tt></span><tt>.builder</tt><span style="font-family: DejaVu Sans;"><tt>如果存在。</tt></span><tt>Rails</tt><span style="font-family: DejaVu Sans;"><tt>也提供一些方法来恰当的分派</tt></span><tt>assign</tt><span style="font-family: DejaVu Sans;"><tt>指定的</tt></span><tt>layouts</tt><span style="font-family: DejaVu Sans;"><tt>到个别的</tt></span><tt>controllers</tt><span style="font-family: DejaVu Sans;"><tt>和</tt></span><tt>actions</tt><span style="font-family: DejaVu Sans;"><tt>。</tt></span>
<h6><a name="specifying-layouts-on-a-per-controller-b"></a> 2.2.12.1 Specifying Layouts on a per-Controller Basis</h6>
You can override the automatic layout conventions in your controllers by using the <tt>layout</tt> declaration in the controller. For example:

<span style="font-family: DejaVu Sans;">你可以通过在</span>controller<span style="font-family: DejaVu Sans;">（类）中使用</span>layout<span style="font-family: DejaVu Sans;">决定（指定的）</span>layout<span style="font-family: DejaVu Sans;">来覆盖自动</span>layout<span style="font-family: DejaVu Sans;">公约。例如：</span>

class ProductsController &lt; ApplicationController

layout "inventory"

#...

end

With this declaration, all methods within <tt> </tt><tt>ProductsController</tt> will use <tt>app/views/layouts/inventory.html.erb</tt> for their layout.

<span style="font-family: DejaVu Sans;">通过这个声明，在</span><tt>ProductsController</tt><span style="font-family: DejaVu Sans;"><tt>中所有的方法都将使用</tt><tt></tt></span><tt>app/views/layouts/inventory.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>作为他们的</tt></span><tt>layout</tt><span style="font-family: DejaVu Sans;"><tt>。</tt></span>

To assign a specific layout for the entire application, use a declaration in your <tt>ApplicationController</tt> class:

<span style="font-family: DejaVu Sans;"><strong>要分配一个指定的</strong></span><strong>layout</strong><span style="font-family: DejaVu Sans;"><strong>给整个</strong></span><strong>application</strong><span style="font-family: DejaVu Sans;"><strong>，在你的</strong></span><tt><strong>ApplicationController</strong></tt><span style="font-family: DejaVu Sans;"><tt><strong>类（</strong></tt></span><tt><strong>app/controllers/application_controller.rb</strong></tt><span style="font-family: DejaVu Sans;"><tt><strong>）</strong></tt><strong>使用一个声明：</strong></span>

class ApplicationController &lt; ActionController::Base

layout "main"

#...

end

With this declaration, all views in the entire application will use <tt>app/views/layouts/main.html.erb</tt> for their layout.<span style="font-family: DejaVu Sans;">通过这个声明，整个应用程序中的</span>view<span style="font-family: DejaVu Sans;">将会使用</span><tt>app/views/layouts/main.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>作为他们的</tt></span><tt>layout</tt><span style="font-family: DejaVu Sans;"><tt>。</tt></span>
<h6><a name="choosing-layouts-at-runtime"></a>2.2.12.2 Choosing Layouts at Runtime<span style="font-family: WenQuanYi Micro Hei;">在运行的时候选择</span>layout</h6>
You can use a symbol to defer the choice of layout until a request is processed:

<span style="font-family: DejaVu Sans;">你可以使用一个</span>symbol<span style="font-family: DejaVu Sans;">来推迟选择的</span>layout<span style="font-family: DejaVu Sans;">直到一个请求被处理：</span>

class ProductsController &lt; ApplicationController

layout :products_layout

&nbsp;

def show

@product = Product.find(params[:id])

end

&nbsp;

private

def products_layout

@current_user.special? ? "special" : "products"

end

&nbsp;

end

Now, if the current user is a special user, they’ll get a special layout when viewing a product. You can even use an inline method to determine the layout:

<span style="font-family: DejaVu Sans;">现在，如果当前用户是一个特殊用户，他们将得到一个特殊的</span>layout<span style="font-family: DejaVu Sans;">当其正在访问一个</span>product<span style="font-family: DejaVu Sans;">。你甚至可以使用一个内联方法来确定</span>layout<span style="font-family: DejaVu Sans;">：</span>

You can also decide the layout by passing a Proc object, the block you give the Proc will be given the <tt>controller</tt> instance, so you can make decisions based on the current request. For example:

<span style="font-family: DejaVu Sans;">你也可以通过一个</span>Proc object<span style="font-family: DejaVu Sans;">来决定</span>layout<span style="font-family: DejaVu Sans;">，你给出的</span>Proc<span style="font-family: DejaVu Sans;">块将会被传递给</span>controller<span style="font-family: DejaVu Sans;">实例，因此你可以在当前的请求的基础上作出（指定</span>layout<span style="font-family: DejaVu Sans;">的）决定。</span>

class ProductsController &lt; ApplicationController

layout Proc.new { |controller| controller.request.xhr? ? 'popup' : 'application' }

end

##<span style="font-family: DejaVu Sans;">块里面的内容是什么意思呢？</span>
<h6><a name="conditional-layouts"></a>2.2.12.3 Conditional Layouts<span style="font-family: WenQuanYi Micro Hei;">条件</span>layouts</h6>
Layouts specified at the controller level support <tt>:only</tt> and <tt>:except</tt> options that take either a method name or an array of method names which correspond to method names within the controller:

<span style="font-family: DejaVu Sans;">在</span>controller<span style="font-family: DejaVu Sans;">级别的</span>layouts<span style="font-family: DejaVu Sans;">指定支持</span>:only and :except<span style="font-family: DejaVu Sans;">选项它们获取一个方法名或者一个方法名组成的数组它们对应于在</span>controller<span style="font-family: DejaVu Sans;">中的方法名。</span>

class ProductsController &lt; ApplicationController

layout "product", :except =&gt; [:index, :rss]

end

With this declaration, the <tt>product</tt> layout would be used for everything but the <tt>rss</tt> and <tt>index</tt> methods.

<span style="font-family: DejaVu Sans;">通过这个声明，</span>product<span style="font-family: DejaVu Sans;">将会使用任何</span>layout<span style="font-family: DejaVu Sans;">但是除了</span>rss<span style="font-family: DejaVu Sans;">和</span>index<span style="font-family: DejaVu Sans;">方法。</span>
<h6><a name="layout-inheritance"></a>2.2.12.4 Layout Inheritance layout<span style="font-family: WenQuanYi Micro Hei;">继承</span></h6>
Layouts are shared downwards in the hierarchy, and more specific layouts always override more general ones. For example:
<ul>
	<li><tt>application_controller.rb</tt></li>
</ul>
class ApplicationController &lt; ActionController::Base

layout "main"

end
<ul>
	<li><tt><span style="color: #000000;"><span style="font-size: small;">posts_controller.rb</span></span></tt></li>
</ul>
class PostsController &lt; ApplicationController

end
<ul>
	<li><tt>special_</tt><tt><span style="color: #000000;"><span style="font-size: small;">posts</span></span></tt><tt>_controller.rb</tt></li>
</ul>
class SpecialPostsController &lt; PostsController

layout "special"

end
<ul>
	<li><tt><span style="color: #000000;"><span style="font-size: small;">old_posts_controller.rb</span></span></tt></li>
</ul>
class OldPostsController &lt; SpecialPostsController

layout nil

&nbsp;

def show

@post = Post.find(params[:id])

end

&nbsp;

def index

@old_posts = Post.older

render :layout =&gt; "old"

end

# ...

end

In this application:
<ul>
	<li>In general, views will be rendered in the <tt>main</tt> layout</li>
	<li><tt>PostsController#index</tt> will use the <tt>main</tt> layout</li>
	<li><tt>SpecialPostsController#index</tt> will use the <tt>special</tt> layout</li>
	<li><tt>OldPostsController#show</tt> will use<strong> </strong><strong>no</strong><strong> </strong><strong>layout</strong><strong> </strong><strong>at</strong><strong> </strong><strong>all</strong><strong> </strong></li>
	<li><tt>OldPostsController#index</tt> will use the <tt>old</tt> layout</li>
</ul>
<h5><a name="avoiding-double-render-errors"></a>2.2.13 Avoiding Double Render Errors<span style="font-family: WenQuanYi Micro Hei;">避免两次</span>render<span style="font-family: WenQuanYi Micro Hei;">错误</span></h5>
Sooner or later, most Rails developers will see the error message “Can only render or redirect once per action”. While this is annoying, it’s relatively easy to fix. Usually it happens because of a fundamental misunderstanding of the way that <tt>render</tt> works.

<span style="font-family: DejaVu Sans;">迟早，多大数</span>Rails<span style="font-family: DejaVu Sans;">开发人员将会看到<span style="font-family: Liberation Serif,Times New Roman,serif;">“</span></span>Can only render or redirect once per action”.<span style="font-family: DejaVu Sans;">当这恼人的</span>(<span style="font-family: DejaVu Sans;">消息出现的时候</span>)<span style="font-family: DejaVu Sans;">，它相当容易修复。它发生的通常情况是因为对</span>render<span style="font-family: DejaVu Sans;">工作方式的根本误解。</span>

For example, here’s some code that will trigger this error:

<span style="font-family: DejaVu Sans;">例如，这些代码将会触发这个错误：</span>

def show

@book = Book.find(params[:id])

if @book.special?

render :action =&gt; "special_show"

end

render :action =&gt; "regular_show"

end

If <tt>@book.special?</tt> evaluates to <tt>true</tt>, Rails will start the rendering process to dump<span style="font-family: DejaVu Sans;">转储</span>the <tt>@book</tt> variable into the <tt>special_show</tt> view.<strong> </strong><strong>But</strong><strong> </strong><strong>this</strong><strong> </strong><strong>will</strong><strong> </strong><em><strong>not</strong></em><strong> </strong><strong>stop</strong><strong> </strong><strong>the</strong><strong> </strong><strong>rest</strong><strong> </strong><strong>of</strong><strong> </strong><strong>the</strong><strong> </strong><strong>code</strong><strong> </strong><strong>in</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>show</strong></tt><strong> </strong><strong>action</strong><strong> </strong><strong>from</strong><strong> </strong><strong>running</strong>, and <strong>when</strong><strong> </strong><strong>Rails</strong><strong> </strong><strong>hits</strong><strong> </strong><strong>the</strong><strong> </strong><strong>end</strong><strong> </strong><strong>of</strong><strong> </strong><strong>the</strong><strong> </strong><strong>action,</strong><strong> </strong><strong>it</strong><strong> </strong><strong>will</strong><strong> </strong><strong>start</strong><strong> </strong><strong>to</strong><strong> </strong><strong>render</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>regular_show</strong></tt><strong> </strong><strong>view</strong><strong> – </strong><strong>and</strong><strong> </strong><strong>throw</strong><strong> </strong><strong>an</strong><strong> </strong><strong>error</strong>. The solution is simple: make sure that you have only one call to <tt>render</tt> or <tt>redirect</tt> in a single code path. One thing that can help is <tt>and</tt><tt> </tt><tt>return</tt>. Here’s a patched version of the method:

def show

@book = Book.find(params[:id])

if @book.special?

render :action =&gt; "special_show" and return

end

render :action =&gt; "regular_show"

end

Make sure you use <tt>and</tt><tt> </tt><tt>return</tt> and not <tt>&amp;&amp;</tt><tt> </tt><tt>return</tt> because while the former will work, the latter will not due to operator precedence in the Ruby Language.

<span style="font-family: DejaVu Sans;">确保你使用的是</span>and return<span style="font-family: DejaVu Sans;">而不是</span>&amp;&amp; return<span style="font-family: DejaVu Sans;">因为即使</span>former<span style="font-family: DejaVu Sans;">将会工作，然而随后的却不会那是因为</span>ruby<span style="font-family: DejaVu Sans;">语言中的运算优先操作。</span>

irb(main):019:0&gt; 1+3 and p 3+4

7

=&gt; nil

irb(main):020:0&gt; 1+3 &amp;&amp; p 3+4

SyntaxError: compile error

(irb):20: syntax error, unexpected tINTEGER, expecting kDO or '{' or '('

1+3 &amp;&amp; p 3+4

^

from (irb):20

from :0

irb(main):021:0&gt; 1+3 &amp;&amp;(p 3+4)

7

=&gt; nil

irb(main):022:0&gt; false and 3+4

=&gt; false
<h4><a name="using-redirect_to"></a>2.3 Using <tt>redirect_to</tt><span style="font-family: WenQuanYi Micro Hei;"><tt>使用重定向</tt></span></h4>
<strong>Another</strong><strong> </strong><strong>way</strong><strong> </strong><strong>to</strong><strong> </strong><strong>handle</strong><strong> </strong><strong>returning</strong><strong> </strong><strong>responses</strong><strong> </strong><strong>to</strong><strong> </strong><strong>an</strong><strong> </strong><strong>HTTP</strong><strong> </strong><strong>request</strong><strong> </strong><strong>is</strong><strong> </strong><strong>with</strong><strong> </strong><tt><strong>redirect_to</strong></tt>. As you’ve seen, <tt>render</tt> tells Rails which view (or other asset) to use in constructing a response. The <tt>redirect_to</tt> method does something completely different: it tells the browser to send a new request for a different URL. For example, you could redirect from wherever you are in your code to the index of photos in your application with this call:

<span style="font-family: DejaVu Sans;">另一种方法是使用</span><tt>redirect_to</tt><span style="font-family: DejaVu Sans;"><tt>来处理</tt></span><tt>HTTP</tt><span style="font-family: DejaVu Sans;"><tt>请求返回响应。正如你看到的，</tt></span><tt>render</tt><span style="font-family: DejaVu Sans;"><tt>通知</tt></span><tt>Rails</tt><tt>——</tt><span style="font-family: DejaVu Sans;"><tt>视图（或者其他</tt></span><tt>asset</tt><span style="font-family: DejaVu Sans;"><tt>）使用其他（</tt></span><tt>layout</tt><span style="font-family: DejaVu Sans;"><tt>）结构来响应。</tt><tt></tt></span><tt>redirect_to</tt><span style="font-family: DejaVu Sans;"><tt>方法做一些完全不同的事情：它告诉浏览器发送一个新的请求给不同的</tt></span><tt>URL</tt><span style="font-family: DejaVu Sans;"><tt>。例如，无论你在你代码的哪里你可以重定向到</tt></span><tt>photos</tt><span style="font-family: DejaVu Sans;"><tt>的</tt></span><tt>index</tt><span style="font-family: DejaVu Sans;"><tt>，在你的应用程序中使用这个掉用：</tt></span>

<code>redirect_to</code><code> </code><code>photos_path</code>

You can use <tt>redirect_to</tt> <strong>with</strong><strong> </strong><strong>any</strong><strong> </strong><strong>arguments</strong> that you <strong>could</strong><strong> </strong><strong>use</strong><strong> </strong><strong>with</strong><strong> </strong><tt><strong>link_to</strong></tt><strong> </strong><strong>or</strong><strong> </strong><tt><strong>url_for</strong></tt>. In addition, <strong>there</strong><strong>’</strong><strong>s</strong><strong> </strong><strong>a</strong><strong> </strong><strong>special</strong><strong> </strong><strong>redirect</strong><strong> </strong><strong>that</strong><strong> </strong><strong>sends</strong><strong> </strong><strong>the</strong><strong> </strong><strong>user</strong><strong> </strong><strong>back</strong><strong> </strong><strong>to</strong><strong> </strong><strong>the</strong><strong> </strong><strong>page</strong><strong> </strong><strong>they</strong><strong> </strong><strong>just</strong><strong> </strong><strong>came</strong><strong> </strong><strong>from:</strong><span style="font-family: DejaVu Sans;"><strong>这里是特殊的重定向发送（命令）给用户（的浏览器）回到原来的页面：</strong></span>

redirect_to :back
<h5><a name="getting-a-different-redirect-status-code"></a> 2.3.1 Getting a Different Redirect Status Code</h5>
Rails uses HTTP status code 302 (temporary redirect) when you call <tt> </tt><tt>redirect_to</tt>. If you’d like to use a different status code (perhaps 301, permanent redirect), you can do so by using the <tt>:status</tt> option:

Rails<span style="font-family: DejaVu Sans;">使用</span>HTTP<span style="font-family: DejaVu Sans;">状态代码</span>301(<span style="font-family: DejaVu Sans;">临时重定向</span>)<span style="font-family: DejaVu Sans;">当你调用</span><tt>redirect_to</tt><span style="font-family: DejaVu Sans;"><tt>。如果你想使用一个不同的状态代码（可能是</tt></span><tt>301</tt><span style="font-family: DejaVu Sans;"><tt>，永久重定向），你可以通过使用</tt></span><tt>:status</tt><span style="font-family: DejaVu Sans;"><tt>选项：</tt></span>

redirect_to photos_path, :status =&gt; 301

Just like the <tt>:status</tt> option for <tt>render</tt>, <tt>:status</tt> for <tt>redirect_to</tt> accepts both numeric and symbolic header designations<span style="font-family: DejaVu Sans;">名称</span>.
<h5><a name="the-difference-between-render-and-redire"></a> 2.3.2 The Difference Between <tt>render</tt> and <tt>redirect_to</tt></h5>
Sometimes inexperienced developers conceive of <tt>redirect_to</tt> as a sort of <tt>goto</tt> command, moving execution from one place to another in your Rails code. <strong>This</strong><strong> </strong><strong>is</strong><strong> </strong><em><strong>not</strong></em><strong> </strong><strong>correct.</strong><strong> </strong><strong>Your</strong><strong> </strong><strong>code</strong><strong> </strong><strong>stops</strong><strong> </strong><strong>running</strong><strong> </strong><strong>and</strong><strong> </strong><strong>waits</strong><strong> </strong><strong>for</strong><strong> </strong><strong>a</strong><strong> </strong><strong>new</strong><strong> </strong><strong>request</strong><strong> </strong><strong>for</strong><strong> </strong><strong>the</strong><strong> </strong><strong>browser.</strong><strong> </strong>It just happens that you’ve told the browser what request it should make next, by sending back an HTTP 302 status code.

<span style="font-family: DejaVu Sans;">有时缺乏经验的开发人员心中构想的</span><tt>redirect_to</tt><span style="font-family: DejaVu Sans;"><tt>就像一种</tt></span><tt>goto</tt><span style="font-family: DejaVu Sans;"><tt>命令，从你的</tt></span><tt>Rails</tt><span style="font-family: DejaVu Sans;"><tt>代码的一个地方移动到另一个地方执行。</tt></span>

Consider these actions to see the difference:

def index

@books = Book.all

end

&nbsp;

def show

@book = Book.find_by_id(params[:id])

if @book.nil?

render :action =&gt; "index"

end

end

With the code in this form, there will likely be a problem if the <tt>@book</tt> variable is <tt>nil</tt>. Remember, a <tt>render</tt><tt> </tt><tt>:action</tt> <strong>doesn</strong><strong>’</strong><strong>t</strong><strong> </strong><strong>run</strong><strong> </strong><strong>any</strong><strong> </strong><strong>code</strong><strong> </strong><strong>in</strong><strong> </strong><strong>the</strong><strong> </strong><strong>target</strong><strong> </strong><strong>action</strong>, so nothing will set up the <tt>@books</tt> variable that the <tt>index</tt> view is presumably<span style="font-family: DejaVu Sans;">可能</span>depending on<span style="font-family: DejaVu Sans;">（实际验证结果也就是说</span>@books<span style="font-family: DejaVu Sans;">为空）</span>. One way to fix this is to redirect instead of rendering:

def index

@books = Book.all

end

&nbsp;

def show

@book = Book.find_by_id(params[:id])

if @book.nil?

redirect_to :action =&gt; :index

end

end

<strong>With</strong><strong> </strong><strong>this</strong><strong> </strong><strong>code,</strong><strong> </strong><strong>the</strong><strong> </strong><strong>browser</strong><strong> </strong><strong>will</strong><strong> </strong><strong>make</strong><strong> </strong><strong>a</strong><strong> </strong><strong>fresh</strong><strong> </strong><strong>request</strong><strong> </strong><strong>for</strong><strong> </strong><strong>the</strong><strong> </strong><strong>index</strong><strong> </strong><strong>page,</strong><strong> </strong><strong>the</strong><strong> </strong><strong>code</strong><strong> </strong><strong>in</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>index</strong></tt><strong> </strong><strong>method</strong><strong> </strong><strong>will</strong><strong> </strong><strong>run,</strong><strong> </strong><strong>and</strong><strong> </strong><strong>all</strong><strong> </strong><strong>will</strong><strong> </strong><strong>be</strong><strong> </strong><strong>well.</strong>

The only downside to this code, is that it requires a round trip<span style="font-family: DejaVu Sans;">旅</span>to the browser, the browser requested the show action with <tt>/books/1</tt> and the controller finds that there are no books, so the controller sends out a 302 redirect response to the browser telling it to go to <tt>/books/</tt>, the browser complies<span style="font-family: DejaVu Sans;">依从</span>and sends a new request back to the controller asking now for the <tt>index</tt> action, the controller then gets all the books in the database and renders the index template, sending it back down to the browser which then shows it on your screen.

<span style="font-family: DejaVu Sans;">这段代码仅有的缺点，是它需要浏览器兜一个圈，浏览器通过向服务器发送</span>/books/1<span style="font-family: DejaVu Sans;">请求</span>show action<span style="font-family: DejaVu Sans;">同时</span>controller<span style="font-family: DejaVu Sans;">发现数据库没有这本书，因此</span>controller<span style="font-family: DejaVu Sans;">发出一个</span>302<span style="font-family: DejaVu Sans;">重定向响应给浏览器告诉它导航到</span>/books/<span style="font-family: DejaVu Sans;">，浏览器依从（响应）并且发回一个新的请求来申请现在（进行）</span>index action<span style="font-family: DejaVu Sans;">，</span>controller<span style="font-family: DejaVu Sans;">随后从数据库中获取所有的</span>books<span style="font-family: DejaVu Sans;">并且</span>renders<span style="font-family: DejaVu Sans;">到</span>index template<span style="font-family: DejaVu Sans;">，发送它至浏览器然后在你的</span>screen<span style="font-family: DejaVu Sans;">得到显示。</span>

While in a small app, this added latency might not be a problem, it is something to think about when speed of response is of the essence<span style="font-family: DejaVu Sans;">本质</span>. One way to handle this double request (though a contrived example) could be:

<span style="font-family: DejaVu Sans;">然而在一个小的</span>app<span style="font-family: DejaVu Sans;">，加载延迟不是问题，有时候是基于响应速度为主的考虑。一种方式来处理这个两次请求（通过一个人为的例子）可以像这样：</span>

def index

@books = Book.all

end

&nbsp;

def show

@book = Book.find_by_id(params[:id])

if @book.nil?

@books = Book.all

render "index", :alert =&gt; 'Your book was not found!'

end

end

Which would detect<span style="font-family: DejaVu Sans;">检测</span>that there are no books, populate<span style="font-family: DejaVu Sans;">填充</span>the <tt>@books</tt> instance variable with all the books in the database and then directly render the <tt>index.html.erb</tt> template returning it to the browser with a flash alert message telling the user what happened.
<h4><a name="using-head-to-build-header-only-response"></a> 2.4 Using <tt>head</tt> To Build Header-Only Responses<span style="font-family: WenQuanYi Micro Hei;">使用</span>head<span style="font-family: WenQuanYi Micro Hei;">来创建一个</span>Header-Only<span style="font-family: WenQuanYi Micro Hei;">响应</span></h4>
<strong>The</strong><strong> </strong><tt><strong>head</strong></tt><strong> </strong><strong>method</strong><strong> </strong><strong>can</strong><strong> </strong><strong>be</strong><strong> </strong><strong>used</strong><strong> </strong><strong>to</strong><strong> </strong><strong>send</strong><strong> </strong><strong>responses</strong><strong> </strong><strong>with</strong><strong> </strong><strong>only</strong><strong> </strong><strong>headers</strong><strong> </strong><strong>to</strong><strong> </strong><strong>the</strong><strong> </strong><strong>browser.</strong><strong> </strong>It provides a more obvious alternative to calling <tt>render</tt><tt> </tt><tt>:nothing</tt>.<span style="font-family: DejaVu Sans;">它提供一个</span><tt>render</tt><tt> </tt><tt>:nothing</tt><span style="font-family: DejaVu Sans;"><tt>明显的替代。</tt></span>The <tt>head</tt> method takes one parameter, which is interpreted<span style="font-family: DejaVu Sans;">解释执行</span>as a hash of header names and values. For example, you can return only an error header:

head :bad_request

Which would produce the following header:

HTTP/1.1 400 Bad Request

Connection: close

Date: Sun, 24 Jan 2010 12:15:53 GMT

Transfer-Encoding: chunked

Content-Type: text/html; charset=utf-8

X-Runtime: 0.013483

Set-Cookie: _blog_session=...snip...; path=/; HttpOnly

Cache-Control: no-cache

Or you can use other HTTP headers to convey additional information:

<span style="font-family: DejaVu Sans;">或者你可以使用其他的</span>HTTP headers<span style="font-family: DejaVu Sans;">来传递额外信息：</span>

head :created, :location =&gt; photo_path(@photo)

Which would produce:<span style="font-family: DejaVu Sans;">这将会生成：</span>

HTTP/1.1 201 Created

Connection: close

Date: Sun, 24 Jan 2010 12:16:44 GMT

Transfer-Encoding: chunked

Location: /photos/1

Content-Type: text/html; charset=utf-8

X-Runtime: 0.083496

Set-Cookie: _blog_session=...snip...; path=/; HttpOnly

Cache-Control: no-cache
<h3><a name="structuring-layouts"></a>3 Structuring Layouts<span style="font-family: WenQuanYi Micro Hei;">结构布局</span></h3>
When Rails renders a view as a response, it does so by combining the view with the current layout (using the rules for finding the current layout that were covered earlier in this guide). Within a layout, you have access to three tools for combining different bits of output to form the overall response:

<span style="font-family: DejaVu Sans;">当</span>Rails<span style="font-family: DejaVu Sans;">渲染一个视图作为响应，它这样做了通过联合视图和当前的</span>layout<span style="font-family: DejaVu Sans;">（使用规则来查找当前的</span>layout<span style="font-family: DejaVu Sans;">，它在这个教程的早期被介绍了）。在一个</span>layout<span style="font-family: DejaVu Sans;">中，你访问三种工具来整合不同的输出块，以形成整体的回应：</span>
<ul>
	<li>Asset tags</li>
	<li><tt>yield</tt> and <tt>content_for</tt></li>
	<li>Partials</li>
</ul>
<h4><a name="asset-tags"></a>3.1 Asset Tags<span style="font-family: WenQuanYi Micro Hei;">标签资源</span></h4>
Asset tags provide methods for generating HTML that links views to feeds, JavaScript, stylesheets, images, videos and audios. These are the six asset tags available in Rails:

<span style="font-family: DejaVu Sans;">标签资产提供方法来创建</span>HTML<span style="font-family: DejaVu Sans;">链接视图到</span>feeds<span style="font-family: DejaVu Sans;">，</span>JavaScript<span style="font-family: DejaVu Sans;">，</span>stylesheets<span style="font-family: DejaVu Sans;">，</span>images<span style="font-family: DejaVu Sans;">，</span>videos<span style="font-family: DejaVu Sans;">和</span>audios<span style="font-family: DejaVu Sans;">。</span>Rails<span style="font-family: DejaVu Sans;">中有六个</span>asset tags<span style="font-family: DejaVu Sans;">可用。</span>
<ul>
	<li><tt>auto_discovery_link_tag</tt></li>
	<li><tt>javascript_include_tag</tt></li>
	<li><tt>stylesheet_link_tag</tt></li>
	<li><tt>image_tag</tt></li>
	<li><tt>video_tag</tt></li>
	<li><tt>audio_tag</tt></li>
</ul>
You can use these tags in layouts or other views, although the tags other than <tt>image_tag</tt> are most commonly used in the <tt>&lt;head&gt;</tt> section of a layout.

<span style="font-family: DejaVu Sans;">你可以使用这些</span>tags<span style="font-family: DejaVu Sans;">在</span>layout<span style="font-family: DejaVu Sans;">或者其他的</span>views<span style="font-family: DejaVu Sans;">，即使</span>tags <tt>image_tag</tt><span style="font-family: DejaVu Sans;">超过通常使用的在</span>layout<span style="font-family: DejaVu Sans;">的</span>&lt;head&gt;<span style="font-family: DejaVu Sans;">部分使用。</span>

The asset tags do <em>not</em> verify the existence of the assets at the specified locations; they simply assume<span style="font-family: DejaVu Sans;">假设</span>that you know what you’re doing and generate the link.

Asset tags<span style="font-family: DejaVu Sans;">并不验证</span>asstes<span style="font-family: DejaVu Sans;">存在的性在</span>specified locations<span style="font-family: DejaVu Sans;">；他们假设你知道他们正在做的什么并且生成链接。</span>
<h5><a name="linking-to-feeds-with-auto_discovery_lin"></a> 3.1.1 Linking to Feeds with <tt>auto_discovery_link_tag</tt></h5>
The <tt>auto_discovery_link_tag</tt> helper builds HTML that most browsers and newsreaders can use to detect the presences of RSS or ATOM feeds. It takes the type of the link (<tt>:rss</tt> or <tt>:atom</tt>), a hash of options that are passed through to url_for, and a hash of options for the tag:

<span style="font-family: DejaVu Sans;">对于</span><tt>auto_discovery_link_tag</tt><tt> </tt><tt>helper</tt><span style="font-family: DejaVu Sans;"><tt>创建</tt></span><tt>HTML</tt><span style="font-family: DejaVu Sans;"><tt>，那些浏览器和新读者能够检测存在的</tt></span><tt>RSS</tt><span style="font-family: DejaVu Sans;"><tt>或者</tt></span><tt>ATOM</tt><span style="font-family: DejaVu Sans;"><tt>。它带有</tt></span><tt>link</tt><span style="font-family: DejaVu Sans;"><tt>类型的参数为</tt></span><tt>(:rss</tt><tt> </tt><tt>or</tt><tt> </tt><tt>:atom)</tt><span style="font-family: DejaVu Sans;"><tt>，一个</tt></span><tt>hash</tt><span style="font-family: DejaVu Sans;"><tt>字典的选项可以通过它来指定</tt><tt></tt></span><tt>url_for</tt><span style="font-family: DejaVu Sans;"><tt>以及</tt></span><tt>tag</tt><span style="font-family: DejaVu Sans;"><tt>的</tt></span><tt>hash</tt><span style="font-family: DejaVu Sans;"><tt>选项。</tt></span>

&lt;%= auto_discovery_link_tag(:rss, {:action =&gt; "feed"},

{:title =&gt; "RSS Feed"}) %&gt;

There are three tag options available for <tt>auto_discovery_link_tag</tt>:
<ul>
	<li><tt>:rel</tt> specifies the <tt>rel</tt> value in the link (defaults to “alternate”)</li>
	<li><tt>:type</tt> specifies an explicit MIME type. Rails will generate an appropriate MIME type automatically.</li>
	<li><tt>:title</tt> specifies the title of the link</li>
</ul>
MIME<span style="font-family: DejaVu Sans;">类型就是设定某种扩展名的文件用一种应用程序来打开的方式类型，当该扩展名文件被访问的时候，浏览器会自动使用指定应用程序来打开。多用于指定一些客户端自定义的文件名，以及一些媒体文件打开方式。</span>
<h5><a name="linking-to-javascript-files-with-javascr"></a> 3.1.2 Linking to JavaScript Files with <tt>javascript_include_tag</tt></h5>
The <tt>javascript_include_tag</tt> helper returns an HTML <tt>script</tt> tag for each source provided. Rails looks in <tt>public/javascripts</tt> for these files by default, but you can specify a full path relative to the document root, or a URL, if you prefer. For example, to include <tt>public/javascripts/main.js</tt>:

<tt>javascript_include_tag</tt><tt> </tt><tt>helper</tt><span style="font-family: DejaVu Sans;"><tt>返回一个</tt></span><tt>HTML</tt><tt> </tt><tt>script</tt><tt> </tt><tt>tag</tt><span style="font-family: DejaVu Sans;"><tt>给每个提供的源。</tt></span><tt>Rails</tt><span style="font-family: DejaVu Sans;"><tt>默认在</tt><tt></tt></span><tt>public/javascripts</tt><span style="font-family: DejaVu Sans;"><tt>查找这些文件，但是你可以指定一个全路径关联到文档目录，或者一个</tt></span><tt>URL</tt><span style="font-family: DejaVu Sans;"><tt>，如果你喜欢。例如，要包含</tt></span><tt>public/javascripts/main.js:</tt>

&lt;%= javascript_include_tag "main" %&gt;

To include <tt>public/javascripts/main.js</tt> and <tt>public/javascripts/columns.js</tt>:

&lt;%= javascript_include_tag "main", "columns" %&gt;

To include <tt>public/javascripts/main.js</tt> and <tt>public/photos/columns.js</tt>:

&lt;%= javascript_include_tag "main", "/photos/columns" %&gt;

To include <tt>http://example.com/main.js</tt>:

&lt;%= javascript_include_tag "http://example.com/main.js" %&gt;

If the application does not use the asset pipeline, the <tt>:defaults</tt> option loads jQuery by default:

<span style="font-family: DejaVu Sans;">如果应用程序没有使用</span>asset<span style="font-family: DejaVu Sans;">管道，</span>:defautls<span style="font-family: DejaVu Sans;">选项默认导入</span>jQuery<span style="font-family: DejaVu Sans;">：</span>

&lt;%= javascript_include_tag :defaults %&gt;

And you can in any case override the expansion in <tt>config/application.rb</tt>:

<span style="font-family: DejaVu Sans;">并且你可以随意覆盖这个扩展在</span><tt>config/application.rb</tt><span style="font-family: DejaVu Sans;"><tt>：</tt></span>

config.action_view.javascript_expansions[:defaults] = %w(foo.js bar.js)

When using <tt>:defaults</tt>, if an <tt>application.js</tt> file exists in <tt>public/javascripts</tt> it will be included as well at then end.

<span style="font-family: DejaVu Sans;">如果使用</span>:defaults<span style="font-family: DejaVu Sans;">，如果一个</span>application.js<span style="font-family: DejaVu Sans;">文件存在于</span>public/javascripts<span style="font-family: DejaVu Sans;">他将会同样被导入在随后。</span>

Also, the <tt>:all</tt> option loads every JavaScript file in <tt>public/javascripts</tt>:<span style="font-family: DejaVu Sans;">（不包含子目录）</span>

&lt;%= javascript_include_tag :all %&gt;

Note that your defaults of choice will be included first, so they will be available to all subsequently<span style="font-family: DejaVu Sans;">随后</span>included files.

<span style="font-family: DejaVu Sans;">注意你默认的选择将会首先被</span>inclueded<span style="font-family: DejaVu Sans;">，因此他们将于随后导入所有的文件。</span>

You can supply the <tt>:recursive</tt> option to load files in subfolders of <tt>public/javascripts</tt> as well:

<span style="font-family: DejaVu Sans;">你也可以提供</span>:recursive<span style="font-family: DejaVu Sans;">（递归）选项来导入在</span><tt>public/javascripts</tt> <span style="font-family: DejaVu Sans;">子文件夹中的文件：</span>

&lt;%= javascript_include_tag :all, :recursive =&gt; true %&gt;

If you’re loading multiple JavaScript files, you can create a better user experience by combining multiple files into a single download. To make this happen in production, specify <tt>:cache</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt> in your <tt>javascript_include_tag</tt>:

<span style="font-family: DejaVu Sans;">如果你正在导入多个的</span>JavaScript<span style="font-family: DejaVu Sans;">文件，你可以创建一个更好的用户体验通过整合多个文件到一个单独的下载（</span>all.js<span style="font-family: DejaVu Sans;">）。要得到这样的效果在产品中，在你的</span><tt>javascript_include_tag</tt><span style="font-family: DejaVu Sans;"><tt>中</tt>指定</span><tt>:cache</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt>

&lt;%= javascript_include_tag "main", "columns", :cache =&gt; true %&gt;

You can even use dynamic paths such as <tt>cache/#{current_site}/main/display</tt>.

<span style="font-family: DejaVu Sans;">你可以使用多元路径例如</span><tt>cache/#{current_site}/main/display</tt>
<h5><a name="linking-to-css-files-with-stylesheet_lin"></a> 3.1.3 Linking to CSS Files with <tt>stylesheet_link_tag</tt></h5>
The <tt>stylesheet_link_tag</tt> helper returns an HTML <tt>&lt;link&gt;</tt> tag for each source provided. Rails looks in <tt>public/stylesheets</tt> for these files by default, but you can specify a full path relative to the document root, or a URL, if you prefer. For example, to include <tt>public/stylesheets/main.css</tt>:

<tt>stylesheet_link_tag</tt><tt> </tt><tt>helper</tt><span style="font-family: DejaVu Sans;"><tt>返回一个</tt></span><tt>HTML</tt><tt> </tt><tt>&lt;link&gt;</tt><span style="font-family: DejaVu Sans;"><tt>标签给每一个提供的源。</tt></span><tt>Rails</tt><span style="font-family: DejaVu Sans;"><tt>默认在</tt><tt></tt></span><tt>public/stylesheets</tt><span style="font-family: DejaVu Sans;"><tt>查找这些文件，但是你可以指定一个全路径关联到文档目录，或者一个</tt></span><tt>URL</tt><span style="font-family: DejaVu Sans;"><tt>，如果你喜欢。例如，要导入</tt></span><tt>public/stylesheets/main.css:</tt>

&lt;%= stylesheet_link_tag "main" %&gt;

To include <tt>public/stylesheets/main.css</tt> and <tt>public/stylesheets/columns.css</tt>:

&lt;%= stylesheet_link_tag "main", "columns" %&gt;

To include <tt>public/stylesheets/main.css</tt> and <tt>public/photos/columns.css</tt>:

&lt;%= stylesheet_link_tag "main", "/photos/columns" %&gt;

To include <tt>http://example.com/main.css</tt>:

&lt;%= stylesheet_link_tag "http://example.com/main.css" %&gt;

By default, <tt>stylesheet_link_tag</tt> creates links with <tt>media="screen"</tt><tt> </tt><tt>rel="stylesheet"</tt><tt> </tt><tt>type="text/css"</tt>. You can override any of these defaults by specifying an appropriate option (<tt>:media</tt>, <tt>:rel</tt>, or <tt>:type</tt>):

<span style="font-family: DejaVu Sans;">默认情况，</span><tt>stylesheet_link_tag</tt><span style="font-family: DejaVu Sans;"><tt>创建</tt></span><tt>links</tt><span style="font-family: DejaVu Sans;"><tt>使用</tt></span><tt>media="screen"</tt><tt> </tt><tt>rel="stylesheet"</tt><tt> </tt><tt>type="text/css"</tt><span style="font-family: DejaVu Sans;"><tt>。你可以覆盖这些默认设置的任何一个通过指定一个适当的选项</tt><tt></tt></span><tt>(:media,</tt><tt> </tt><tt>:rel,</tt><tt> </tt><tt>or</tt><tt> </tt><tt>:type):</tt>

&lt;%= stylesheet_link_tag "main_print", :media =&gt; "print" %&gt;

The <tt>all</tt> option links every CSS file in <tt>public/stylesheets</tt>:<span style="font-family: DejaVu Sans;">（不包含子目录）</span>

&lt;%= stylesheet_link_tag :all %&gt;

You can supply the <tt>:recursive</tt> option to link files in subfolders of <tt>public/stylesheets</tt> as well:<span style="font-family: DejaVu Sans;">（递归导入</span><tt>public/stylesheets</tt><span style="font-family: DejaVu Sans;"><tt>所有的样式</tt>）</span>

&lt;%= stylesheet_link_tag :all, :recursive =&gt; true %&gt;

If you’re loading multiple CSS files, you can create a better user experience by combining multiple files into a single download. To make this happen in production, specify <tt>:cache</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt> in your <tt>stylesheet_link_tag</tt>:<span style="font-family: DejaVu Sans;">（使用</span>cache<span style="font-family: DejaVu Sans;">来改善用户体验）</span>

&lt;%= stylesheet_link_tag "main", "columns", :cache =&gt; true %&gt;

By default, the combined file will be delivered as <tt>stylesheets/all.css</tt>. You can specify a location for the cached asset file instead:

<span style="font-family: DejaVu Sans;">默认情况下组合文件会交付为</span><tt>stylesheets/all.css</tt><span style="font-family: DejaVu Sans;"><tt>。你可以指定一个</tt></span><tt>lacation</tt><span style="font-family: DejaVu Sans;"><tt>给</tt></span><tt>cached</tt><tt> </tt><tt>asset</tt><span style="font-family: DejaVu Sans;"><tt>文件替代：</tt></span>

<tt>&lt;%=</tt><tt> </tt><tt>stylesheet_link_tag</tt><tt> </tt><tt>"main",</tt><tt> </tt><tt>"columns",</tt>

<tt> </tt><tt>:cache</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>'cache/main/display'</tt><tt> </tt><tt>%&gt;</tt>

You can even use dynamic paths such as <tt>cache/#{current_site}/main/display.</tt>
<h5><a name="linking-to-images-with-image_tag"></a><tt>3.1.4</tt><tt> </tt><tt>Linking</tt><tt> </tt><tt>to</tt><tt> </tt><tt>Images</tt><tt> </tt><tt>with</tt><tt> </tt><tt>image_tag</tt></h5>
The <tt>image_tag</tt> helper builds an HTML <tt>&lt;img</tt><tt> </tt><tt>/&gt;</tt> tag to the specified file. By default, files are loaded from <tt>public/images</tt>.

<tt>image_tag</tt><tt> </tt><tt>helper</tt><span style="font-family: DejaVu Sans;"><tt>对于指定的文件创建一个</tt></span><tt>HTML</tt><tt> </tt><tt>&lt;img</tt><tt> </tt><tt>/&gt;</tt><span style="font-family: DejaVu Sans;"><tt>标签。默认情况，文件会从</tt><tt></tt></span><tt>public/images</tt><span style="font-family: DejaVu Sans;"><tt>被导入。</tt></span>

<span style="color: #800000;">Note</span><span style="color: #800000;">that</span><span style="color: #800000;">you</span><span style="color: #800000;">must</span><span style="color: #800000;">specify</span><span style="color: #800000;">the</span><span style="color: #800000;">extension</span><span style="color: #800000;">of</span><span style="color: #800000;">the</span><span style="color: #800000;">image.</span><span style="color: #800000;">Previous</span><span style="color: #800000;">versions</span><span style="color: #800000;">of</span><span style="color: #800000;">Rails</span><span style="color: #800000;">would</span><span style="color: #800000;">allow</span><span style="color: #800000;">you</span><span style="color: #800000;">to</span><span style="color: #800000;">just</span><span style="color: #800000;">use</span><span style="color: #800000;">the</span><span style="color: #800000;">image</span><span style="color: #800000;">name</span><span style="color: #800000;">and</span><span style="color: #800000;">would</span><span style="color: #800000;">append</span><tt><span style="color: #800000;">.png</span></tt><span style="color: #800000;">if</span><span style="color: #800000;">no</span><span style="color: #800000;">extension</span><span style="color: #800000;">was</span><span style="color: #800000;">given</span><span style="color: #800000;">but</span><span style="color: #800000;">Rails</span><span style="color: #800000;">3.0</span><span style="color: #800000;">does</span><span style="color: #800000;">not.</span>

<span style="font-family: DejaVu Sans;"><span style="color: #800000;">注意你必须指定图像文件的扩展名。以前版本的</span></span><span style="color: #800000;">Rails<span style="font-family: DejaVu Sans;">能够允许你仅仅使用图像文件名称就会被添加</span></span><span style="color: #800000;">.png<span style="font-family: DejaVu Sans;">即使没有扩增名被给出。但是</span></span><span style="color: #800000;">Rails</span><span style="color: #800000;">3.0<span style="font-family: DejaVu Sans;">不这样了</span>。</span>

&lt;%= image_tag "header.png" %&gt;

You can supply a path to the image if you like:

<span style="font-family: DejaVu Sans;">你可以给</span>image<span style="font-family: DejaVu Sans;">提供一个路径如果你喜欢：</span>

&lt;%= image_tag "icons/delete.gif" %&gt;

You can supply a hash of additional HTML options:

<code>&lt;%=</code> <code>image_tag</code><code> </code><code>"icons/delete.gif",</code><code> </code><code>{:height</code> <code>=&gt;</code><code> </code><code>45}</code><code> </code><code>%&gt;</code>

You can also supply an alternate image to show on mouseover:<span style="font-family: DejaVu Sans;">你可以提供一个在鼠标经过的备用图像。</span>

&lt;%= image_tag "home.gif", :onmouseover =&gt; "menu/home_highlight.gif" %&gt;

You can supply alternate text for the image which will be used if the user has images turned off in their browser. If you do not specify an alt text explicitly, it defaults to the file name of the file, capitalized and with no extension. For example, these two image tags would return the same code:

<span style="font-family: DejaVu Sans;">你可以提供一个备用文字给</span>image<span style="font-family: DejaVu Sans;">它们将在用户的浏览器关闭图像（显示）的时候使用。如果你不准确的指定一个</span>alt<span style="font-family: DejaVu Sans;">文本，它默认是文件的文件名，大写（首字母）并且没有扩展名。例如，下面的两个</span>image<span style="font-family: DejaVu Sans;">标签将会返回同样的代码：</span>

&lt;%= image_tag "home.gif" %&gt;

&lt;%= image_tag "home.gif", :alt =&gt; "Home" %&gt;

You can also specify a special size tag, in the format “{width}x{height}”:

&lt;%= image_tag "home.gif", :size =&gt; "50x20" %&gt;

In addition to the above special tags, you can supply a final hash of standard HTML options, such as <tt>:class</tt>, <tt>:id</tt> or <tt>:name</tt>:

&lt;%= image_tag "home.gif", :alt =&gt; "Go Home",

:id =&gt; "HomeImage",

:class =&gt; 'nav_bar' %&gt;
<h5><a name="linking-to-videos-with-video_tag"></a>3.1.5 Linking to Videos with <tt>video_tag</tt></h5>
The <tt>video_tag</tt> helper builds an HTML 5 <tt>&lt;video&gt;</tt> tag to the specified file. By default, files are loaded from <tt>public/videos</tt>.

<tt>video_tag</tt><tt> </tt><tt>helper</tt><span style="font-family: DejaVu Sans;"><tt>给指定文件创建一个</tt></span><tt>HTML</tt><tt> </tt><tt>5</tt><tt> </tt><tt>&lt;video&gt;</tt><span style="font-family: DejaVu Sans;"><tt>标签。默认的，文件会从</tt><tt></tt></span><tt>public/videos</tt><span style="font-family: DejaVu Sans;"><tt>被导入。</tt></span>

&lt;%= video_tag "movie.ogg" %&gt;

Produces<span style="font-family: DejaVu Sans;">生成</span>

&lt;video src="/videos/movie.ogg" /&gt;

Like an <tt>image_tag</tt> you can supply a path, either absolute, or relative to the <tt>public/videos</tt> directory. <strong>Additionally</strong><strong> </strong><strong>you</strong><strong> </strong><strong>can</strong><strong> </strong><strong>specify</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>:size</strong></tt><tt><strong> </strong></tt><tt><strong>=&gt;</strong></tt><tt><strong> </strong></tt><tt><strong>"#{width}x#{height}"</strong></tt><strong> </strong><strong>option</strong><strong> </strong><strong>just</strong><strong> </strong><strong>like</strong><strong> </strong><strong>an</strong><strong> </strong><tt><strong>image_tag</strong></tt><strong>.</strong> <strong>Video</strong><strong> </strong><strong>tags</strong><strong> </strong><strong>can</strong><strong> </strong><strong>also</strong><strong> </strong><strong>have</strong><strong> </strong><strong>any</strong><strong> </strong><strong>of</strong><strong> </strong><strong>the</strong><strong> </strong><strong>HTML</strong><strong> </strong><strong>options</strong><strong> </strong><strong>specified</strong><strong> </strong><strong>at</strong><strong> </strong><strong>the</strong><strong> </strong><strong>end</strong> (<tt>id</tt>, <tt>class</tt> et al).

The video tag also supports all of the <tt>&lt;video&gt;</tt> HTML options through the HTML options hash, including:

video<span style="font-family: DejaVu Sans;">标签同样支持所有的</span><tt>&lt;video&gt;</tt> HTML<span style="font-family: DejaVu Sans;">选项通过</span>HTML<span style="font-family: DejaVu Sans;">选项的</span>hash<span style="font-family: DejaVu Sans;">字典，包含：</span>
<ul>
	<li><tt>:poster</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>'image_name.png'</tt>, provides an image to put in place of the video before it starts playing.</li>
	<li><tt>:autoplay</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt>, starts playing the video on page load.</li>
	<li><tt>:loop</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt>, loops the video once it gets to the end.</li>
	<li><tt>:controls</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt>, provides browser supplied controls for the user to interact with the video.</li>
	<li><tt>:autobuffer</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt>, the video will pre load the file for the user on page load.</li>
</ul>
You can also specify multiple videos to play by passing an array of videos to the <tt>video_tag</tt>:

&lt;%= video_tag ["trailer.ogg", "movie.ogg"] %&gt;

This will produce:

&lt;video&gt;&lt;source src="trailer.ogg" /&gt;&lt;source src="movie.ogg" /&gt;&lt;/video&gt;
<h5><a name="linking-to-audio-files-with-audio_tag"></a> 3.1.6 Linking to Audio files with <tt>audio_tag</tt></h5>
The <tt>audio_tag</tt> helper builds an HTML 5 <tt>&lt;audio&gt;</tt> tag to the specified file. By default, files are loaded from <tt>public/audios</tt>.

&lt;%= audio_tag "music.mp3" %&gt;

You can supply a path to the audio file if you like:

&lt;%= audio_tag "music/first_song.mp3" %&gt;

You can also supply a hash of additional options, such as <tt>:id</tt>, <tt>:class</tt> etc.

Like the <tt>video_tag</tt>, the <tt>audio_tag</tt> has special options:
<ul>
	<li><tt>:autoplay</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt>, starts playing the audio on page load</li>
	<li><tt>:controls</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt>, provides browser supplied controls for the user to interact with the audio.</li>
	<li><tt>:autobuffer</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>true</tt>, the audio will pre load the file for the user on page load.</li>
</ul>
<h4><a name="understanding-yield"></a>3.2 Understanding <tt>yield</tt><span style="font-family: WenQuanYi Micro Hei;"><tt>未知的</tt></span><tt>yield</tt></h4>
Within the context of a layout, <tt>yield</tt> identifies a section where content from the view should be inserted. The simplest way to use this is to have a single <tt>yield</tt>, into which the entire contents of the view currently being rendered is inserted:

<span style="font-family: DejaVu Sans;">在</span>layout<span style="font-family: DejaVu Sans;">的</span>context<span style="font-family: DejaVu Sans;">（上下文）中，</span>yield<span style="font-family: DejaVu Sans;">表示一节来自</span>view<span style="font-family: DejaVu Sans;">的内容将会被插入。最简单的方法来使用这个是有一个</span>yield<span style="font-family: DejaVu Sans;">，视图的整个内容将会被</span>rendered <span style="font-family: DejaVu Sans;">插入。</span>

&lt;html&gt;

&lt;head&gt;

&lt;/head&gt;

&lt;body&gt;

&lt;%= yield %&gt;

&lt;/body&gt;

&lt;/html&gt;

You can also create a layout with multiple yielding regions:<span style="font-family: DejaVu Sans;">你也可以创建一个</span>layout<span style="font-family: DejaVu Sans;">有多个</span>yield<span style="font-family: DejaVu Sans;">区域。</span>

&lt;html&gt;

&lt;head&gt;

&lt;%= yield :head %&gt;

&lt;/head&gt;

&lt;body&gt;

&lt;%= yield %&gt;

&lt;/body&gt;

&lt;/html&gt;

The main body of the view will always render into the unnamed <tt>yield</tt>. To render content into a named <tt>yield</tt>, you use the <tt>content_for</tt> method.

<span style="font-family: DejaVu Sans;">视图的</span>body<span style="font-family: DejaVu Sans;">将会总是</span>render<span style="font-family: DejaVu Sans;">一个</span>unnamed yield<span style="font-family: DejaVu Sans;">。要</span>render<span style="font-family: DejaVu Sans;">内容给一个知名的</span>yield<span style="font-family: DejaVu Sans;">，使用</span><tt>content_for</tt><span style="font-family: DejaVu Sans;"><tt>方法。</tt></span>
<h4><a name="using-content_for"></a>3.3 Using <tt>content_for</tt></h4>
The <tt>content_for</tt> method allows you to insert content into a named <tt>yield</tt> block in your layout. For example, this view would work with the layout that you just saw:

<tt>content_for</tt><span style="font-family: DejaVu Sans;"><tt>允许你插入内容到知名的</tt></span><tt>yield</tt><span style="font-family: DejaVu Sans;"><tt>块到你的</tt></span><tt>layout</tt><span style="font-family: DejaVu Sans;"><tt>。</tt></span>

&lt;% content_for :head do %&gt;

&lt;title&gt;A simple page&lt;/title&gt;

&lt;% end %&gt;

&nbsp;

&lt;p&gt;Hello, Rails!&lt;/p&gt;

The result of rendering this page into the supplied layout would be this HTML:

&lt;html&gt;

&lt;head&gt;

&lt;title&gt;A simple page&lt;/title&gt;

&lt;/head&gt;

&lt;body&gt;

&lt;p&gt;Hello, Rails!&lt;/p&gt;

&lt;/body&gt;

&lt;/html&gt;

The <tt>content_for</tt> method is very helpful when your layout contains distinct regions such as sidebars and footers that should get their own blocks of content inserted. It’s also useful for inserting tags that load page-specific JavaScript or css files into the header of an otherwise generic layout.
<h4><a name="using-partials"></a>3.4 Using Partials</h4>
Partial templates – usually just called “partials” – are another device for breaking the rendering process into more manageable chunks. With a partial, you can move the code for rendering a particular piece of a response to its own file.

Partial templates-<span style="font-family: DejaVu Sans;">通常被称为<span style="font-family: Liberation Serif,Times New Roman,serif;">“</span></span>partials”-<span style="font-family: DejaVu Sans;">是另一个设备（单元）分解渲染过程到易于管理的多个块中。通过一个</span>partial<span style="font-family: DejaVu Sans;">，你可以渲染了一个特定块的文件，通过</span>respense<span style="font-family: DejaVu Sans;">这个文件来响应特定的代码。</span>
<h5><a name="naming-partials"></a>3.4.1 Naming Partials<span style="font-family: WenQuanYi Micro Hei;">命名</span>Partials</h5>
To render a partial as part of a view, you use the <tt>render</tt> method within the view:

&lt;%= render "menu" %&gt;

This will render a file named <tt>_menu.html.erb</tt> at that point within the view being rendered. Note the leading underscore character: partials are named with a leading underscore<span style="font-family: DejaVu Sans;">下划线</span>to distinguish<span style="font-family: DejaVu Sans;">区分</span>them from regular views, even though they are referred to without the underscore. This holds true even when you’re pulling in a partial from another folder:

<span style="font-family: DejaVu Sans;">这将会</span>render<span style="font-family: DejaVu Sans;">一个名为</span><tt>_menu.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>的文件在渲染视图的</tt></span><tt>Partials</tt><span style="font-family: DejaVu Sans;"><tt>插入点的时候。注意起始的下划线字符：</tt></span><tt><span style="color: #800000;"><strong>partials</strong></span></tt><span style="font-family: DejaVu Sans;"><tt><span style="color: #800000;"><strong>被命名为以下划线（</strong></span></tt></span><tt><span style="color: #800000;"><strong>'_'</strong></span></tt><span style="font-family: DejaVu Sans;"><tt><span style="color: #800000;"><strong>）开始的规则来区分它们和视图</strong></span></tt><tt>，即使他们（的调用没有下划线）。这在你从除当前视图所在文件夹之外调入也一样成立：</tt></span>

<tt>&lt;%=</tt><tt> </tt><tt>render</tt><tt> </tt><tt>"shared/menu"</tt><tt> </tt><tt>%&gt;</tt>

That code will pull in the partial from <tt>app/views/shared/_menu.html.erb</tt>.
<h5><a name="using-partials-to-simplify-views"></a>3.4.2 Using Partials to Simplify Views</h5>
One way to use partials is to treat them as the equivalent of subroutines: as a way to move details out of a view so that you can grasp what’s going on more easily. For example, you might have a view that looked like this:

&lt;%= render "shared/ad_banner" %&gt;

&nbsp;

&lt;h1&gt;Products&lt;/h1&gt;

&nbsp;

&lt;p&gt;Here are a few of our fine products:&lt;/p&gt;

...

&nbsp;

&lt;%= render "shared/footer" %&gt;

Here,<strong> </strong><strong>the</strong><strong> </strong><tt><strong>_ad_banner.html.erb</strong></tt><strong> </strong><strong>and</strong><strong> </strong><tt><strong>_footer.html.erb</strong></tt><strong> </strong><strong>partials</strong><strong> </strong><strong>could</strong><strong> </strong><strong>contain</strong><strong> </strong><strong>content</strong><strong> </strong><strong>that</strong><strong> </strong><strong>is</strong><strong> </strong><strong>shared</strong><strong> </strong><strong>among</strong><strong> </strong><strong>many</strong><strong> </strong><strong>pages</strong><strong> </strong><strong>in</strong><strong> </strong><strong>your</strong><strong> </strong><strong>application.</strong> You don’t need to see the details of these sections when you’re concentrating on a particular page.

For content that is shared among all pages in your application, you can use partials directly from layouts.

For content that is shared among all pages in your application, you can use partials directly from layouts.
<h5><a name="partial-layouts"></a>3.4.3 Partial Layouts</h5>
A partial can use its own layout file, just as a view can use a layout. For example, you might call a partial like this:

<span style="font-family: DejaVu Sans;">一个</span>partial<span style="font-family: DejaVu Sans;">可以使用它们自己的</span>layout<span style="font-family: DejaVu Sans;">文件，就像一个视图可以使用一个</span>layout<span style="font-family: DejaVu Sans;">。例如你可以这样调用一个</span>partial<span style="font-family: DejaVu Sans;">：</span>

&lt;%= render :partial =&gt; "link_area", :layout =&gt; "graybar" %&gt;

This would look for a partial named <tt>_link_area.html.erb</tt> and render it using the layout <tt>_graybar.html.erb</tt>. Note that layouts for partials follow the same leading-underscore naming as regular partials, and are placed in the same folder with the partial that they belong to (not in the master <tt>layouts</tt> folder).

<span style="font-family: DejaVu Sans;">这将会查找一个名叫</span><tt>_link_area.html.erb</tt><span style="font-family: DejaVu Sans;">的</span>partial<span style="font-family: DejaVu Sans;">并且在</span><tt>_graybar.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>中</tt></span>render<span style="font-family: DejaVu Sans;">和使用它。</span>

Also note that<strong> </strong><strong>explicitly</strong><strong> </strong><strong>specifying</strong><strong> </strong><tt><strong>:partial</strong></tt><strong> </strong><strong>is</strong><strong> </strong><strong>required</strong> when passing additional options such as <tt>:layout</tt>.
<h5><a name="passing-local-variables"></a>3.4.4 Passing Local Variables</h5>
You can also pass local variables into partials, making them even more powerful and flexible. For example, you can use this technique to reduce duplication between new and edit pages, while still keeping a bit of distinct content:
<ul>
	<li><tt>new.html.erb</tt><tt> </tt></li>
</ul>
&lt;h1&gt;New zone&lt;/h1&gt;

&lt;%= error_messages_for :zone %&gt;

&lt;%= render :partial =&gt; "form", :locals =&gt; { :zone =&gt; @zone } %&gt;
<ul>
	<li><tt><span style="color: #000000;"><span style="font-size: small;">edit.html.erb</span></span></tt></li>
</ul>
&lt;h1&gt;Editing zone&lt;/h1&gt;

&lt;%= error_messages_for :zone %&gt;

&lt;%= render :partial =&gt; "form", :locals =&gt; { :zone =&gt; @zone } %&gt;
<ul>
	<li><tt>_</tt><tt><span style="color: #000000;"><span style="font-size: small;">form.html.erb</span></span></tt></li>
</ul>
&lt;%= form_for(zone) do |f| %&gt;

&lt;p&gt;

&lt;b&gt;Zone name&lt;/b&gt;&lt;br /&gt;

&lt;%= f.text_field :name %&gt;

&lt;/p&gt;

&lt;p&gt;

&lt;%= f.submit %&gt;

&lt;/p&gt;

&lt;% end %&gt;

Although the same partial will be rendered into both views, Action View’s submit helper will return “Create Zone” for the new action and “Update Zone” for the edit action.

<strong>Every</strong><strong> </strong><strong>partial</strong><strong> </strong><strong>also</strong><strong> </strong><strong>has</strong><strong> </strong><strong>a</strong><strong> </strong><strong>local</strong><strong> </strong><strong>variable</strong><strong> </strong><strong>with</strong><strong> </strong><strong>the</strong><strong> </strong><strong>same</strong><strong> </strong><strong>name</strong><strong> </strong><strong>as</strong><strong> </strong><strong>the</strong><strong> </strong><strong>partial</strong> (minus the underscore). You can pass an object in to this local variable via the <tt>:object</tt> option:

&lt;%= render :partial =&gt; "customer", :object =&gt; @new_customer %&gt;

Within the <tt>customer</tt> partial, the <tt>customer</tt> variable will refer to<span style="font-family: DejaVu Sans;">引用</span><tt>@new_customer</tt> from the parent view.

In previous versions of Rails, <strong>the</strong><strong> </strong><strong>default</strong><span style="font-family: DejaVu Sans;"><strong>默认情况</strong></span><strong>local</strong><strong> </strong><strong>variable</strong><strong> </strong><strong>would</strong><strong> </strong><strong>look</strong><strong> </strong><strong>for</strong><strong> </strong><strong>an</strong><strong> </strong><strong>instance</strong><strong> </strong><strong>variable</strong><strong> </strong><strong>with</strong><strong> </strong><strong>the</strong><strong> </strong><strong>same</strong><strong> </strong><strong>name</strong><strong> </strong><strong>as</strong><strong> </strong><strong>the</strong><strong> </strong><strong>partial</strong><strong> </strong><strong>in</strong><strong> </strong><strong>the</strong><strong> </strong><strong>parent</strong>. This behavior was deprecated<span style="font-family: DejaVu Sans;">增加</span>in 2.3 and has been <strong>removed</strong><strong> </strong><strong>in</strong><strong> </strong><strong>Rails</strong><strong> </strong><strong>3.0.</strong><span style="font-family: DejaVu Sans;"><strong>（可以自己添加如上）</strong></span>

If you have an instance of a model to render into a partial, you can use a shorthand syntax:

&lt;%= render @customer %&gt;

Assuming<span style="font-family: DejaVu Sans;">假设</span>that the <tt>@customer</tt> instance variable contains an instance of the <tt>Customer</tt> model, this will use <tt>_customer.html.erb</tt> to render it and will pass the local variable <tt>customer</tt> into the partial which will refer to the <tt>@customer</tt> instance variable in the parent view.

<span style="font-family: DejaVu Sans;">假设</span><tt>@customer</tt><span style="font-family: DejaVu Sans;"><tt>实例变量包含在一个</tt><tt></tt></span><tt>Customer</tt><span style="font-family: DejaVu Sans;"><tt>的实例中，这将会使用</tt></span><tt>_customer.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>来</tt></span><tt>render</tt><span style="font-family: DejaVu Sans;"><tt>它并且传递本地变量</tt><tt></tt></span><tt>customer</tt><span style="font-family: DejaVu Sans;"><tt>给</tt><tt></tt></span><tt>partial</tt><span style="font-family: DejaVu Sans;"><tt>引用</tt></span><tt>@customer</tt><span style="font-family: DejaVu Sans;"><tt>实例变量的父视图。</tt></span>
<h5><a name="rendering-collections"></a>3.4.5 Rendering Collections</h5>
Partials are very useful in rendering collections. When you pass a collection to a partial via the <tt>:collection</tt> option, the partial will be inserted once for each member in the collection:
<ul>
	<li><tt>index.html.erb</tt></li>
</ul>
&lt;h1&gt;Products&lt;/h1&gt;

&lt;%= render :partial =&gt; "product", :collection =&gt; @products %&gt;
<ul>
	<li><tt>_product.html.erb</tt></li>
</ul>
&lt;p&gt;Product Name: &lt;%= product.name %&gt;&lt;/p&gt;

When a partial is called with a pluralized collection, then the individual instances of the partial have access to the member of the collection being rendered via a variable named after the partial. In this case, the partial is <tt>_product</tt>, and within the <tt>_product</tt> partial, you can refer to <tt>product</tt> to get the instance that is being rendered.

<span style="font-family: DejaVu Sans;">当一个</span>partial<span style="font-family: DejaVu Sans;">被通过一个多元的</span>collection<span style="font-family: DejaVu Sans;">调用，然后</span>partial <span style="font-family: DejaVu Sans;">的个别的实例会访问</span>collection<span style="font-family: DejaVu Sans;">的成员并开始被</span>rendered<span style="font-family: DejaVu Sans;">通过一个在</span>partial<span style="font-family: DejaVu Sans;">后面的变量名。在本例中</span>the partial is <tt>_product</tt>,<span style="font-family: DejaVu Sans;">并且在</span><tt>_product</tt> partial<span style="font-family: DejaVu Sans;">中，你可以引用</span>product<span style="font-family: DejaVu Sans;">来得到被开始渲染的实例。</span>

In Rails 3.0, there is also a shorthand for this. Assuming <tt>@products</tt> is a collection of <tt>product</tt> instances, you can simply write this in the <tt>index.html.erb</tt> to produce the same result:

<span style="font-family: DejaVu Sans;">在</span>Rails 3.0<span style="font-family: DejaVu Sans;">中有一个快捷操作。假设</span><tt>@products</tt><span style="font-family: DejaVu Sans;"><tt>是一个</tt></span><tt>product</tt><span style="font-family: DejaVu Sans;"><tt>实例的集合，你可以简单的在</tt><tt></tt></span><tt>index.html.erb</tt><span style="font-family: DejaVu Sans;"><tt>中写入下面的代码来产生相同的结果：</tt></span>

&lt;h1&gt;Products&lt;/h1&gt;

&lt;%= render @products %&gt;

Rails determines the name of the partial to use by looking at the model name in the collection. In fact, you can even create a heterogeneous<span style="font-family: DejaVu Sans;">合成</span>collection and render it this way, and Rails will choose the proper partial for each member of the collection:
<ul>
	<li><tt>index.html.erb</tt><tt> </tt></li>
</ul>
&lt;h1&gt;Contacts&lt;/h1&gt;

&lt;%= render [customer1, employee1, customer2, employee2] %&gt;
<ul>
	<li><tt>customers/_customer.html.erb</tt><tt> </tt></li>
</ul>
&lt;p&gt;Customer: &lt;%= customer.name %&gt;&lt;/p&gt;
<ul>
	<li><tt>employees/_employee.html.erb</tt></li>
</ul>
&lt;p&gt;Employee: &lt;%= employee.name %&gt;&lt;/p&gt;

In this case, Rails will use the customer or employee partials as appropriate for each member of the collection.
<h5><a name="local-variables"></a>3.4.6 Local Variables</h5>
To use a custom local variable name within the partial, specify the <tt>:as</tt> option in the call to the partial:

&lt;%= render :partial =&gt; "product", :collection =&gt; @products, :as =&gt; :item %&gt;

With this change, <strong>you</strong><strong> </strong><strong>can</strong><strong> </strong><strong>access</strong><strong> </strong><strong>an</strong><strong> </strong><strong>instance</strong><strong> </strong><strong>of</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>@products</strong></tt><strong> </strong><strong>collection</strong><strong> </strong><strong>as</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>item</strong></tt><strong> </strong><strong>local</strong><strong> </strong><strong>variable</strong><strong> </strong><strong>within</strong><strong> </strong><strong>the</strong><strong> </strong><strong>partial</strong>.<span style="font-family: DejaVu Sans;">你可以访问</span>item<span style="font-family: DejaVu Sans;">本地变量来访问</span><tt><strong>@products</strong></tt><tt><strong> </strong></tt><tt><strong>collection</strong></tt><span style="font-family: DejaVu Sans;"><tt><strong>的实例。</strong></tt></span>

&nbsp;

You can also<strong> </strong><strong>pass</strong><strong> </strong><strong>in</strong><strong> </strong><strong>arbitrary</strong><strong> </strong><strong>local</strong><strong> </strong><strong>variables</strong><strong> </strong><strong>to</strong><strong> </strong><strong>any</strong><strong> </strong><strong>partial</strong><strong> </strong><strong>you</strong><strong> </strong><strong>are</strong><strong> </strong><strong>rendering</strong><strong> </strong><strong>with</strong><strong> </strong><strong>the</strong><strong> </strong><tt><strong>:locals</strong></tt><tt><strong> </strong></tt><tt><strong>=&gt;</strong></tt><tt><strong> </strong></tt><tt><strong>{}</strong></tt><strong> </strong><strong>option:</strong>

&lt;%= render :partial =&gt; 'products', :collection =&gt; @products,

:as =&gt; :item, :locals =&gt; {:title =&gt; "Products Page"} %&gt;

Would render a partial <tt>_products.html.erb</tt> once for each instance of <tt>product</tt> in the <tt>@products</tt> instance variable passing the instance to the partial as a local variable <strong>called</strong><strong> </strong><tt><strong>item</strong></tt><strong> </strong><strong>and</strong><strong> </strong><strong>to</strong><strong> </strong><strong>each</strong><strong> </strong><strong>partial</strong>,<strong> </strong><strong>make</strong><strong> </strong><strong>the</strong><strong> </strong><strong>local</strong><strong> </strong><strong>variable</strong><strong> </strong><tt><strong>title</strong></tt><strong> </strong><strong>available</strong><strong> </strong><strong>with</strong><strong> </strong><strong>the</strong><strong> </strong><strong>value</strong><strong> </strong><tt><strong>Products</strong></tt><tt><strong> </strong></tt><tt><strong>Page</strong></tt><strong>.</strong>

Rails also makes a counter variable available within a partial called by the collection, named after the member of the collection followed by <tt>_counter</tt>. <strong>For</strong><strong> </strong><strong>example,</strong><strong> </strong><strong>if</strong><strong> </strong><strong>you</strong><strong>’</strong><strong>re</strong><strong> </strong><strong>rendering</strong><strong> </strong><tt><strong>@products</strong></tt><strong>,</strong><strong> </strong><strong>within</strong><strong> </strong><strong>the</strong><strong> </strong><strong>partial</strong><strong> </strong><strong>you</strong><strong> </strong><strong>can</strong><strong> </strong><strong>refer</strong><strong> </strong><strong>to</strong><strong> </strong><tt><strong>product_counter</strong></tt><strong> </strong><strong>to</strong><strong> </strong><strong>tell</strong><strong> </strong><strong>you</strong><strong> </strong><strong>how</strong><strong> </strong><strong>many</strong><strong> </strong><strong>times</strong><strong> </strong><strong>the</strong><strong> </strong><strong>partial</strong><strong> </strong><strong>has</strong><strong> </strong><strong>been</strong><strong> </strong><strong>rendered.</strong><strong> </strong>This does not work in conjunction<span style="font-family: DejaVu Sans;">结合</span>with the <tt>:as</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>:value</tt> option.

You can also specify a second partial to be rendered between instances of the main partial by using the <tt>:spacer_template</tt> option:
<h5><a name="spacer-templates"></a>3.4.7 Spacer Templates<span style="font-family: WenQuanYi Micro Hei;">间隔</span>templates</h5>
&lt;%= render @products, :spacer_template =&gt; "product_ruler" %&gt;

Rails will render the <tt>_product_ruler</tt> partial (<strong>with</strong><strong> </strong><strong>no</strong><strong> </strong><strong>data</strong><strong> </strong><strong>passed</strong><strong> </strong><strong>in</strong><strong> </strong><strong>to</strong><strong> </strong><strong>it</strong>) <strong>between</strong><strong> </strong><strong>each</strong><strong> </strong><strong>pair</strong><strong> </strong><strong>of</strong><strong> </strong><tt><strong>_product</strong></tt><strong> </strong><strong>partials</strong>.
<h4><a name="using-nested-layouts"></a>3.5 Using Nested Layouts<span style="font-family: WenQuanYi Micro Hei;">使用嵌套</span>layouts</h4>
You may find that your application requires a layout that differs slightly<span style="font-family: DejaVu Sans;">略有不同</span>from your regular application layout to support one particular controller. Rather than repeating the main layout and editing it, you can accomplish<span style="font-family: DejaVu Sans;">完成</span>this by using nested layouts (sometimes called sub-templates). Here’s an example:

Suppose<span style="font-family: DejaVu Sans;">假设</span>you have the following <tt>ApplicationController</tt> layout:
<ul>
	<li><tt>app/views/layouts/application.html.erb</tt></li>
</ul>
&lt;html&gt;

&lt;head&gt;

&lt;title&gt;&lt;%= @page_title or 'Page Title' %&gt;&lt;/title&gt;

&lt;%= stylesheet_link_tag 'layout' %&gt;

&lt;style type="text/css"&gt;&lt;%= yield :stylesheets %&gt;&lt;/style&gt;

&lt;/head&gt;

&lt;body&gt;

&lt;div id="top_menu"&gt;Top menu items here&lt;/div&gt;

&lt;div id="menu"&gt;Menu items here&lt;/div&gt;

&lt;div id="content"&gt;&lt;%= content_for?(:content) ? yield(:content) : yield %&gt;&lt;/div&gt;

&lt;/body&gt;

&lt;/html&gt;

On pages generated by <tt>NewsController</tt>, you want to hide the top menu and add a right menu:
<ul>
	<li><tt>app/views/layouts/news.html.erb</tt></li>
</ul>
&lt;% content_for :stylesheets do %&gt;

#top_menu {display: none}

#right_menu {float: right; background-color: yellow; color: black}

&lt;% end %&gt;

&lt;% content_for :content do %&gt;

&lt;div id="right_menu"&gt;Right menu items here&lt;/div&gt;

&lt;%= content_for?(:news_content) ? yield(:news_content) : yield %&gt;

&lt;% end %&gt;

&lt;%= render :template =&gt; 'layouts/application' %&gt;

#&lt;<span style="font-family: DejaVu Sans;">表达式</span>1&gt;?&lt;<span style="font-family: DejaVu Sans;">表达式</span>2&gt;:&lt;<span style="font-family: DejaVu Sans;">表达式</span>3&gt;; "?"<span style="font-family: DejaVu Sans;">运算符的含义是</span>: <span style="font-family: DejaVu Sans;">先求表达式</span>1<span style="font-family: DejaVu Sans;">的值</span>, <span style="font-family: DejaVu Sans;">如果为真</span>, <span style="font-family: DejaVu Sans;">则执行表达式</span>2<span style="font-family: DejaVu Sans;">，并返回表达式</span>2<span style="font-family: DejaVu Sans;">的结果</span>; <span style="font-family: DejaVu Sans;">如果表达式</span>1<span style="font-family: DejaVu Sans;">的值为假</span>, <span style="font-family: DejaVu Sans;">则执行表达式</span>3 <span style="font-family: DejaVu Sans;">，并返回表达式</span>3<span style="font-family: DejaVu Sans;">的结果</span>.

That’s it. The News views will use the new layout, hiding the top menu and adding a new right menu inside the “content” div.

There are several ways of getting similar results with different sub-templating schemes using this technique. Note that there is no limit in nesting levels. One can use the <tt>ActionView::render</tt> method via <tt>render</tt><tt> </tt><tt>:template</tt><tt> </tt><tt>=&gt;</tt><tt> </tt><tt>'layouts/news'</tt> to base a new layout on the News layout. If you are sure you will not subtemplate the <tt>News</tt> layout, you can <strong>replace</strong> the <tt>content_for?(:news_content)</tt><tt> </tt><tt>?</tt><tt> </tt><tt>yield(:news_content)</tt><tt> </tt><tt>:</tt><tt> </tt><tt>yield</tt> <strong>with</strong><strong> </strong><strong>simply</strong><strong> </strong><tt><strong>yield</strong></tt><strong>.</strong>

&nbsp;
