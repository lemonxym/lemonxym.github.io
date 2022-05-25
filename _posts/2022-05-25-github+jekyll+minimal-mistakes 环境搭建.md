---
author: lemonxym
toc: true
toc_label: "目录"
toc_icon: "cog"
---
# 博客

## jekyll + github pages

假设你已经预先有了git pages对应的仓库，即<username>.github.io，所有一切都是直接在github线上使用的。

参考资料：

https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll

https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll

https://jekyllrb.com/docs/

https://gist.github.com/widdowquinn/f255783f826f358f5de97186131419a9



永远要听话看官方文档

1. 安装ruby和bundler

安装ruby好像用安装包就可以了

安装bundler

```
gem install bundler jekyll
# 用ruby的gem命令安装bundler，bundler可以用来管理ruby项目的gem依赖
```

![image-20220524193604680]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220524193604680.png)

2. 用命令行先建立一个本地仓库（github的文档里有在仓库内某一个文件夹内存放jekyll项目代码的方法，这里只是默认放在仓库内的根目录下）

```
mkdir blog

git init
# 初始化git项目

cd blog

jekyll new --skip-bundle .
# 创建jekyll网站
```

打开当前目录下的Gemfile文件

注释掉jekyll的版本号那一行，并且修改版本号，我现在是226，版本号在https://pages.github.com/versions/

![image-20220524194648145]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220524194648145.png)

然后命令行运行

```
bundle install
```

！！踩坑：

官方文档第九点看漏了

![image-20220524194816720]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220524194816720.png)

然后就有了这个报错...（只要记得把Gemfile里jekyll版本号注释掉就好）

![image-20220524194940872]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220524194940872.png)



回到原来的话题



建议用这个命令先本地启动一下jekyll项目试试看

```
bundle exec jekyll serve
# 要先bundle add webrick，不然运行不了，详细见https://jekyllrb.com/docs/
```

启动以后在http://localhost:4000看

3. commit修改

这里充分暴露了我对github不熟悉的短板，看了一遍又一遍，还是记不住clone、push、pull...建议不太熟还是用github客户端，不建议用命令行。

！！踩坑：

不知道是不是因为现在github属于微软了，main变成了默认分支。在线上仓库里，我的代码是放在main分支的，但是在我本地仓库里（在他们俩还没有建立关联的时候），我是放在master分支的（这个原因也未明，当时用了bundle init命令也有可能我用的git init时电脑里git的版本太老了？）。

commit修改以后，到线上仓库那里去pull requests，结果它说没有可以对比的地方。后来一通乱找，在错误的路上越走越远。

最后才想到切换分支就好了

![image-20220524195819447]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220524195819447.png)

学了这么多年，甚至没有对git熟悉，更别提熟悉它的原理了，学校里那套和社会很脱节，确实有点失败吧，但是现在开始好好做就好。



回到原话题，在同一个分支commit修改就好。

打开https://<username>.github.io/就可以看到修改了



## 改主题（minimal mistakes）

参考：

https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/

有两种方法：gem-based method和remote theme method

如果用gem-based method就等于让minimal-mistakes-jekyll 这个gem来帮你管理这个网站，很多相关文件都放在那个gem的目录下，而不放在当前这个项目myblog下。这样本地的myblog commit到<username>.github.io仓库上是没办法用的

![image-20220525083427320]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220525083427320.png)

所以用remote theme method。

第一步这个创建或替换Gemfile内容，纠结了半天是要整个替换？我自己的做法是复制粘贴，但是注意和原文件重复了的行留一行就好。

![image-20220525083802790]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220525083802790.png)

其他按他说的做就好，没啥问题

![image-20220525084046786]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220525084046786.png)

## 坑：带图片的markdown post

上传post后图片不显示，我写的是绝对路径，图片文件夹放在_post/attachments里，后来用了相对路径，也不行。

看到minimal mistakes的作者说

![image-20220525102130024]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220525102130024.png)

放到了/assets/images文件夹里，用相对路径用绝对路径都不行，非得加上一个{{ site.url }}{{ site.baseurl }}才行。

minimal mistakes支持的是kramdown：https://www.fabriziomusacchio.com/blog/2021-08-11-Minimal_Mistakes_Cheat_Sheet/#via-markdown，但是typora不支持kramdown

![image-20220525102637334]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-25-github+jekyll+minimal-mistakes 环境搭建/image-20220525102637334.png)

也就是每次写完还得手动改，加上{{ site.url }}{{ site.baseurl }}，加上之后本地用typora又看不了，还得复制两份。想着要不换主题吧，但是minimal mistakes的文档最齐全...

github pages支持kramdown和GFM两种markdown，typora也支持后者，也尝试了修改项目的markdown：https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/setting-a-markdown-processor-for-your-github-pages-site-using-jekyll，没用。

如果是我资料查漏了，请告诉我，感激不尽。