#电子信息 #stm32 #vscode

# STM32CubeMX进行环境配置:

## 1. (初学指导)新建项目找到对应的板子型号 以STM32F103C8T6为例子：

  
  1.1 先创建一个新项目
    ![[stm32cube_001.png]]
    
   1.2 然后找到对应的板子，这里可以点击收藏下次可以快速找到，然后双击或点击右上叫的sart按钮就可以进入配置界面：
     ![[stm32cube_002.png]]
   
   1.3 进入配置页面后找到systemcore点击RCC,然后在右边的下拉选项中选择“Crystal/Ceramic Resonator”。然后进入到sys里
     ![[stm32cube_003.png]]