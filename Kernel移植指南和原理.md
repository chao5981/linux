  Kernel的移植我们依然采用老方法，利用命令make -p 生成汇编日志，我们通过分析汇编日志来推测我们需要修改哪些部分

  创建执行文件.sh，输入以下内容
  
      1 #!/bin/sh
      2 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean
      3 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- imx_v7_mfg_defconfig
      4 make V=1  ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- all -j16

  给.sh文件可执行权限后，将其日志写入到一个文件并于sourceinsight查看

  查阅发现，它编译的大部分文件都是linux的底层运行逻辑，而非外设。唯一一个外设文件是设备树文件(.dtb)。

  并且直接移植NXP的kernel也可以启动

  因此我们需要做的是:
    1.给设备树文件和编译设备树相关文件改名，让其支持设备树编译
    2.由于设备树的修改才是后续驱动开发的主要内容，因此现在设备树可以不修改，也可以提前修改网口驱动

  移植步骤如下：
    1.把arch/arm/configs里的v7_mfg_defconfig文件改名。imx_v7_defconfig 和 imx_v7_mf
g_defconfig 都可作为 I.MX6ULL EVK 开发板所使用的默认配置文件。但是这里建议使用 imx_
v7_mfg_defconfig 这个默认配置文件，首先此配置文件默认支持 I.MX6UL 这款芯片，而且重要
的一点就是此文件编译出来的 zImage 可以通过 NXP 官方提供的 MfgTool 工具烧写！！imx_v7_
mfg_defconfig 中的“mfg”的意思就是 MfgTool。

  2.设备树文件改名

  3.设备树文件夹的makefile添加改名的.dtb文件
