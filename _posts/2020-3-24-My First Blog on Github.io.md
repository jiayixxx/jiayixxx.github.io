---
layout:     post
title:      My First Blog on Github.io
subtitle:    "\"Hello World, Hello Blog\""
date:       2020-03-24
author:     BY
header-img: img/what-is-a-blog.png
catalog: true
tags:
    - Blog Build
---


## Something to say

After meeting many obstacles, I copied a repository from qiubaiying.

The original repository is <https://github.com/qiubaiying/qiubaiying.github.io>

Although I know nothing about html and little about GitHUb.

Or more critically, I am learning German right now.

SO everytime I start writing something with English, German jumps out and vice versa.

The reason why I start my blog is that I think it's time to sort my codes and projects out

---

## Process

I will briefly describe the way I did to build this Blog.

### 1. github part

Firstly, with the help of qiubaiying, i mean his Blog. [博客搭建详细教程](https://github.com/qiubaiying/qiubaiying.github.io/wiki/%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E8%AF%A6%E7%BB%86%E6%95%99%E7%A8%8B)

I could barely understand where to put tiles and how to make it work.

But things get difficult when I was trying to reach github.com since its DNS got polluted by GFW.

#### 1*  accelerate github access

It takes me a few minutes to deal with that. And specific solution could be found in [国内加速访问Github的办法，超级简单](https://zhuanlan.zhihu.com/p/65154116).  

This way is basically changing the pin address of domain in China by modifying the host.

By the time 2020/3/24 21:00 GMT+8, the hosts are as following:

```bash
140.82.112.4 github.com

185.199.108.153 assets-cdn.github.com

199.232.5.194 github.global.ssl.fastly.net

52.216.93.131 github-cloud.s3.amazonaws.com
```

### 2. jekyll part

Secondly, as I am writing this post, wierd thing comes up that the page i pushed does not appear on my site.

Then I realize that this process may related with jekyll and i go back to install jekyll on Windows.

The tutorial for jekyll is [jekyll on windows](http://jekyllcn.com/docs/windows/#installation)

which required for something called [Chocolatey](https://chocolatey.org/install)

After seveal command lines, i mean a lot of ones. (i am still waiting for the installation so that i could write this post)

I am quite curious that with 300Mb internet bandwidth it still takes like a year to download a 40Mb-file.

And some other wierd things come up, saying "ERROR:  Could not find a valid gem 'nokogiri' (>= 0), here is why:
          Unable to download data from <https://rubygems.org/> - SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed (<https://api.rubygems.org/specs.4.8.gz>)"

It seems like i have not proper SSL to download nokogiri and after googling for the solution, i decide to take another approach which demonstrated on <https://jekyllrb.com/docs/installation/windows/>

As i was checking my wechat message, the installation finished and finally it works.

---

### 3. How to use Jekyll

Finally, I managed to set up Jekyll environment on my windows 10. And it seems a little bit intrige to use Jekyll since I an a little white and know nothing about bash except some simple linux commandlines, however, it really helps (:.

#### 3.1 Basic commands

The first thing to do is open your cmd.exe with win+R or windows powershell and go into the repo directory.

```bash
cd {your repository directory}
```

Then you could run jekyll and see if it works using `jekyll` and if it shows as following, it proves working.

```bash
A subcommand is required.
jekyll 4.0.0 -- Jekyll is a blog-aware, static site generator in Ruby

Usage:

  jekyll <subcommand> [options]

Options:
        -s, --source [DIR]  Source directory (defaults to ./)
        -d, --destination [DIR]  Destination directory (defaults to ./_site)
            --safe         Safe mode (defaults to false)
        -p, --plugins PLUGINS_DIR1[,PLUGINS_DIR2[,...]]  Plugins directory (defaults to ./_plugins)
            --layouts DIR  Layouts directory (defaults to ./_layouts)
            --profile      Generate a Liquid rendering profile
        -h, --help         Show this message
        -v, --version      Print the name and version
        -t, --trace        Show the full backtrace when an error occurs

Subcommands:
  compose
  docs
  import
  build, b              Build your site
  clean                 Clean the site (removes site output and metadata file) without building.
  doctor, hyde          Search site and print specific deprecation warnings
  help                  Show the help message, optionally for a given subcommand.
  new                   Creates a new Jekyll site scaffold in PATH
  new-theme             Creates a new Jekyll theme scaffold
  serve, server, s      Serve your site locally
```

Codes above also shows what basic commands for Jekyll are.

### 3.2 Getting Start

1. Fork your favourite theme from [wiki of Jekyll](https://github.com/jekyll/jekyll/wiki/sites)

2. Clone the repo into your computer(optional since i live in China and the github connection sucks). Because i use github desktop, it would not be any problem to clone and push.

3. Open folder `_posts` and there should be some examples, if not, create one with name must be in the correct form.

   * yyyy-mm-dd-title.md
    example: '2020-03-24-example.md'

4. Open the file you just created with code or notepad. And add some headings required by the theme. My headings are, for example but not always be effective in your repo.

```bash
---
layout:     post
title:      My First Blog on Github.io
subtitle:    "\"Hello World, Hello Blog\""
date:       2020-03-24
author:     BY
header-img: img/what-is-a-blog.png
catalog: true
tags:
    - Blog Build
---
```

which specifies the layout, title, date and auther required by my theme.

5. Write your post with markdown and you could also download code extensions for Markdown language. It's basically a easy language of text editing and you could find how to write that in [markdown_wikipedia](en.wikipedia.org/wiki/Markdown).

6. Save the text and go back to windows command line, i.e. cmd or powershell whatever you just used to check jekyll.

7. Run command `jekyll build --w` which i prefer to use `--w` or `--watch` meaning to monitor all changes in the file so that i can preview without run more commands.

8. Before *STEP 7* you may need to run command `jekyll serve` or `jekyll s` which enables you to build the serve on your local computer and see your web on your browser in [localhost](http://localhost:4000).

9. Finally, after all the editing, push your repo onto github, after few minutes you may see your Blog article on your github.io.
