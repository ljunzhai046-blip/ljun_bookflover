#电子信息  #stm32  #C语言  #vscode 
## **引入：为什么需要串口**

第一次实验中，我们让 LED 闪烁了起来。但是有一个问题：**程序运行到哪一步了？变量现在是几？程序有没有卡死？** 我们完全不知道，只能干瞪眼看着灯闪。

串口（UART）就是单片机的”嘴”。有了它，单片机可以把内部的变量、状态打印到电脑屏幕上；反过来，电脑也可以发命令控制单片机。这是以后所有项目最重要的调试手段，没有之一。

### **先分清两个容易混淆的工具**

|   |   |   |
|---|---|---|
|**工具**|**作用**|**第一次用到过吗**|
|**ST-Link**|把程序**烧录**进芯片（下载程序用）|上次已学会|
|**CH340（USB转TTL）**|单片机和电脑**通信聊天**用|上次只介绍过，本次才是主角|

一句话记住：**ST-Link** **管”灌程序”，CH340** **管”聊闲天”**。两个可以同时插在电脑上，各干各的活，互不干扰。

---

## **第一步：安装** **CH340** **驱动，配置** **VS Code** **串口监视器**

### **一、安装** **CH340** **驱动**

1. CH340 模块插上电脑后，Windows 一般会自动安装驱动。如果没有自动安装，需要手动下载：浏览器搜索 **“CH340驱动** **沁恒官网”**（CH340 是沁恒微电子 WCH 生产的芯片），进入官网下载页找到 CH341SER（Windows 版），下载解压后运行 SETUP.EXE，点击”安装”，弹出”驱动预安装成功”即可。

2. 把 CH340 模块插上电脑 USB 口。

3. 检查驱动是否成功：右键”此电脑” → 管理 → 设备管理器 → 展开”端口(COM和LPT)“，如果看到 USB-SERIAL CH340 (COM3) 之类的字样（COM 号每个人不同），说明驱动安装成功。**记住这个** **COM** **号，等下要用。**

PS: 如果下载不了也可以从上次的压缩包里找到CH340进行下载，有WINDOS和LINUX这2个版本，根据自己的操作系统下载![](file:///C:\Users\10702\AppData\Local\Temp\ksohtml30300\wps1.jpg)

点击这里的SETUP.EXE（用WINDOS系统来做演示）

![](file:///C:\Users\10702\AppData\Local\Temp\ksohtml30300\wps2.jpg) 

点击安装就可以了

![](file:///C:\Users\10702\AppData\Local\Temp\ksohtml30300\wps3.jpg) 

[图：设备管理器中识别到 CH340 的 COM 口]

![](file:///C:\Users\10702\AppData\Local\Temp\ksohtml30300\wps4.jpg) 

上面是检查到CH340的界面这个是没有插上CH340的状态

![](file:///C:\Users\10702\AppData\Local\Temp\ksohtml30300\wps5.jpg) 

这个是没有检查到CH340的界面

而通信软件可以使用这个压缩包里的串口调试助手（丁丁）来使用

打开是![](file:///C:\Users\10702\AppData\Local\Temp\ksohtml30300\wps6.jpg)这个界面

如果没有出现 COM 口：换一个 USB 口试试；检查数据线（有些线是”充电线”没有数据功能）；重新运行驱动安装程序。

### **二、在** **VS Code** **中安装串口监视器（Serial** **Monitor）**

串口数据需要用一个”串口助手”来查看和发送。第一次实验我们已经在 VS Code 里写代码、编译、烧录了，这次我们直接在 VS Code 里装一个串口插件，**写代码和看串口在同一个窗口完成**，不用额外开软件。

1. 打开 VS Code，点击左侧四个小方块图标进入插件商店，搜索 **Serial Monitor**（发布者是 Microsoft 微软官方），点击安装。

2. 安装后，按快捷键 Ctrl+Shift+P 打开命令面板，输入并选择 Serial Monitor: Focus on Serial Monitor View（或直接点击底部状态栏/侧栏出现的 “SERIAL MONITOR” 面板）。

3. 面板中设置三项：

o **Port（端口）**：选刚才设备管理器里看到的那个 COM 号

o **Baud** **rate（波特率）**：选 115200（为什么是115200，下面会讲）

o 设置好后点 **Start** **Monitoring（开始监视）**，相当于独立串口助手的”打开串口”

4. 面板下方是**发送输入框**：输入内容后按回车或点发送按钮，数据就传给单片机；上方区域是**接收区**，显示单片机发过来的数据。

小贴士：独立串口助手作为备选 如果 Serial Monitor 用起来不习惯，也可以用独立串口助手软件（群文件中提供的即可，或下载 sscom、友善串口调试助手等任意一款），功能完全一样：选 COM 口、设波特率 115200、打开串口。以后用 Keil、CubeIDE 等其他软件时，独立串口助手是通用的。 注意：同一个 COM 口同一时间只能被一个软件占用。如果 Serial Monitor 连不上，检查是不是有串口助手软件还开着这个串口，关掉即可。


## **第二步：轻量原理** **——** **三句话看懂** **UART**

2.1：### **TX** **和** **RX** **是什么：**
· **TX**（Transmit）：发送脚，数据的”嘴巴”
· **RX**（Receive）：接收脚，数据的”耳朵”
  在链接到stm32时，这两个引进必须两两交叉链接。嘴巴要传到耳朵里才有用。

2.2：波特率是什么：

波特率就是双方”说话的速度”，单位是”位/秒”。UART 没有时钟线，双方必须**提前约定好速度**才能听懂对方。常用的有 9600、115200 等，本次统一用 **115200**。

单片机设 115200，串口助手也设 115200 → 正常显示。 两边速度不一致 → 收到一堆乱码。**乱码十有八九是波特率没对上。**

2.3：一个字节是怎么发出去的（了解即可）：

发送一个字节时，线路上实际传输的是：起始位(1位) + 数据位(8位) + 停止位(1位)。这就是配置里常见的 “115200, 8, N, 1”（波特率115200、8个数据位、无校验、1个停止位）。这部分现在不用深究，记住配置时两边保持一致就行。

## **第三步：CubeMX** **配置** **USART1**

打开 CubeMX，按照第一次的方法新建 STM32F103C8T6 工程（也可以在第一次的点灯工程基础上修改，本次以新建为例）。

### **一、开启** **USART1**

1. 左侧栏找到 Connectivity → USART1，点击展开。
   ![[Pasted image 20261016171819.png]]

2. Mode 选择 Asynchronous（异步模式，就是普通串口）。

3. 选择后可以看到右侧芯片图上 **PA9** **变成绿色并标注** **USART1_TX，PA10** **标注** **USART1_RX**。这两个引脚是固定的，不用自己选。

### **二、检查串口参数**

点击下方出现的 Parameter Settings，确认参数为：

· Baud Rate（波特率）：**115200**

· Word Length（数据位）：**8 Bits**

· Parity（校验位）：**None**

· Stop Bits（停止位）：**1**

（默认值一般就是这组，确认一遍即可）

![[Pasted image 20261016172032.png]]
### **三、开启串口中断（实验二要用）**

点击 NVIC Settings 选项卡，勾选 USART1 global interrupt（开启 USART1 全局中断）。

为什么开中断：中断的意思是”有数据来了再叫我”，单片机平时该干嘛干嘛，收到串口数据时自动放下手里的活去处理。这样收数据不会漏。

![[Pasted image 20261016172115.png]]

### **四、配置** **LED** **引脚**

和第一次一样，把 **PA4** 设置为 GPIO_Output（本次实验继续用 PA4 控制 LED）。

### **五、配置时钟树**

和第一次一样，HCLK 填 72MHz，回车自动完成配置。

### **六、生成工程**

项目管理设置与第一次完全相同（全英文路径、IDE 选 CMake），确认扩展库已下载后点击 GENERATE CODE。

## **第四步：实验一** **—— printf** **重定向，单片机给电脑发消息**

### **一、添加代码**

用 VS Code 打开工程，打开 Core/Src/main.c。

**①** **包含头文件**：在文件顶部找到 /* USER CODE BEGIN Includes */ 和 /* USER CODE END Includes */，在中间添加：

"#include **<stdio.h>**"

![[Pasted image 20261016172542.png]]

**②** **添加** **printf** **重定向函数**：找到 /* USER CODE BEGIN 4 */ 和 /* USER CODE END 4 */（在 main 函数后面），在中间添加：

int _write(int fd, char *ptr, int len)  
{  
  HAL_UART_Transmit(&huart1, (uint8_t *)ptr, len, HAL_MAX_DELAY);  
  **return** len;  
}

![[Pasted image 20261016173006.png]]
代码说明：

· C 语言的 printf 原本是给电脑屏幕用的，单片机没有屏幕。

· 这段代码告诉编译器：“printf 的内容，统统通过串口1发出去”。这叫做**重定向**。

· 以后直接用 printf 就行，里面的 _write 函数不用管它怎么实现的，原样复制即可。

**③** **在** **while(1)** **中发送消息**：和第一次一样，在 /* USER CODE END WHILE */ 和 /* USER CODE BEGIN 3 */ 之间写入：

printf("Hello STM32! count = %d\r\n", count);  
count++;  
HAL_Delay(1000);



**④** **定义计数变量**：在 /* USER CODE BEGIN PV */ 和 /* USER CODE END PV */ 之间添加：

uint16_t count = 0;

### **二、编译、烧录、接线**

1. 按第一次的方法编译并烧录（ST-Link 负责烧录）。

2. **接线（CH340** **部分）**：

|   |   |
|---|---|
|**CH340**|**连接到** **STM32**|
|TX|PA10（USART1_RX）|
|RX|PA9（USART1_TX）|
|GND|GND|

注意： - 又是交叉！CH340 的 TX 接单片机的 RX(PA10)，CH340 的 RX 接单片机的 TX(PA9)。 - **GND** **必须接**（双方要有共同的参考地），否则数据全是乱的。 - CH340 的 3.3V/5V 引脚**不接**！单片机由 ST-Link 供电，两个电源同时接可能烧板子。

3. 在 VS Code 底部打开 Serial Monitor 面板：选好 COM 口、波特率 115200 → 点 Start Monitoring。

### **三、验证结果**

Serial Monitor 接收区每隔 1 秒打印一行：

Hello STM32! count = 0  
Hello STM32! count = 1  
Hello STM32! count = 2  
...

提示：\r\n 是”回车换行”，让每条消息单独占一行。只写 \n 在某些工具里不会换行。

---

## **第五步：实验二** **——** **串口接收，电脑控制单片机**

这次反过来：在 Serial Monitor 里发送字符 1 开灯，发送 0 关灯。

### **一、添加代码**

**①** **定义接收变量**：在 /* USER CODE BEGIN PV */ 区域（刚才 count 变量旁边）添加：

uint8_t rx_data;   _// 存放收到的一个字节_

**②** **启动串口接收**：找到 /* USER CODE BEGIN 2 */ 和 /* USER CODE END 2 */（在 main 函数里、while(1) 之前），添加：

HAL_UART_Receive_IT(&huart1, &rx_data, 1);   _// 开启串口1中断接收，每次收1个字节_

代码说明：这行代码相当于告诉单片机：“竖起耳朵听着，串口1来数据了就叫我”。它只负责”开始听”，收到数据后做什么由下面的回调函数决定。

**③** **编写接收回调函数**：在 /* USER CODE BEGIN 4 */ 区域（刚才 _write 函数旁边）添加：

void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart)  
{  
  **if** (huart->Instance == USART1)          _// 确认是串口1收到的_  
  {  
    **if** (rx_data == '1')                   _// 收到字符 '1'_  
    {  
      HAL_GPIO_WritePin(GPIOA, GPIO_PIN_4, GPIO_PIN_SET);    _// PA4 高电平，LED 亮_  
      printf("received '1', LED ON\r\n");  
    }  
    **else** **if** (rx_data == '0')              _// 收到字符 '0'_  
    {  
      HAL_GPIO_WritePin(GPIOA, GPIO_PIN_4, GPIO_PIN_RESET);  _// PA4 低电平，LED 灭_  
      printf("received '0', LED OFF\r\n");  
    }  
    **else**  
    {  
      printf("unknown command: %c\r\n", rx_data);            _// 收到其他字符_  
    }  
  
    HAL_UART_Receive_IT(&huart1, &rx_data, 1);   _// 重新开启接收，等下一个字节_  
  }  
}

代码说明：

· HAL_UART_RxCpltCallback 是 HAL 库约定好的函数名，**每收到一个字节，HAL** **库会自动调用它一次**。函数名不能改，原样复制即可。

· 函数最后一行**必须重新开启接收**，否则只能收到一个字节，之后就”聋”了。

· 注意 '1' 是**字符1**，不是数字1。电脑通过串口发来的是字符。

关于 CubeMX 重新生成：上面的代码都写在 USER CODE 区域内，重新生成工程不会被覆盖。这一点和第一次完全相同。

### **二、编译烧录，验证结果**

1. 编译烧录（ST-Link），接线保持不变（CH340 还接着）。

2. 在 Serial Monitor 下方的发送输入框输入 1，按回车发送 → LED 亮，接收区显示 received '1', LED ON。

3. 发送 0 → LED 灭，显示 received '0', LED OFF。

4. 随便发个别的字符（比如 a）→ 显示 unknown command: a。

发送时直接输入 1 或 0 即可，**不要输入引号**。如果发送后多出一条 unknown command，检查输入框里是不是多带了空格或换行。

### **三、关于中文乱码（呼应第一次文档）**

如果想让单片机打印中文（如 printf("灯已打开\r\n");）：

· 用 **Serial** **Monitor（VS** **Code** **插件）**：它默认按 UTF-8 解码，和 VS Code 里源文件的编码一致，**中文一般可以直接正常显示**，这正是推荐用它的原因之一；

· 用**独立串口助手**：很多串口助手按 GBK 解码，会显示乱码，需要在软件设置里把编码改成 UTF-8（如果软件支持的话）。

保险起见，初学阶段打印内容还是建议用英文或拼音，把所有精力集中在串口本身。感兴趣的可以搜索”串口助手 中文乱码 UTF-8 GBK”了解更多，不属于本次必学内容。

---

## **第六步：硬件接线总表与检查清单**

### **一、本次实验全部接线**

|   |   |   |
|---|---|---|
|**模块**|**引脚**|**连接到** **STM32**|
|ST-Link（烧录用）|3.3V / SWCLK / SWDIO / GND|同第一次，不再重复|
|CH340（通信用）|TX|PA10（USART1_RX）|
|CH340（通信用）|RX|PA9（USART1_TX）|
|CH340（通信用）|GND|GND|
|LED|正极（长脚）→ GND，负极（短脚）→ PA4|同第一次|



### **二、接线检查清单**

☐ ST-Link 接线与第一次一致，烧录正常

☐ CH340 TX → PA10，CH340 RX → PA9（交叉连接）

☐ CH340 GND → STM32 GND（共地）

☐ CH340 的 3.3V/5V 引脚悬空不接

☐ LED 仍在 PA4 上

☐ 设备管理器中能同时看到 ST-Link 和 CH340 两个设备

---

## **第七步：常见错误排查清单**

|                              |                                                                    |
| ---------------------------- | ------------------------------------------------------------------ |
| **现象**                       | **排查顺序**                                                           |
| 设备管理器没有 COM 口                | ① 换 USB 口 ② 重新装 CH340 驱动 ③ 换线（排除纯充电线）                              |
| Serial Monitor 连不上 / 提示端口被占用 | ① COM 号选对了吗 ② 串口是不是被别的软件（如独立串口助手、FlyMcu）占用了，关掉它再点 Start Monitoring |
| 收到一堆乱码                       | ① 波特率两边都是 115200 吗 ② GND 接了吗 ③ 时钟树 HCLK 是 72MHz 吗                  |
| 完全没有数据                       | ① TX/RX 是不是接反了 ② Serial Monitor 的 Start Monitoring 点了吗 ③ 代码烧进去了吗   |
| 只能发不能收 / 只能收不能发              | TX 和 RX 其中一根接反或没接牢，重点查杜邦线是否松动                                      |
| 只能收到一次数据                     | 回调函数最后忘了写 HAL_UART_Receive_IT(...) 重新开启接收                          |
| 编译报错找不到 huart1               | 检查 CubeMX 里 USART1 是否真的开启了，重新 GENERATE CODE                        |