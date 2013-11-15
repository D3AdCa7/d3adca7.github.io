---
layout: post
title: "CSAW CTF Quals 2013 review"
description: ""
category: review
tags: []
---
{% include JB/setup %}

## Trivia
<hr>
五道简单上手题，第二个的答案是DICKS还是很逗的。

## Recon
<hr>
即是所谓的侦察题。
### Alexander Taylor - 100 Points
<hr>
这道题一开始的方向放在了这个人的网站fuzyll.com上，浪费了无数时间进去，SMTP的所有指令可能包含的trick都测试了，之后发现这人再judges里的照片格式不太对，搞下来之后一看里面text好多信息，把最后一段52个byte长度的信息和CSAW xor一下得到key

### Julian Cohen - 100 Points
<hr>
很容易就能找到这人是HockeyInJune，之后发现网站，其实有三个之多：

```
http://omnom.nom.co/
http://catsoncupcakes.com/
http://deathbycats.com/

```
ping了之后得到ip

```
23.23.196.37
```
进去之后看到key

### Jordan Wiens - 100 Points
<hr>
这个题提示了Michael Vario这个人，然后发现这个人写了关于PGP key的post，于是在如下地址发现了蛛丝马迹。

```
http://pool.sks-keyservers.net:11371/pks/lookup?op=vindex&search=psifertex
```
之后把

```
http://pool.sks-keyservers.net:11371/pks/lookup?op=get&search=0x9FBEBC5EA827D636
```
这个东西拖下来decode看了看，明显有信息在里面，然后是个图片。。

mark一个链接，以后有pgp的问题可以拖上去

```
http://keyserver.gazzang.net/pks/lookup?op=index&search=0xc13a8c4a5ac06dcfd869f8ce9fbebc5ea827d636
```

### Kevin Chung - 100 Points
<hr>
这个就是得思考一下这个人的经历，他从前拿过HSF的冠军，于是key的地址是

```
https://hsf.isis.poly.edu/assets/uploads/pages/previous_winners/key.txt
```

### historypeats - 100 Points
<hr>
最简单的，google，然后看twitter，然后看github，然后commit history，done

### Brandon Edwards - 100 Points
<hr>
思路和上一道题类似，google，发现id：drraid，然后github，发现sophsec，然后看commit history就找到key了。

### Odin - 100 Points
<hr>
据说每年都有一道和IRC有关的题目，于是IRC里面那个snOwDIN可疑死了。

/whois snOwDIN


or 右键 get info

发现 linkedin:chinesespies

去linkedin就发现key了

### Theodore Reed - 100 Points
<hr>
Teddy!可以和第一题并列为最难的两道，这个看了好久那个hotandspicy然后页面的提示为

```
Would you like some supersexyhotandspicyprosauce?
```
我抽了首字母sshasp出来然后始终觉得和ssh有关系，而且这网站也开这https，然后胡搞了半天。。

总觉得recon的话自己都把问题复杂化了。

正确的姿势是去把这个blog的所有外链认真看一下，发现一个youtube的视频评论里面有key。


## Web
<hr>

### Guess Harder - 100 Points
<hr>
简单题，document.cookie="admin=True"，一看见就秒了。

### Nevernote - 200 Points
<hr>
一开始注册了一个帐号搞了搞没搞动去看misc的题了，后来注册了一个帐号，两个帐号之间一发信息觉得REFERER有问题，于是在自己vps上写了个简单的PHP：

```
<?php
	$hqd = $_SERVER['HTTP_REFERER'];
	$f = fopen('csaw.txt','a');
	fwrite($f,"$hqd\n-------\n");
	fclose($f);
?>
```
发现这个必须作为index.php因为slash貌似会被转义，然后以这个作为link发email给admin，就可以看到一个referer如下

```
http://128.238.66.214/viewmessage.php?enc=vox57jtM01n9aZo0ZVNtgu755vAI0x7aJNd7iMQq7mla18C7W99UeWpCys9%2FbKFYjVkl9QwrmcHRob3RQFRyxuRAXoDDAeARE5KAp6mweoEHs90JiRpd7gbGz4fhIg2iN6TzaNNaJE4fqhgB240KfQBBT%2B3x%2FL%2BWvzMpatWbJZs%3D
```
访问这个地址就能看到管理员的名为key的链接得到key。

### herpderper - 300 Points
<hr>
看了好些个writeup，前面的步骤有的直接把request部分从代码里挖出来了，有的是修复了代码的证书问题然后用burp把request截下来看的。

有个比较好的writeup，[herpderper](http://nopsrus.blogspot.sg/2013/09/csaw-ctf-2013-web-herpderper-300-points.html)

### WidgetCorp - 400 Points
<hr>
php序列化注入


# 太懒了于是这个writeup烂尾了。。



