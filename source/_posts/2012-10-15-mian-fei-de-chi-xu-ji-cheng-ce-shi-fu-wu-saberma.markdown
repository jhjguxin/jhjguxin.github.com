---
layout: post
title: "免费的持续集成测试服务 --saberma"
date: 2012-04-03 23:18
comments: true
categories: [专业]
tags: [travis-ci,持续集成测试]
---
## 免费的持续集成测试服务 --saberma
<a href="https://github.com/saberma/shopqi">ShopQi</a> 大部分的功能都是被自动化测试覆盖的，每次代码提交之前如果都要在本地把所有测试跑一遍所花费的时间也不少。所以我们需要有一个服务器，代码提交后自动触发运行所有测试用例。

排除自建服务器这条路子，就只能在网上寻找了，优先考虑免费的

当时(几个月前)找的时候，travis-ci还没有出来，其他收费的持续集成也不太满意。
在一次偶然的机会，看到github上某个项目的README中显示了一个building status的图标，才发现了 <code>travis-ci</code>

从6月初使用到现在， <code>travis-ci</code> 的功能已经非常完善稳定，因此推荐给大家试用一下
<h2>travis-ci</h2>
A distributed build system for the open source community.
专为开源社区打造的分布式编译系统，也就是持续集成测试了。

<img src="http://saberma.me/images/article/travis/travis.png" alt="" />

它有以下特点:
<ol>
	<li>免费，虽然是免费，但比收费的还要简单好用!</li>
	<li>支持多语言: 支持Clojure Erlang Javascript(Node.js) PHP Ruby，我只试用了Ruby</li>
	<li>支持多环境: 例如同时测试项目在 ruby1.8.6, 1.9.2, 1.9.3, jruby等的运行情况，支持多数据库mysql, postgres等</li>
	<li>支持浏览器headless测试: 结合Xvfb可以跑集成测试了</li>
	<li>非侵入式，只需要一个.travis.yml，就可以了，项目不用做过多的改动</li>
</ol>
<h3>配置.travis.yml</h3>
项目的根目录要加入 <code>.travis.yml</code> 文件
在里面指定rvm使用的ruby版本，指定bundler的运行参数等等

可以参考 <a href="https://github.com/saberma/shopqi/blob/master/.travis.yml">.travis.yml</a>
<h3>使用</h3>
要有一个 <a href="https://github.com/saberma">github</a> 帐号，在 <a href="http://travis-ci.org/">travis-ci网站</a> 使用该帐号登录
在 <a href="http://travis-ci.org/profile">profile</a> 选中您要使用的 项目 就可以了

以后向github提交代码时，travis-ci就会自动跑测试了，测试結果会发送到邮箱

<img src="http://saberma.me/images/article/travis/building.png" alt="" />
