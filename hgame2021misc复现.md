---
title: hgame2021 misc复现
date: 2021-03-05 12:35:15## misc 2021/01/26
tags:
     -misc
toc: ture
---
## week1
### Base全家福
看题目名字就知道是base套娃，这里再总结一下base系列的特点

> Base64：A-Z 、a-z、+、/ (由于特性末尾可能会有一个或者两个=) 
>
> Base58：相比Base64，不使用数字0，字母大写OI和小写l以及符号+/
>
> Base32:A-Z、2-7、可以出现两个以上的=
>
> Base16：0-9、A-F（和十六进制不是一样吗）

根据以上可得到flag

hgame{We1c0me_t0_HG4M3_2021}

<!--more-->

### 不起眼压缩包的养成的方法

图片注释里面有

> Secret hidden IN picture

binwalk和foremost一把梭分离出一个压缩包，压缩包注释有

> Password is picture ID(up to 8 digits)

这里我直接软件爆破得到压缩包密码**70415155**

根据官方wp似乎要去**Pixiv**搜图片可以得到ID

解压得到压缩包**plain.zip**和**NO PASSWORD.txt**

打开plain.zip发现里面也有一个**NO PASSWORD.txt**

看到plain可以知道是明文攻击，txt里面还有一个提示

> By the way,i only use storage.

可以知道要把压缩模式为storage，中文版也就是存储模式，默认压缩模式是标准，

这里是个坑

![avatar](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/1.png)

选择存储模式然后在**Advanced Archive Password Recovery**选择明文攻击就好了，然后

得到密码

>  C8uvP$DP

得到一个flag.zip这里根据之前NO PASSWORD.txt提示

> Because its too strong or null.XD

知道是伪加密（直接放进去**Ziperello**也可以知道）

修改一下加密位得到

![avatar](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/2.png)

这个是Unicode在网页上面的形式，直接改txt后缀为html打开就得到flag

> hgame{2IP_is_Usefu1_and_Me9umi_i5_W0r1d}

### Galaxy

这个是流量题，用**wireshark**查看导出对象可以得到**galaxy.png**

**stegslove** **binwalk** **zsteg**一把梭都没发现什么，后来直接改一下高度得到flag

> hgame{Wh4t_A_W0nderfu1_Wa11paper}

后来看了wp知道在windows的图片查看器打开是没有问题的，但是用其他会报错，这里是crc修改过了

贴一下官方的脚本

```python
import struct
import binascii
from Crypto.Util.number import bytes_to_long
img = open("galaxy.png", "rb").read()
for i in range(0xFFFF):
stream = img[12:20] + struct.pack('>i', i) + img[24:29]
crc = binascii.crc32(stream)
if crc == bytes_to_long(img[29:33]):
print(hex(i))
```

### Word RE:MASTER

这道题我搞了好久来着（不愧是150分的题，坑多）

得到一个有密码的maimai.docx和没密码的first.docx,先看first.docx，

看到

> Fuck!我的脑子好疼！

就知道是brainfuck编码，把后缀改成zip，解压

发现有一个password.xml，里面有

> +++++ +++[- >++++ ++++< ]>+++ +.<++ +[->+ ++<]> ++.<+ ++[-> +++<] >+.<+ ++[-> - --<] >-.++ ++++. <+++[ ->--- <]>-. +++.+ .++++ ++++. <+++[ ->--- <]>-- ----. +.--- --..+ .++++ +++++ .<+++ [->-- -<]>- ----- .<

解码得到

> DOYOUKNOWHIDDEN?

这个是maimai的密码，然后打开看到一张表情包

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210305201157.png)

所以可知有SNOW隐写,把隐藏文字打开就可以看到

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210305201634.png)

这里有个怪怪的地方，我用的是wps，这两个地方打开看到的隐藏文字是不一样的，我尝试几次才搞出来的

不知道用word怎么样

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210305201456.png)

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210305201453.png)

然后保存到txt里面再

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210305201924.png)

得到flag

>hgame{Cha11en9e_Whit3_P4ND0R4_P4R4D0XXX}

这里再给一下网页端的[snow隐写](http://fog.misty.com/perry/ccs/snow/snow/snow.html)

week1体验还是比较良好的

## week2

### Tools

套娃题，直接搬官方wp

每⼀层⼯具的名字都在压缩包⽂件名给出了，⼯具使⽤的密码都在同⼀层套娃的图⽚备注⾥，直接找⼯ 具解就完事 JPHS 有图形界⾯

> java Extract Matryoshka.jpg -p '!LyJJ9bi&M7E72*JyD'
>
>  steghide.exe extract -p 'A7SL9nHRJXLh@$EbE8' -sf .\01.jpg outguess -k "z0GFieYAee%gdf0%lF" -r 02.jpg secret.txt

得到密码

> rFQmRoT5lze@4X4^@0

得到的四张图片拼一下图得到flag

> hgame{Taowa_is_N0T_g00d_but_T001s_is_Useful}

### DNS

流量题，打开**wireshark**发现经常出现一个可疑的网址

**flag.hgame2021.cf**

浏览器访问发现一直弹

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210305205523.png)

然后还有一些奇怪的地方比如

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210306132418.png)

感觉像是提示，但是后面看wp也没发现什么，挨个挨个追踪流看到了

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210306132648.png)

有提示是**SPF**，这里贴一下百度百科的解释

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210306132823.png)

直接用命令查找

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210306154439.png)

得到flag

>hgame{D0main_N4me_5ystem}

+ 用这个[网站](https://www.kitterman.com/spf/validate.html)查看也可以

### Telegraph：1601 6639 3459 3134 0892

这道题还挺好听，题目这串数字猜测是中文电码

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210306160734.png)

百度一波

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210306160905.png)

猜测要留下某个特定的频段，用**Audaticy**打开切到频谱看到

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210306161431.png)

发现了850Hz，切到波形放大到一分钟十秒左右发现有莫斯电码

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210306161737.png)

直接手动得到flag

> hgame{4G00DS0NGBUTN0T4G00DMAN039310KI}

后来看了wp，在AU里面可以看到带宽约为200Hz

然后对其进行中心频率为850Hz，带宽为200Hz的带通滤波，然后导出再

用在线工具得到flag

### Hallucigenia

题目是一张很大得到图片，用**stegslove**打开发现有

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210306162720.png)

扫出来

>gmBCrkRORUkAAAAA+jrgsWajaq0BeC3IQhCEIQhCKZw1MxTzSlNKnmJpivW9IHVPrTjvkkuI3sP 7bWAEdIHWCbDsGsRkZ9IUJC9AhfZFbpqrmZBtI+ZvptWC/KCPrL0gFeRPOcI2WyqjndfUWlNj+d gWpe1qSTEcdurXzMRAc5EihsEflmIN8RzuguWq61JWRQpSI51/KHHT/6/ztPZJ33SSKbieTa1C5k oONbLcf9aYmsVh7RW6p3SpASnUSb3JuSvpUBKxscbyBjiOpOTq8jcdRsx5/IndXw3VgJV6iO1+6j l4gjVpWouViO6ih9ZmybSPkhaqyNUxVXpV5cYU+Xx5sQTfKystDLipmqaMhxIcgvplLqF/LWZzIS 5PvwbqOvrSlNHVEYchCEIQISICSZJijwu50rRQHDyUpaF0y///p6FEDCCDFsuW7YFoVEFEST0BAA CLgLOrAAAAAggUAAAAtAAAAFJESEkNAAAAChoKDUdOUIk=

刚开始没发现什么，用了[在线网站](https://the-x.cn/base64)(这个网站真的好用，会自动识别）才发现

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210306162918.png)

在末尾看到反过来的png，根据题目的提示可以知道，要把十六进制数据反过来就可以了，直接上官方wp的脚本，写得很妙

```python
from base64 import b64decode
open('flag.png','wb+').write(b64decode(open('flag_inv_b64.txt', 'rb').read())[::-1])
```

跑完就得到

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210306164902.jpg)

可以发现还是翻转了，由于手机前置摄像头拍照是镜像的，所以俺直接拿出手机对着屏幕拍得到了

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210306164906.jpg)

得到flag

>hgame{tenchi_souzou_dezain_bu}

## week3



## week4

### Akira之瞳-1

下载得到一个raw文件，考点是内存取证，这也是我第一次做内存取证，用到了volatility

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308085709.png)

先查看系统信息，得知是Win7SP1x64,查看一下进程

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308085947.png)

查看进程，发现有一个

  ![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308090011.png)

所以我们要提取这个东东,得到1092.dmp，然后foremost它，发现有一个压缩包

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308090235.png)

得到1092.dmp 然后foremost它,发现一个压缩包，得到提示是用户密码的sha256

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308090602.png)

用命令hashdump查看一下密码的hash

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308090730.png)

把84b0d9c9f830238933e7131d60ac6436在www.cmd5.com查看一下得到密码asdqwe123

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308090833.png)

sha256一下得到压缩包密码，发现有

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308091055.png)

直接盲水印得到flag，这里有一点要注意，就是python3和python2的盲水印脚本跑出来的结果是不一样的，而这里要用python3的脚本跑得到

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/4.png)

，然后放到stegslove可以看到

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308091424.png)

hgame{7he_f1ame_brin9s_me_end1ess_9rief}

### Akira之瞳-2

又是内存取证,操作了一波在桌面发现有一个dumpme.txt

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308092038.png)

提取后发现密码和一段提示

> zip password is: 5trqES&P43#y&1TO
> And you may need LastPass

LastPass是一个插件，可以保存用户的各种密码，感觉是要我们查看用户网站的密码，解压得到

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308093305.png)

这里有个坑，

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308093354.png)

有大小却没有占用空间，把隐藏系统文件去掉

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308093557.png)

然后通过参考资料知道有MasterKeyFile(其实是看hgame2020的wp发现这么考过，想着今年也会这么出题)

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308093728.png)

[参考资料](https://xz.aliyun.com/t/2104/)

最初是思路是通过提取出lsass.exe的内存，然后用mimikatz来从lass进程提取出Master Key然后再去解密密码[参考资料](https://www.dazhuanlan.com/2020/03/25/5e7b1628278c6/)

结果出现了这个报错

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308110227.png)

然后思路就断了，后来看wp才发现是先用插件解密Cookies解出用户密码，自己还是忽略了lastpass这个提示，在github可以找到volatility一个[lasspass](https://github.com/kevthehermit/volatility_plugins/tree/master/lastpass)的插件，这里搬一下wp

>akira@kasumi:/tmp/$ volatility --plugins=./ -f secret_work.raw -- profile=Win7SP1x64 lastpass
>
> ... 
>
>Found LastPass Entry for live.com,bing.com,hotmail.com,live.com,microsoft.com,msn.com,windows.com,window sazure.com,office.com,skype.com,azure.com UserName: windows login & miscrosoft 
>
>Pasword: vIg*q3x6GFa5aFBA

然后再用**mimikatz**来获取MasterKey

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308113217.png)

然后解密Cookies

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308113435.png)

去年也考了**VeraCrypt**，这里用它打开container，用密码**!bWjAqM2z!iSoJsV*&IRV@*AVI1VrtAb**挂载

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308113722.png)

发现里面就一张图片**ADS.jpg**

可以知道是

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308114040.png)

用软件查看得到

![avater](https://kenoeblog.oss-cn-guangzhou.aliyuncs.com/image/20210308155521.png)

得到flag

>hgame{Which_0nly_cryin9_3yes_c4n_de5cribe}