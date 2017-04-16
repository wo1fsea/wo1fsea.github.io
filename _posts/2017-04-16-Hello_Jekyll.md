---
layout: post
title: "Hello, Jekyll!"
date: 2017-04-16 10:38:34 +0800
categories: Daily
keywords: Blog
---

把 Github 上这个 Blog 从 [Hexo](https://hexo.io) 迁移到了 [Jekyll](http://jekyllrb.com) , 原因本是贪图 Jekyll 是 Github Page 官方支持的，可以不用在本地生成直接提交原始文件由 Github Page 生成转换后的页面。后来发现，Github Page 并不支持插件，直接使用默认的转换无法生成 categories 。辗转又发现了，其实可以使用 [Travis CI](https://travis-ci.org) 自动跑运行生成过程并提交到相应的 Github 分支上，就在这个 Blog 的 Repository 里部署了 Travis CI 自动从 draft 分支生成页面提交到 master 里。这个 [.travis.yml](https://github.com/wo1fsea/wo1fsea.github.io/blob/draft/.travis.yml) 里还是很容易看出是具体怎么做的。
今天把原来的数据全部转换了一下到 Jekyll 的格式上，主要是修改了文件名和每篇文章前面的描述。使用了 [whiteglass](https://github.com/yous/whiteglass) 主题，主题设置的字体不是很适合中文，修改了一下页面字体。使用 Jekyll 还有另一个好处，即是 Jekyll 本身支持 LaTeX 公式，可以在 Markdown 里面方便地书写 LaTeX 公式，详见 [Math SupportPermalink](https://jekyllrb.com/docs/extras/) 。原来 [生存技能 01. Markdown & LaTex Formulas](https://wo1fsea.github.io/2016/11/26/Survival_Skill_Markdown_and_LaTex_Formulas/) 里用 google chart api 生成 LaTex 公式图片的过程可以不必使用了。
