# 流程说明
很久不更新，再拿出来发现完全忘记怎么玩了，那就写个readme帮忙自己快速回忆；

* 安装Node.js
* 如果npm不稳定，可以更新npm源
``
npm config set registry https://registry.npm.taobao.org npm info underscore
``
* 由于使用的是Hexo博客框架，所以第一步先安装全局Hexo
``
npm install -g hexo-cli
``
* 在根目录下获取package.json中的依赖文件
``
npm install
``
* markdown文件生成静态页面
``
hexo generate
``
* 启动Hexo服务器
``
hexo server
``
* 创建文章
``
hexo new "filename"
``
* 发布文章到Github
``
hexo deploy
``