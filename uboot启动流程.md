
uboot文件夹含义解释
<img width="1136" height="748" alt="533552e502eda5d0ac4aebc362a62541" src="https://github.com/user-attachments/assets/e12c473b-34b5-4994-8a91-aa55e5e4af8a" />


<img width="807" height="716" alt="c46db67195045b5f2305eaad936af32e" src="https://github.com/user-attachments/assets/ee8616df-ecbd-4413-b37e-984b012f4978" />


makefile文件详解
<img width="1320" height="622" alt="41ba77a21d80d56c94e0596caf6d39e4" src="https://github.com/user-attachments/assets/181a2714-265f-4d0f-a8a3-2f987b00c31f" />

make指令流程
<img width="968" height="745" alt="1fef63d5d13085cc883f325bde44a904" src="https://github.com/user-attachments/assets/7c15f43b-f4ff-4cb5-ae75-644f99348a7a" />

前俩篇的具体博客，写的比正点原子简洁

https://blog.csdn.net/xi_xix_i/article/details/134918576

uboot启动流程大体框架
<img width="949" height="650" alt="ab6b31d5c4021d83f88379b7dee485ad" src="https://github.com/user-attachments/assets/9a38c9a8-8487-4350-8ec3-7b983caad86d" />

uboot启动流程代码详解
<img width="1053" height="485" alt="3717774c1ab49f8d19a1c61ea2c49c4a" src="https://github.com/user-attachments/assets/a372177c-6958-41a9-b570-87bfd2672336" />

vector.S关键步骤
<img width="995" height="859" alt="image" src="https://github.com/user-attachments/assets/29bd5454-fd39-4910-9e27-adaef1b8d915" />


Start.S关键步骤

reset部分
<img width="926" height="836" alt="image" src="https://github.com/user-attachments/assets/82e28fb6-4a43-4572-8897-b973abfee208" />

<img width="960" height="781" alt="image" src="https://github.com/user-attachments/assets/893ad8ba-4c64-4148-b3de-6a01d50855f3" />

cpu_init_cp15部分
<img width="808" height="795" alt="image" src="https://github.com/user-attachments/assets/0cc5511a-f673-4e57-a2cf-2a4ee2cba962" />

cpu_init_crit部分
<img width="873" height="667" alt="image" src="https://github.com/user-attachments/assets/4bada7b1-0e18-4d28-a2b0-2f5fe7744906" />

lowlevel_init.S部分
<img width="1251" height="840" alt="image" src="https://github.com/user-attachments/assets/9c173a51-2c4e-4449-8140-bd5cca5301c5" />

执行完毕后，回到Start.S跳转到_main

crt0.S部分
<img width="973" height="832" alt="image" src="https://github.com/user-attachments/assets/f6b2e021-40bc-46f7-8e4e-6edbacb99b85" />

<img width="887" height="451" alt="image" src="https://github.com/user-attachments/assets/cd7082f7-ba1c-4f84-bd10-3d730c8ecede" />

<img width="1077" height="335" alt="image" src="https://github.com/user-attachments/assets/c29f482a-d12c-4405-ac81-d021b3fade15" />

<img width="1299" height="796" alt="image" src="https://github.com/user-attachments/assets/110829cb-4b66-488b-aef1-d9870d7ece7f" />

到board_init_f部分

<img width="743" height="804" alt="image" src="https://github.com/user-attachments/assets/8c73aca9-0e25-4dc4-8d45-03ee35484d5f" />

board_init_r部分

<img width="909" height="825" alt="image" src="https://github.com/user-attachments/assets/cbd9efea-0f84-440a-b62d-5a6412507081" />


也是调用一个大数组，然后调用run_main_loop


最后到main.c部分
<img width="914" height="840" alt="image" src="https://github.com/user-attachments/assets/b5136ee5-9bf6-47c8-b581-59a143cce5a0" />

整个uboot就启动完成











