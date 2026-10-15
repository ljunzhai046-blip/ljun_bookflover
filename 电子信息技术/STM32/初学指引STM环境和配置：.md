#电子信息 #stm32 #vscode

# STM32CubeMX进行环境配置:

## 1. (初学指导)新建项目找到对应的板子型号 以STM32F103C8T6为例子：

  
  1.1 先创建一个新项目
    ![[stm32cube_001.png]]
    
   1.2 然后找到对应的板子，这里可以点击收藏下次可以快速找到，然后双击或点击右上叫的sart按钮就可以进入配置界面：
     ![[stm32cube_002.png]]
   
   1.3 进入配置页面后找到systemcore点击RCC,然后在右边的下拉选项中选择“Crystal/Ceramic Resonator”。然后进入到sys里，在右侧的配置下拉选项中选择“Serial Wire”
     ![[stm32cube_003.png]]
     
   ![[stm32cube_004.png]]

   1.4 然后就可以在右边的芯片那个地方找一个引脚设置为“GPIO_Output”

  ![[stm32cube_005.png]]

  1.5 时钟配置，按照最高的72hz时钟进行配置：首先把“HSI”设置为“HSE”，然后在上面选择“PLLCLK”，并在后面的“HCLK“输入”72“后按回车（不按相当于没有保存）。这样之后时钟就算是配置好了。
  ![[stm32cube_006.png]]


## 2 项目管理以及代码生成
  刚刚我们已经完成了配置的初设，现在我们就可以根据刚才的初设进行项目的代码生成了。
  
  2.1：我们先点开到”Project Manager“那，在这个里面输入项目的名字，然后确定项目文件夹保存的位置，然后把ide设置成你用的ide（我用的cmake）。
  
![[stm32cube_007.png]]

  2.2：紧接着我们点开”Code Generator“那里，勾选"Generate files"里的第一个选项。
  ![[stm32cube_008.png]]

  2.3：然后点击上方的"Help"选项的“Manage”那里。
  ![[stm32cube_009.png]]

  2.4： 然后在这个界面中下载扩展库（选你对应的板子型号),点击install下载。下载完后点击finish,扩展库就算是下载完毕了。
  ![[stm32cube_0010.png]]
  
![[stm32cube_0011.png]]

  2.5：现在就可以点击右上角的“GENERATE CODE”生成代码了。之后会有个弹窗出来表示你生成成功了，然后方便你快速定位代码生成到哪里去了。

![[stm32cube_0012.png]]

![[stm32cube_0013.png]]


## 3 VScode的插件安装

  3.1：下载“STM32CubelIDE",如图所示的这个。
  ![[stm32cube_0014.png]]

  3.2: 下载时应避免使用可能造成冲突的其他C语言插件，造成冲突时可以通过