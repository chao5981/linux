 #这一节对实际uboot移植帮助不大，更多是素养的提升#

 首先，uboot是从arch/arm/vector.S开始启动的，通过这个文件，跳转到reset部分。具体流程如图所示：

 <img width="1612" height="895" alt="image" src="https://github.com/user-attachments/assets/bbd13110-b281-41fb-9277-f4b34b5f0982" />


 而reset部分在哪？在这之前需要了解编译uboot后各个文件夹的具体作用了，如表格所示：

<img width="1136" height="748" alt="dc3b5c427481a789bb882098df828f68" src="https://github.com/user-attachments/assets/950db4bd-2b28-495c-b3f4-c16b6f37fb45" />

<img width="807" height="716" alt="9560630225c516b4dda64f15d6d7f098" src="https://github.com/user-attachments/assets/da0d4a86-10d1-4359-8b5a-c6b06f8fcc2d" />

如何理解这一堆乱七八糟的文件夹呢。可以先这样通俗的理解：

api,cmd,common,disk等文件我们均不需要管，这属于uboot的库文件，相对uboot还要底层，无需修改

arch记录着不同体系的启动代码，分析启动流程时需要了解，但其实对uboot移植帮助不大

board存储着各个板子的专属的初始化代码，因为每个板子有不同的外设布局，uboot要初始化部分外设，而外设与主控芯片的连接引脚可能是不同的，因此相关厂家需要自己写一份能用的初始化代码到uboot文件夹中

config为uboot源码的通用配置文件，不涉及外设，用于给Makefile传参，告诉Makefile需要编译哪些文件生成uboot文件

Kconfig是图形化配置文件，后面需要用到

因此，在了解的时候，我们需要关注arch,board,config,Kconfig,Makefile文件即可

 
