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

  


  
  
