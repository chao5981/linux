##uboot移植##

  网上的uboot移植指南，有一种知其然而不知其所以然的感觉。因此我自己理了一份uboot移植指南，方便自己回忆和修改，也希望能给探究uboot移植的朋友们
提供一个新的思路

  移植uboot，顾名思义就是把已有的uboot适配于自己的板子上，那么在这个过程中，我们必然需要修改某些部分使其能在我们自己的板子跑起来。

  那么，我怎么知道我要修改哪些部分呢？

  要想知道我们需要修改哪些部分，我们先得把和自己板子类似的uboot源码找到吧，不然的话那可得改死自己了！

  我们可以放心的是，一般地做应用层的公司，是不会在驱动层面花太多时间的，一般地，都会以芯片公司的开发板作为蓝本，在这个上面上进行小改，所以，
uboot里面的大部分文件夹我们是不需要进行改动的。

  就以正点原子的EMMC版的IMX6ULL为例，它的蓝本是IMX6UL_EVK，因此，我们只需要关注IMX6UL_EVK的uboot源码。

  知道了自己关注的是哪部分的源码，那么我们不妨编译一下，**看看它编译的时候编译了哪些文件，那么不就知道我们要留意哪些文件了吗？**

  在编译之前，我们至少了解uboot是如何编译的。

  具体请见**uboot启动流程.md**一节，这里就简单点说。

  Makefile需要一个.config文件告诉它要编译uboot众多文件中的哪些文件，而.config文件需要一个xxx_deconfig文件生成。因此，逻辑链为：我们需要
make一个xxx_deconfig文件以生成一个.config文件，然后再make编译相关的uboot源码。

  芯片厂家都会自己写一个deconfig文件在configs文件夹中，由于我用的是emmc存储的，512运行内存的IMX6ULL开发板，因此我在configs文件中找mx6ull_14x14_ddr512_emmc_defconfig文件，这就是我用来生成.config的配置文件

  为了省去重复输入命令的麻烦，我创建了一个执行文件imx6ul_emmc.sh，代码如下

    1 #!/bin/bash
    2 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean
    3 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- mx6ull_14x14_ddr512_emmc_defconfig
    4 make V=1 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j12                                                       

  第一行是固定格式，为了让linux系统知道这个文件是一个执行文件

  第二行指定编译器并先进行一次清除

  第三行先make一下mx6ull_14x14_ddr512_emmc_defconfig，生成.config文件

  第四行make编译内核，打印编译日志，-j12是让电脑用 12 个线程同时编译内核，加块编译速度

  最后给文件执行权限，输入./imx6ul_emmc.sh -p >makefile.txt

  make -p > xxx 是一个非常常用的命令，即把编译日志输入到xxx文件中，这里我就将其写入到makefile.txt中

  最后我们传到window系统用source_insight4查看。看起来非常复杂，但是还是有分析办法的，让我们一步一步看

  <img width="1519" height="746" alt="image" src="https://github.com/user-attachments/assets/50a2e73d-4eed-469d-a405-d4faad982fa0" />

  后面很长很长的语句是上一个命令的具体执行，为什么是这样显示的呢？因为我们make的时候是编译的顶层Makefile，顶层Makefile会调用底层makefile，后面的一长串就是底层makefile正在执行的部分。

  <img width="1536" height="724" alt="image" src="https://github.com/user-attachments/assets/bfa04ab2-60a5-4c29-a02f-994c8a1d9eb4" />

  终于到了我们能看得懂的部分了！它把.config指定的相关架构，通用代码，命令，板级配置文件的C文件/汇编文件编译成.o文件，然后打包成built-in.o文件

  我们继续往下翻阅，又能看到它编译了driver,net,fs,lib文件夹

  最后一步：
  <img width="1504" height="647" alt="image" src="https://github.com/user-attachments/assets/84f96977-b37a-4eb9-a0d6-f6f57482b039" />

  于是，我们对uboot移植部分分析完毕。


  由上面出现的文件，我们能推测出在移植uboot中我们需要做些什么。

  1.创建自己的板子文件夹，不要直接在原版本上修改而污染源码。也就是给相关文件改个名字方便辨认和后续修改
  2.根据板子外设的变化修改板级文件

  步骤如下：
  
  第一步：复制相应的defconfig文件，重新命名为自己的defconfig，并修改内容

  我现在所用的蓝本是mx6ull_14x14_evk_emmc_defconfig，于是复制并重新命名，并修改里面的板子名字

  <img width="1110" height="201" alt="image" src="https://github.com/user-attachments/assets/15bca4b2-d5f0-470f-b745-08daf80d912e" />

  只需要修改红色框住的地方即可。蓝色框内无需修改

  为什么呢？首先，第一个红色框指定生成 i.MX 处理器启动所需的“镜像头”时，用到的初始化配置文件。
  
  第三行的红框也好理解，这一行的作用是指定编译的板子类型，也就是相应的板级文件，会影响其他文件的编译，如图
  <img width="1270" height="759" alt="image" src="https://github.com/user-attachments/assets/aaa29bd2-6463-4075-865e-27db6575a486" />


  第一行的篮框可以不修改的原因是，这个是EMMC驱动的宏定义，在mx6ullevk.c板级文件中有这样的定义

  <img width="1838" height="922" alt="b0815369bfb4caf1a86a5d6533869b89" src="https://github.com/user-attachments/assets/7589a59b-7c48-4c20-aa00-5e95f8e9fd3b" />

  并且这个宏定义只有在mx6ullevk.c文件中存在，并且有很多，要改的话很费劲，所以建议不改

  第二步：修改板级文件夹
    进入include/configs目录下，找到对应的板级文件，我对应的是mx6ullevk.h，只需要把宏定义改掉即可

  <img width="1136" height="911" alt="image" src="https://github.com/user-attachments/assets/7352689c-4dc0-48a4-ad2d-2f4f275a4d85" />

  拷贝一份板级文件夹，进入board/freescale中，将对应的文件夹拷贝一份，我对应的是mx6ullevk

  把mx6ullevk.c文件名字改名(里面不包含mx6ullevk的宏定义和引用，所以里面不需要动)；把Makefile文件的obj-y名字改掉；把Kconfig里面的if判断和default改掉；把imximage.cfg的mx6ullevk改掉；把MAINTAINERS里面的mx6ullevk名字改掉并删除config俩行，没用。

  其实改完这些第一步就算完成了，但是如果要修改图形化配置的话，还需要往下做：

  修改对应的图形化配置文件

  <img width="670" height="493" alt="696136a5a5656c7d619b80f81e3e4f85" src="https://github.com/user-attachments/assets/85a9e56c-b66a-4497-a123-d6e4fb136f60" />

  <img width="670" height="60" alt="image" src="https://github.com/user-attachments/assets/c0400611-a7a0-463c-b334-cf005b532763" />


  添加这些，为了支持图形化配置


  如果编译完成后，grep一下你Include/configs修改的.h文件名字，例如：
        
      grep -nR "mx6ull_alientek_emmc.h

  如果输出类似于这样，就代表板子添加成功了！

  <img width="1526" height="946" alt="image" src="https://github.com/user-attachments/assets/e4947325-b6a9-4a05-aeb4-443b5206940f" />

  <img width="1424" height="907" alt="image" src="https://github.com/user-attachments/assets/68229244-c6f4-4ece-8d4c-ed0770f99961" />

  接下来我们我们进入到第二步：根据板子外设的变化修改板级文件的驱动

  一般的板子的驱动修改都在板级文件中修改，除非没有驱动，那就得自己写了......

  我们要修改的驱动有：串口 DDR 网口 EMMC等

  这些都在板级文件夹有定义，要改可以直接在这改动

  在我现在的板子上，串口是不变的，EMMC启动和DDR是NXP厂家封装好的，现在就差一个网口和官方的不一样，因此我只需要修改网口驱动即可。

  

  



  



  
  
