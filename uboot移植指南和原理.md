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

  好，由于我用的板子相关的deconfig

    1 #!/bin/bash
    2 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean
    3 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- mx6ull_14x14_ddr512_emmc_defconfig
    4 make V=1 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j12                                                       

  
