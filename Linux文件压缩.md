---
tags: [tar]
date: 2019-02-14 10:19:41
---

原文:http://blog.sina.com.cn/s/blog_407abb0d0100lajg.html


1 各种压缩 解压命令
（1） tar
**仅仅做打包动作**，相当于归档处理，不做压缩；解压也一样，只是把归档文件释放出来。

打包归档：
tar -cvf examples.tar examples   (examples为shell执行路径下的目录)

释放解压：
tar -xvf examples.tar （解压至当前shell执行目录下）
tar -xvf examples.tar  -C /path (/path 解压至其它路径)

（2）tar.gz tgz   (tar.gz和tgz只是两种不同的书写方式，后者是一种简化书写，等同处理)
Linux下使用非常普遍的一种压缩方式，兼顾了压缩时间（耗费CPU）和压缩空间（压缩比率）
其实这是对（1）的tar包进行gzip算法的压缩

打包压缩：
tar -zcvf examples.tgz examples   (examples为shell执行路径下的目录)

释放解压：
tar -zxvf examples.tar （解压至当前shell执行目录下）
tar -zxvf examples.tar  -C /path (/path 解压至其它路径)

（3）tar.bz(超高压缩率)
Linux下压缩比率较tgz大，即压缩后占用更小的空间，使得压缩包看起来更小。但同时在压缩，解压的过程却是非常耗费CPU时间。

打包压缩：
tar -jcvf examples.tar.bz examples   (examples为shell执行路径下的目录)

释放解压：
tar -jxvf examples.tar.bz （解压至当前shell执行目录下）
tar -jxvf examples.tar.bz  -C /path (/path 解压至其它路径)

真实例子:

![1550110708.png](https://i.loli.net/2019/02/14/5c64d08f7d00f.png?filename=1550110708.png)

(4) tar.bz2
较tar.bz有着更快速的效率。所使用的命令与（3）相同，不再赘述。

(5) .gz
压缩：
gzip-d examples.gz examples

解压：
gunzip examples.gz

（6） .Z
压缩：
compress files

解压：
uncompress examples.Z

(7) tar.Z  （这个和tar.gz的解压只在大小写(Z与z)之分，如果在解压时候不小心将z打成Z时候无法解压tar.gz了）
压缩：
tar -Zcvf examples.tar.Z examples

解压：
tar -Zxvf examples.tar.Z

(8) .zip
压缩：
zip -r examples.zip examples (examples为目录)

解压：
zip examples.zip

(9) .rar
压缩：
rar -a examples.rar examples

解压：

rar -x examples.rar

2 各种压缩比率，占用时间对比

为了保证能够让压缩比率较为明显，需选取一个内容较多、占用空间较大的目录作为Demo。我将自己Ubuntu 9.10中/user/local整个目录作为范例，/user/local内所有文件总计大小为877.7MB。

在此，我们定义 压缩比率=原内容大小/压缩后大小，压缩比率越大，则表明压缩后占用空间的压缩包越小

（1）.tar
tar -cvf local.tar /usr/local
打包后 local.tar: 892.6MB, 耗费时间：55 s (秒)。此实验打包后.tar后反倒比原来文件内容更大，出乎意料！
压缩比率为877.7/892.6=0.98（居然不是相同，等于1！）

  tar -xvf local.tar
释放877.7MB(与原/usr/local大小完全相同，符合预期)，耗费时间：78 s，比打包耗时

对于.tar而言，打包比释放较为快速，但打包后的.tar比原目录内容所占空间要大。

(2) .tgz
tar -zcvf local.tgz /usr/local
打包后 local.tgz: 344.1MB, 耗费时间： 146 s (秒)。此实验说明.tgz压缩到50%以下的空间，具体为 压缩比率=877.7/344.1 =2.55

tar -zxvf local.tar
解压877.7MB，耗费时间： 56 s。 此与.tar 正相反，解压比打包省时了很多，接近到打包时间的三分之一。

(3) .tar.bz
tar -jcvf local.tar.bz /usr/local
打包后local.tar.bz: 318.4 MB  耗费时间：330 s（即5 m 30 s，非常之漫长！）
压缩比率为877.7/318.4=2.76

相对于.tgz的压缩得更小巧，但优势并不大，CPU耗费时间却多了两倍多。

tar -xcvf local.tar.bz
解压877.7 MB，耗费时间： 128 s。 此与.tgz相似，解压比打包省时很多，接近打包时间的三分之一。

（4） .tar.bz2
tar -jcvf local.tar.bz2 /usr/local
打包后local.tar.bz:  318.4 MB  耗费时间： 302 s
压缩比率为877.7/318.4=2.76

与.tar.bz打包后大小完全一致，但CPU耗费时间稍少一点。因此从此实验说明，网上一些言论称tar.bz2比tar.bz有着更大的压缩比率似乎说不通，只是速度稍快，高效了一些而已。

tar -xcvf local.tar.bz
解压877.7 MB，耗费时间：  123 s。 此与.tar.bz相差不大，略有优势。

由此可见，对于tar.bz 与tar.bz2打包后并无差异，只是tar.bz2较为快速，时间上略占上风。

其它如.zip  .Z .rar等压缩方式在Linux下使用不是非常广泛，在此不做进一步探讨实验。

综合起来，在压缩比率上： tar.bz=tar.bz2>tgz>tar
             占用空间与压缩比率成反比： tar.bz=tar.bz2tar.bz2>tgz>tar
                              解压： tar.bz>tar.bz2>tar>tgz
               从效率角度来说，当然是耗费时间越短越好

因此，Linux下对于占用空间与耗费时间的折衷多选用tgz格式，不仅压缩率较高，而且打包、解压的时间都较为快速，是较为理想的选择。

如果对效率很关切，非常在乎时间的话，选择tgz tar的方式都不错。当然，如果disk空间较为紧张，非常在乎空间的话，选择高压缩比率的tar.bz2则更为适宜。

结论：

再一次印证了物理空间与时间的矛盾（想占用更小的空间，得到高压缩比率，肯定要牺牲较长的时间；反之，如果时间较为宝贵，要求快速，那么所得的压缩比率一定较小，当然会占用更大的空间了）。

