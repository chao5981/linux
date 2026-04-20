 #这一节对实际uboot移植帮助不大，更多是素养的提升#

 首先，uboot是从arch/arm/vector.S开始启动的，通过这个文件，跳转到reset部分。具体流程如图所示：

 <img width="1612" height="895" alt="image" src="https://github.com/user-attachments/assets/bbd13110-b281-41fb-9277-f4b34b5f0982" />

 而reset部分在哪？在
