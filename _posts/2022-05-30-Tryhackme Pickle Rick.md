---
author: lemonxym
title: Tryhackme "Pickle Rick" challenge
---

{{ site.url }}{{ site.baseurl }}/

昨天周日，忙着比赛没有学习。这个挑战仍然是没有完全独立完成的，看了别人的WP说这可能是Tryhackme最简单的挑战...嗯，加油吧。

总结：

1. 自己知道的东西太少且不成体系，感觉需要用上是调不出来
2. 会的工具太少，会的也学的太浅，好像除了Burpsuite，关键时刻都想不起来哪个工具了。

![image-20220530093830314]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530093830314.png)

# 1. 登录

nmap扫一下，发现两个端口

![image-20220530144052435]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530144052435.png)

（future learning：发现自己已经忘了nmap怎么用了，一是学的太浅，二是没做笔记）

不知道从那里入手，先看网页源码：

![image-20220530094029709]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530094029709.png)

用gobuster想看看网站目录，但是唯一能找到的就只有/asseets了。看一下文件目录，有一个portal.jpg，中文意思是入口。

![image-20220530111159875]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530111159875.png)

到这里我就做不下去了，后面是看了[别人的WP](https://whokilleddb.medium.com/tryhackme-pickle-rick-walkthrough-2c33bf07c77b)做的。

因为有portal这个词，就猜测会不会入口页面就叫portal.php或者portal.html？

![image-20220530143229279]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530143229279.png)

username刚才已经知道了，现在要找密码。

网上说去找robots.txt，我是听说过这件事，robots.txt是给搜索引擎的爬虫准备的，上面有相关的指令。不过我没想到这件事。

![image-20220530144257760]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530144257760.png)

robots.txt上就有密码，感觉事情没那么简单。

# 2. 第一个ingredient

用ls查看当前目录下文件

![image-20220530144444754]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530144444754.png)

输入

```
cat Sup3rS3cretPickl3Ingred.txt
```

但他不给我看

![image-20220530144558036]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530144558036.png)

搜一下[ 除了cat能显示文件内容还有什么命令也能 ](https://unix.stackexchange.com/questions/86321/how-can-i-display-the-contents-of-a-text-file-on-the-command-line)，这里说less可以

```
less Sup3rS3cretPickl3Ingred.txt
```

所以第一个答案就有了

![image-20220530145232633]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530145232633.png)



还有一个clue.txt

```
less clue.txt
```

![image-20220530145602885]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530145602885.png)

这算什么线索嘛...



到处看看发现这一层的文件最多，

```
ls {{ site.url }}{{ site.baseurl }}/..
```

![image-20220530150021356]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530150021356.png)

# 3. 其他ingredients

在{{ site.url }}{{ site.baseurl }}/{{ site.url }}{{ site.baseurl }}/tmp下找到一个名为 systemd-private-6f3168880b604d1b897e8272555f94d9-systemd-timesyncd.service-DluyTA 的文件，但是用less打不开

尝试了more、head、tail、cat全都打不开，这条路行不通



## 3.1 trick 1

[这位](https://whokilleddb.medium.com/tryhackme-pickle-rick-walkthrough-2c33bf07c77b)为了看源码中命令的黑名单，用了这个方法：

```
grep -R ""
```

我觉得这个好聪明，grep是用来搜索文件的，用户手册如下

![image-20220530164156082]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530164156082.png)

执行命令后显示界面如下

![image-20220530164250586]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530164250586.png)

查看源代码就可以看到源码

![image-20220530164613763]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530164613763.png)

所以不能使用cat、head、more、tail、nano、vim、vi指令。

（future learning：这到底是什么黑科技，我实在不知道这个大佬是怎么想到的？？）

## 3.2 trick 2

用了

```
sudo -l
```

来查看sudo权限

用户手册如下，大概意思是列出允许当前用户执行的命令。

![image-20220530164906187]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530164906187.png)

用sudo居然不用密码耶，不过就算sudo也不能执行黑名单里的命令哦

![image-20220530165233918]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530165233918.png)



把所有的子文件都列出来

```
sudo ls {{ site.url }}{{ site.baseurl }}/{{ site.url }}{{ site.baseurl }}/{{ site.url }}{{ site.baseurl }}/*
```

加了sudo和没加sudo结果不一样的，没有sudo权限有些文件不显示的。



第二个ingredients在/home/rick下

```
sudo less "{{ site.url }}{{ site.baseurl }}/{{ site.url }}{{ site.baseurl }}/{{ site.url }}{{ site.baseurl }}/home/rick/second ingredients"
```

上面有空格，一定别忘了加双引号

![image-20220530171536544]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530171536544.png)



有一个3rd.txt文件

![image-20220530165803607]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530165803607.png)

访问这个文件

```
sudo less {{ site.url }}{{ site.baseurl }}/{{ site.url }}{{ site.baseurl }}/root/3rd.txt
```

得到结果

![image-20220530165944906]({{ site.url }}{{ site.baseurl }}/assets/images/2022-05-30-Tryhackme Pickle Rick/image-20220530165944906.png)
