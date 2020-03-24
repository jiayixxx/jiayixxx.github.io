---
layout:     post
title:      My First Blog on Github.io
subtitle:    "\"Hello World, Hello Blog\""
date:       2020-03-24
author:     BY
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - 生活
---

> “🙉🙉🙉 ”

## 前言

After meeting many obstacles, I copied a repository from qiubaiying.

The original repository is <https://github.com/qiubaiying/qiubaiying.github.io>

Although I know nothing about html and little about GitHUb.

Or more critically, I am learning German right now.

SO everytime I start writing something with English, German jumps out and vice versa.

The reason why I start my blog is that I think it's time to sort my codes and projects out

<p id = "build"></p>
---

## 正文

I will briefly describe the way I did to build this Blog.

Firstly, with the help of qiubaiying, i mean his Blog. [博客搭建详细教程](https://github.com/qiubaiying/qiubaiying.github.io/wiki/%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E8%AF%A6%E7%BB%86%E6%95%99%E7%A8%8B)

I could barely understand where to put tiles and how to make it work.

But things get difficult when I was trying to reach github.com since its DNS got polluted by GFW.

It takes me a few minutes to deal with that. And specific solution could be found in [国内加速访问Github的办法，超级简单](https://zhuanlan.zhihu.com/p/65154116).  

This way is basically changing the pin address of domain in China by modifying the host.

By the time 2020/3/24 21:00 GMT+8, the hosts are as following:

140.82.112.4 github.com

185.199.108.153 assets-cdn.github.com

199.232.5.194 github.global.ssl.fastly.net

52.216.93.131 github-cloud.s3.amazonaws.com

Secondly, as I am writing this post, wierd thing comes up that the page i pushed does not appear on my site.

Then I realize that this process may related with jekyll and i go back to install jekyll on Windows.

The tutorial for jekyll is [jekyll on windows](http://jekyllcn.com/docs/windows/#installation)

which required for something called [Chocolatey](https://chocolatey.org/install)

After seveal command lines, i mean a lot of ones. (i am still waiting for the installation so that i could write this post)

I am quite curious that with 300Mb internet bandwidth it still takes like a year to download a 40Mb-file.

And some other wierd things come up, saying "ERROR:  Could not find a valid gem 'nokogiri' (>= 0), here is why:
          Unable to download data from <https://rubygems.org/> - SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed (<https://api.rubygems.org/specs.4.8.gz>)"

It seems like i have not proper SSL to download nokogiri and after googling for the solution, i decide to take another approach which demonstrated on <https://jekyllrb.com/docs/installation/windows/>

As i was checking my wechat message, the installation seems finished.

---
