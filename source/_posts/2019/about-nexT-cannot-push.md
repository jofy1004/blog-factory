---
title: 关于hexo使用了nexT，但是无法push到github的问题解决
premalink: about-nexT-cannot-push.md
date: 2019-03-14 21:53:14
tags: [hexo, nexT, github, git]
categories: 疑难杂症
---
clone主题nexT后，再push，发现github的文件夹是空的，没有提示错误，本地也完成了提交，无任何change，于是百度找原因，发现一个作者跟我有类似问题。
{% blockquote %}
链接：https://calvinoshaw.github.io/2017/04/23/%E5%85%B3%E4%BA%8E-hexo-%E7%9A%84%E4%B8%BB%E9%A2%98%E6%96%87%E4%BB%B6%E5%A4%B9%E5%9B%A0%E4%B8%BA-modified-content-%E8%80%8C%E6%97%A0%E6%B3%95-push-%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/
{% endblockquote %}
但是我并没有按照上文作者的做法，我只是重新删除缓存，再次push就成功了。
{% codeblock lang:plain %}
git rm --cache themes/next

rm 'themes/next'

git status

On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   .idea/workspace.xml
        deleted:    themes/next

git add themes/next/

git commit -m "commit nexT"

git push origin master
{% endcodeblock %}
