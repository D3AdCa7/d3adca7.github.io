---
layout: post
title: "ASIS CTF Finals 2013 review"
description: ""
category:  review
tags: [ASIS CTF Finals 2013]
---
{% include JB/setup %}
## Crypto

### Rookie Agent
<hr>
这道题的重点是写小脚本做一下频度分析之后来脑补，脑洞起了非常重要的作用，之后就是对着几个搞出来的组合来分析剩下的组合对应的字母。
因为别人写了writeup于是自己就懒得写了。

### RSAng
<hr>
这道题当时没做出来很可惜，搞定的话就是第三了，好可惜。

有个很赞的writeup, [RSAng (Asis CTF 2013)](http://koala.cs.pub.ro/hexcellents/wiki/writeups/asis_rsang) by Radu Caragea from hexcellents CTF team

## Forensic

### PCAP
<hr>
首先是仔细，把每一个流的信息都收集整理一下，stream 0的信息是后来解7z用的，这题其实不太难，问题是坑爹的官方一开始给的pcap文件里面有两个流是缺包的，后来看到了news部分新放出来两个补充包于是秒掉了，按照时间顺序＋字典序，把所有那个html页面中显示的以md5命名的文件拼接起来就好了，然后输入stream 0中显示的密码就可以搞定。

### SPCAP
<hr>
分数好低的SPCAP，貌似就4个流，其中有一个包含了一个jpg文件，export出来即可。

### rm -rf
<hr>
这个题是cbmixx搞定的，就是使用foremost然后把都分好类的文件看一下。

看一个老外的blog上面写可以直接用hexeditor打开img文件然后搜索string:ASIS_

## Web
### Login
<hr>
这个题自己写了writeup，我觉得面对盲注这种效率很重要的注入，一定要写自己的小脚本，思路要灵活。

### Flag Generator
<hr>
第一天下午苦思冥想一下午的题，后来据说是可以本地跑，就是把页面的一段js拿到本地来暴力，答案是simple的md5加上那个ASIS_的头，看页面源代码的时候不够仔细。

### HackTheWorld
<hr>
一开始官方给的名为Login的cookie值是空密码的md5，即D41D8CD98F00B204E9800998ECF8427E，但后来官方update了这题一次，那个Login的值变成了B4D9AFD077B864AE835F65ABDBD0373A，有感觉是要覆盖这个值但不知道覆盖成什么样，之后hqd大神指出这个值改一下可以进，不过他说错了个事情，这个值不是md5，而是lmhash，改成user valid的lmhash：DF373898EC0C2CDCA3FA909D4FAC6D77之后就可以进入，之后觉得就是个命令注入，不过那会困的意识模糊了没有试出来，看了一下别人的writeup，就是使用\`即可，使用ASIS.\`ls\`即可看出来，flag以文件名的形式放在了当前目录下。

## Stego
### Fragmentation
<hr>
首先用这张图和media里那张diff一下，使用了一个叫做ImageMagick的工具，出现flag的上半段：

	ASIS_7da6f7fb1c6a5adad

之后就是用stepic来看一下图片里的隐藏信息，

	1ba1eccd1f5a1a9
	
### Windows
<hr>
看metadata，然后思路要广，音频一般的提取信息方法是转换单声道，倒过来放，从频谱图看信息。
视频的处理想到了就要去做尝试，不能止步于yy。

### Chessboard
<hr>
这道题当时ZTrix做出来了，真是厉害死了。。




