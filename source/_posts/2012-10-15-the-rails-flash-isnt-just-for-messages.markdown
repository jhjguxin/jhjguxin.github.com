---
layout: post
title: "The Rails flash isn't just for messages"
date: 2012-08-24 00:04
comments: true
categories: [rails,ruby,web]
tags: [Analytics,events,flash,Google,message,rails]
---
## The Rails flash isn't just for messages
<a href="http://guides.rubyonrails.org/action_controller_overview.html#the-flash">The Rails flash</a> is typically used for short messages:

app/controllers/sessions_controller.rb
<pre>&lt;code&gt;redirect_to root_url, notice: &quot;You have been logged out.&quot;&lt;/code&gt;</pre>
But it can be used for more than that, any time that you redirect and want to pass along some state without making it part of the URL.

These are some things I’ve used it for.
<h2>Identifiers for more complex messages</h2>
Maybe you want to show a more complex message after signing up, containing things like links and bullet points.

Rather than send all that in the flash, you can send some identifier that your views know how to handle.

This could be the name of a partial:

app/controllers/users_controller.rb
<pre>class UsersController &lt; ApplicationController
  def create
   @user = actually_create_user
   flash[:partial] = &quot;welcome&quot;
   redirect_to some_path
 end
end</pre>


&nbsp;
app/views/layouts/application.html.haml
</figcaption>
<div>
<pre>- if flash[:partial]
 = render partial: &quot;shared/flashes/#{flash[:partial]}&quot;</pre>
app/views/shared/flashes/_welcome.html.haml

</div>

<pre>%p Welcome!
 %ul
   %li= link_to(&quot;Do this!&quot;, this_path)
   %li= link_to(&quot;Do that!&quot;, that_path)</pre>
</figure>

&nbsp;


Or just a flag:



&nbsp;

app/controllers/users_controller.rb
<div>
<pre>&lt;code&gt;flash[:signed_up] = true redirect_to root_path&lt;/code&gt;</pre>
</div>
</figure> <figure> <figcaption>app/views/welcomes/show.html.haml</figcaption>
<div>
<pre>&lt;code&gt;- if flash[:signed_up] %p Welcome!&lt;/code&gt;</pre>
</div>
</figure>

&nbsp;

&nbsp;

&nbsp;
<h2>Pass on the referer</h2>
Say you have some filter redirecting incoming requests. Maybe you’re detecting the locale and adding it to the URL, or verifying credentials.

You can use the flash to make sure the redirected-to controller gets the original referer.

&nbsp;

<figure> <figcaption>app/controllers/application_controller.rb</figcaption>
<div>
<pre>class ApplicationController &lt; ActionController::Base
 before_filter :make_locale_explicit

  private
  def make_locale_explicit
    if params[:locale].blank? &amp;&amp; request.get?
      flash[:referer] = request.referer
      redirect_to params.merge(locale: I18n.locale)
    end
  end
end</pre>
</div>
</figure>

&nbsp;

&nbsp;

&nbsp;

Now, any controller that cares about the referer could get it with:
<pre>&lt;code&gt;flash[:referer] || request.referer&lt;/code&gt;</pre>
<h2>Google Analytics events</h2>
Say you want to track a <a href="https://developers.google.com/analytics/devguides/collection/gajs/eventTrackerGuide">Google Analytics event</a> event with JavaScript when a user has signed up. You could do something like this.

Send event data from the controller:

&nbsp;

<figure> <figcaption>app/controllers/users_controller.rb</figcaption>
<div>
<pre>class UsersController &lt; ApplicationController
  def create
    @user = actually_create_user
    flash[:events] = [ [&quot;_trackEvent&quot;, &quot;users&quot;, &quot;signup&quot;] ]
    redirect_to some_path
  end
end</pre>
</div>
</figure>

&nbsp;


Then turn it into JavaScript in your view:

&nbsp;

<figure> <figcaption>app/helpers/layout_helper.rb</figcaption>
<div>
<pre>def analytics_events
  Array(flash[:events]).map do |event|
    &quot;_gaq.push(#{raw event.to_json});&quot;
  end.join(&quot;\n&quot;)
end</pre>
</div>
</figure> <figure> <figcaption>app/views/layouts/application.html.haml</figcaption>
<div>
<pre>:javascript
  = analytics_events</pre>
</div>
</figure>

&nbsp;

&nbsp;

&nbsp;
<h2>The flash vs. params</h2>
You may have considered that any of the above could have be done with query parameters instead. Including common flash messages:
&nbsp;

<figure> <figcaption>app/controllers/sessions_controller.rb</figcaption>
<div>
<pre>redirect_to root_url(notice: &quot;You have been logged out.&quot;)</pre>
<span style="font-size: small;"><span style="line-height: normal;">
</span></span>

</div>
</figure> <figure> <figcaption>app/views/layouts/application.html.haml</figcaption>
<div>
<pre>- if params[:notice]
  %p= params[:notice]</pre>
</div>
</figure>

&nbsp;

Using the flash means that the passed data doesn’t show in the URL, so it won’t happen twice if the link is shared, bookmarked or reloaded. Also the URL will be a little cleaner.

Additionally, the user can’t manipulate the flash, as it’s stored in the session. This adds some protection. If the flash partial example above used <code>params</code>, a user could pass in <code>../../admin/some_partial</code> to see things they shouldn’t.
<h2>Fin</h2>
I’d love to hear about what unconventional uses you’ve put the flash to!
