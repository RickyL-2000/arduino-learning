# arduino 进阶

## 脉宽调制

* On-Time（导通时间） - 时间信号的持续时间较长。

* Off-Time（关断时间） - 时间信号的持续时间较短。

* Period（周期） - 表示为PWM信号的导通时间和关断时间的总和。

* Duty Cycle（占空比） - 它表示为在PWM信号周期内保持导通的时间信号的百分比。


### analogWrite() 函数

**analogWrite()** 函数将模拟值（PWM波）写入引脚。它可用于以不同的亮度点亮LED或以各种速度驱动电机。在调用 analogWrite() 函数之后，引脚将产生指定占空比的稳定方波，直到下一次调用 analogWrite() 或在相同引脚上调用 digitalRead() 或 digitalWrite()。大多数引脚上的PWM信号频率约为490 Hz。在Uno和类似的板上，引脚5和6的频率约为980Hz。Leonardo 上的引脚3和11也以980Hz运行。

在大多数Arduino板上（ATmega168或ATmega328），此功能在引脚3，5，6，9，10和11上工作。在Arduino Mega上，它在引脚2-13和44-46上工作。旧的Arduino ATmega8板仅支持引脚9，10和11上的 analogWrite()。

> example:

    analogWrite (pin, value);
    
* value - 占空比：0(始终导通)到255(始终关断)之间


## arduino 随机数

我们有两个函数：

* randomSeed(seed)

* random()

### randomSeed(seed)

randomSeed(seed)函数重置Arduino的伪随机数生成器。虽然random()返回的数字的分布本质上是随机的，但是顺序是可预测的。你应该将发生器重置为某个随机值。如果你有一个未连接的模拟引脚，它可能会从周围环境中拾取随机噪音。这些可能是无线电波，宇宙射线，手机的电磁干扰，荧光灯等。

> example:

    randomSeed(analogRead(5)); // randomize using noise from analog pin5

### random()

> example:

    long random(max)  // it generate random numbers from 0 to max   [0, max)
    long random(min, max) // it generate random numbers from min to max


## arduino 中断 (????)

中断的重要特征：

1. 中断可以来自各种来源。在这种情况下，我们使用的是由数字引脚上的状态改变触发的硬件中断。
2. 大多数Arduino设计有两个硬件中断（称为“interrupt0”和“interrupt1”）分别硬连接到数字I/O引脚2和3。
3. Arduino Mega有六个硬件中断，包括引脚21，20，19和18上的附加中断（“interrupt2”到“interrupt5”）。
4. 你可以使用称为 **“中断服务程序”（Interrupt Service Routine，通常称为ISR)** 的特殊函数来定义程序。
5. 可以定义该程序并指定上升沿，下降沿或两者的条件。在这些特定条件下，将处理中断。
6. 每次在输入引脚上发生事件时，都可以自动执行该函数。


中断类型：有两种类型的中断

* 硬件中断 - 它们响应外部事件而发生，例如外部中断引脚变为高电平或低电平。

* 软件中断 - 它们响应于在软件中发送的指令而发生。“Arduino语言”支持的唯一类型的中断是**attachInterrupt()**函数。


中断在Arduino程序中非常有用，因为它有助于解决时序问题。中断的良好应用是读取旋转编码器或观察用户输入。一般情况下，ISR应尽可能短且快。如果你的草图使用多个ISR，则一次只能运行一个。其他中断将在当前完成之后执行，其顺序取决于它们的优先级。

通常，全局变量用于在ISR和主程序之间传递数据。为了确保在ISR和主程序之间共享的变量正确更新，请将它们声明为volatile。


> example:

    attachInterrupt(digitalPinToInterrupt(pin), ISR, mode); // recommended for arduino board
    attachInterrupt(pin, ISR, mode);  // recommended Arduino Due, Zero only
    
* pin: the pin number
* ISR: the ISR to call when the interrupt occurs;
* * this function must take no parameters and return nothing.
* * this function is sometimes referred to an interrupt service routine.
* mode: defines when the interrupt should be triggered.


以下三个常量被预定义为有效值：

* LOW ：在引脚为低电平时触发中断。

* CHANGE ：在引脚更改值时触发中断。

* FALLING ：当引脚从高电平变为低电平时触发中断。  


example:

    int pin = 2; //define interrupt pin to 2
    volatile int state = LOW; // To make sure variables shared between an ISR
    //the main program are updated correctly,declare them as volatile.

    void setup() {
    pinMode(13, OUTPUT); //set pin 13 as output
    attachInterrupt(digitalPinToInterrupt(pin), blink, CHANGE);
    //interrupt at pin 2 blink ISR when pin to change the value
    } 
    void loop() { 
    digitalWrite(13, state); //pin 13 equal the state value
    } 

    void blink() { 
    //ISR function
    state = !state; //toggle the state when the interrupt occurs
    }


## 通信

已经定义了数百个通信协议来实现这种数据交换。每个协议可以分为两类：并行或串行。

### 并行通信

通过输入/输出端口在Arduino和外设之间进行并行连接是短距离（最多几米）的理想解决方案。然而，在其他情况下，当需要在两个设备之间建立较长距离的通信时，不可能使用并行连接。并行接口同时传输多个位。它们通常需要数据总线 - 通过八条，十六条或更多的线路进行传输。数据以1和0的巨大波形传输。

### 串行通信模块

今天，大多数Arduino板都是用几种不同的串行通信系统作为标准设备。

使用哪个系统取决于以下因素:

1. 微控制器有多少个器件与数据交换？
2. 数据交换的速度有多快？
3. 这些设备之间的距离是多少？
4. 是否需要同时发送和接收数据？


**串行通信类型**

同步通信：同步的设备使用相同的时钟，它们的时序彼此同步

异步通信：异步的设备具有各自的时钟，并由前一状态的输出触发

很容易找出设备是否同步。如果给所有连接的设备提供相同的时钟，则它们是同步的。如果没有时钟线，它是异步的。例如，UART（通用异步收发器）模块是异步的。


异步串行协议有一些内置的规则。这些规则只是有助于确保可靠且无误的数据传输的机制。这些避免外部时钟信号的机制是：

* Synchronization bits 同步位
* Data bits 数据位
* Parity bits 奇偶校验位
* Baud rate 波特率

#### 同步位

