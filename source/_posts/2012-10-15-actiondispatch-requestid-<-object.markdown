---
layout: post
title: "ActionDispatch::RequestId < Object"
date: 2012-07-03 13:15
comments: true
categories: [rails]
tags: [rails,requestId]
---
## ActionDispatch::RequestId < Object
Makes a unique request id available to the action_dispatch.request_id env variable (which is then accessible through <a href="http://api.rubyonrails.org/classes/ActionDispatch/Request.html#method-i-uuid">ActionDispatch::Request#uuid</a>) and sends the same id to the client via the X-Request-Id header.

The unique request id is either based off the X-Request-Id header in the request, which would typically be generated by a firewall, load balancer, or the web server, or, if this header is not available, a random uuid. If the header is accepted from the outside world, we sanitize it to a max of 255 chars and alphanumeric and dashes only.

The unique request id can be used to trace a request end-to-end and would typically end up being part of log files from multiple pieces of the stack.

<a href="http://api.rubyonrails.org/classes/ActionDispatch/RequestId.html">http://api.rubyonrails.org/classes/ActionDispatch/RequestId.html</a>

<a href="http://stackoverflow.com/search?q=rails+RequestId">http://stackoverflow.com/search?q=rails+RequestId</a>
