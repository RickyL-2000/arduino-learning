# 函数

## 函数库

### arduino I/O 函数

####  pinMode() 函数

arduino 引脚默认配置为输入，不需要 pinMode() 显式声明为输入

pinMode() 函数用于讲特定引脚配置为输入或输出。可以使用 INPUT_PULLUP 模式启用内部上拉电阻。此外，INPUT 模式显示禁止内部上拉。

**PROBLEM**：什么是上拉电阻和下拉电阻？？？

example:

    void setup() {
        pinMode(pin, mode);
    }

#### digitalWrite() 函数

digitalWrite()函数用于向数字引脚写入HIGH或LOW值。如果该引脚已通过pinMode()配置为OUTPUT，则其电压将被设置为相应的值：HIGH为5V（或3.3V在3.3V板上），LOW为0V（接地）。如果引脚配置为INPUT，则digitalWrite()将启用（HIGH）或禁止（LOW）输入引脚的内部上拉。建议将pinMode()设置为INPUT_PULLUP，以启用 内部上拉电阻。

example:

    digitalWrite(pin, value);

* pin - 你希望设置模式的引脚的编号

* value - HIGH 或 LOW


#### analogRead()

Arduino能够检测是否有一个电压施加到其引脚，并通过 **digitalRead()** 函数报告。开/关传感器（检测物体的存在）和模拟传感器之间存在一个差异，模拟传感器的值连续变化。为了读取这种类型的传感器，我们需要一个不同类型的引脚。

example:

    analogRead(pin);

* pin - 要读取的模拟输入引脚的编号 (大多数电路板上为0至5，Mini和Nano上为0至7，Mega上为0至15)


### 字符函数

所有数据都以字符形式输入计算机，包括字母，数字和各种特殊符号。

字符处理库包括几个函数，执行有用的测试和字符数据的操作。每个函数接收一个字符，表示为int或EOF作为参数。字符通常作为整数操作。

记住，EOF通常具有值-1，而一些硬件架构不允许负值存储在char变量中。因此，字符处理函数将字符作为整数来操作。


### 数学库

Arduino数学库(math.h)包含了许多用于操作浮点数的有用的数学函数。

#### 