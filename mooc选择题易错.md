# mooc选择题易错整理

![alt text](/assets/mooc0.png)

---

![alt text](/assets/mooc1.png)
Celeron是Inter的处理器型号

---
![alt text](/assets/mooc2.png)
中断是种行为或操作而不是资源

---
![alt text](/assets/mooc3.png)
管态指内核态, 目态指用户态

---
![alt text](/assets/mooc4.png)
父进程创建的子进程是彼此独立的

---
![alt text](/assets/mooc5.png)
混合式线程是内核级线程和用户级线程的组合

---
![alt text](/assets/mooc6.png)
可动态链接的就视为动态重定位, 可静态链接的就视为只能静态重定位

---
![alt text](/assets/mooc7.png)
重新执行出现中断的那一条指令

---
![alt text](/assets/mooc8.png)
首先是由硬件MMU检测到缺页中断, 操作系统处理该中断时需要从硬盘调入页进入内存, 磁盘是I/O设备

---
![alt text](/assets/mooc9.png)
虚拟设备技术: 使用一类物理设备模拟另一类物理设备的技术

---
![alt text](/assets/mooc10.png)
- 单缓冲：只有一个缓冲区，CPU 和 I/O 设备必须轮流使用，效率低。

- 双缓冲：两个缓冲区，允许 CPU 和 I/O 设备部分重叠工作，但并发能力有限。

- 循环缓冲：多个缓冲区组成环形队列，适合单一数据流（如一个生产者-消费者对），但多进程共享时可能存在问题。

- 缓冲池：一组动态管理的缓冲区，可被多个进程共享，支持高并发 I/O 操作。

---
![alt text](/assets/mooc11.png)
直接存取也称为随机存取

---
![alt text](/assets/mooc12.png)
是用磁盘模拟的虚拟设备, 而不是磁盘这一个共享设备

---
![alt text](/assets/mooc13.png)

---
![alt text](/assets/mooc14.png)
通道程序不是在CPU上运行的, 是在通道(I/O处理器)上运行的, 使用的是通道指令