---
layout:         post
title:          Fault Injection Survey, 2016-01-21
category:       sdn
description:    NFV, Fault injection
---


故障注入方法按照事先选定的故障模型,采用某种策略人为地将故障引入目标系统中,通过观察和分析系统在被注入故障情况下的行为,可以为试验者提供所需的定性/定量的评价结果。Fault Injection按照种类分，可以分为五种：hardware, software, simulation, emulation and hybrid。一般来说，我们所指的是software层面的Fault Injection，即SWIFI(software implemented fault injection )。SWIFI主要分为编译时注入和运行时注入。编译时注入需要通过修改源代码，并重新编译。这样的方式能够实现准确的故障注入，但是需要修改源代码重新发布，故障注入实验执行效率低。运行时注入是程序运行时破坏程序状态来实现，这种方式不需要改变原系统环境，效率高。但是缺点是故障注入精度较低，故障模拟准确度不高。


##1. Fault injection of Hardware
硬件故障往往表现为I/O传输的中断（网络瞬间丢包、网卡失效）和CPU与内存系统的不稳定行为（VM、物理节点中的内存、寄存器和缓冲器的指令和数据的崩溃，资源泄露等）。这些硬件故障可以通过加载不同Linux内核模块的方式或者相应的工具指令来实现，如注入I/O 损耗、中断和延时、注入错误代码和数据、强制终止VM和其主机、引入CPU和内存hogs（故意消耗CPU和内存资源）。硬件故障主要为服务器关机、CPU工作不正常、内存故障、网卡失效或性能下降、磁盘故障等。

![NFV arno overview]({{ site.url }}/image/2016-01-21/1fault.png)

* CPU故障：CPU 故障常见为部分CPU工作不正常。在目前的Linux内核中支持CPU热插拔，能够实现CPU核动态的开关。CPU核的损坏可通过修改/sys/devices/system/cpu/cpuX/online的值，来更新CPU状态以关闭部分CPU核，从而模拟部分的损坏。

* 内存故障：内存故障有内存物理空间减少、内存泄露等。内存物理空间的减少可使用ulimit的shell指令来实现，它能够设置内存资源限制、数据段最大值、锁定进程最大值等。
* 网卡故障：网卡故障通常表现为网卡失效、宽带降低、传输延迟、数据包丢包等。在Linux中提供了Netem这样的网络模拟功能模块，能够模拟出复杂的互联网传输性能。TC（traffic tool流量控制）是用于Linux内核的流量控制，主要是通过在输出端口处建立一个队列来实现流量控制。利用TC和Netem配合使用，可设置网卡传输延迟、丢包率、数据包重复、数据包损坏概率、包乱序等故障。

![NFV architecture]({{ site.url }}/image/2016-01-21/2fault.png)

* 磁盘故障：磁盘故障主要由磁盘损坏以及磁盘读写速度变慢。磁盘故障的模拟可通过使用Linux内核模块或者fsdisk、libfiu、Device-Mapper等工具实现。Linux内核自带有故障注入的工具，能够向内存分配函数、页分配和I/O操作。fsdisk是Linux的一个驱动，绑定在磁盘上，为故障注入提供了接入路径，能够设定随机的写入错误、只读、读写延迟等功能。libfiu是针对故障注入的C语言库，能够在不修改代码的情况下，在运行中模拟POSIX API的故障。


##2. Fault injection of Hypervisor
###2.1. 利用修改内核源代码的方式
如利用GDB在KVM和Xen Hypervisor中插入中断点来触发故障注入，通过修改指令、寄存器等实现故障的发生。
* 软件错误：这里主要是指发生在内存、寄存器，或者执行和控制的CPU单元中的以单字节或多字节置反为主的软件或瞬间错误，它主要是由于放射性颗粒、电磁干扰以及电压不稳定引起的。通过在目标程序中设置中断点（如qemu-kvm程序），当中断点触发时，注入程序首先移除所有中断点，然后修改目标对象。

* 客户系统不当行为：这里是指guest系统的不当行为可能会导致系统状态崩溃或CPU异常。实现方式：在guest OS中把故障注入作为一个内核模块安装进去，来实现1)随机的打乱一个进程的状态：修改数据、栈、程序代码或寄存器中的一个或多个字节；2)引发CPU异常：从机器检测异常中获取异常，如无效指令。

* 性能故障：性能故障是指由I/O接入堵塞、CPU耗尽、或者事件中断引起的一个或多个线程延迟。实现方式：首先设置中断点地址、线程数以及延迟时间。当中断点在一个线程中被触发时，移除该线程中的中断点，其他线程继续运行。当有中断点触发的线程达到预先设定的时候，清除所有中断点，并注入延迟代码，使线程休眠。
###2.2. KMV API
2.1中使用编译KVM源代码的方式实现，需要修改源代码进行故障注入，耗时耗力。KVM所有API都以ioctl()系统调用的形式出现，所以可将KVM内核故障抽象的KVM的API上，使用KVM的系统调用产生的错误来反映KVM行为上的故障。例如KMV内核创建VM后，会调用kvm_arch_create函数来创建一些结构相关信息，主要包括内核(kvm_create_pit)、外设(kvm_init_coalsced_mimo)、其他对/dev/kvm(kvm_arch_dev_ioctl)的操作等。因而可借助Source Insight等工具可追踪到指定ioctl()在内核源代码中的位置，并利用内核模块odc.ko导出二进制代码片段，利用distorm3对其反编译，然后做出相应的故障修改后再导入。在程序运行时利用脚本文件执行故障的触发。

##3. Fault Injection in OpenStack
目前还没有一种比较全面的，专门为OpenStack设计的OpenStack的Fault Injection工具。很多文章都是选择一些常见的Fault，或者利用一些简单的场景制造Fault，例如：delete VM image while saving snapshot, external dependency crash or delete a network with existing VM。
目前有一些跟云平台，或者OpenStack相关的工具，但是这些工具都不能直接使用。
* Tempest：使用OpenStack的API，测试OpenStack相关的功能是否正常
* Chaos Monkey：分布式系统中，随机关闭其中的一些节点，测试系统功能是否正常
* Gigan：用于制造low-level的Fault：bit flips in the CPU registers
* ORCHESTRA：script-driven probing and fault injection
* ComFIRM：similar to ORCHESTRA
* Tools for Simulating Disk Failures

这个是Fault Injection的通用框架，简单来说就是注入故障然后测试。

![NFV architecture]({{ site.url }}/image/2016-01-21/FI-framework.png)

OpenStack平台提供Infrastructure as a Service，直观上提供的服务主要有两种：提供存储，提供VM
* 存储（以下以SWIFT为例）

![NFV architecture]({{ site.url }}/image/2016-01-21/memory.png)

* VM

![NFV architecture]({{ site.url }}/image/2016-01-21/vm.png)

当然还有其他形式的Fault Injection，例如模块之间的通信，以及配置文件。
* Communication
  o可以使用ORCHESTRA和ComFIRM，修改OpenStack不同模块之间的通信（AMQP）
* Configuration
  o删掉配置文件，重启服务
一张OpenStack的Architecture，里面的每一个小模块都可以做Fault Injection，比如停掉这些服务。

![NFV architecture]({{ site.url }}/image/2016-01-21/Openstack-architecture.png)

对于以上几种错误，常见的模拟方式是利用nosetests框架，写JSON配置文件，Python脚本。
这是一个SWIFT存储的一个配置文件，一个proxy，两个storage。

![NFV architecture]({{ site.url }}/image/2016-01-21/swift.png)


这是一个简单的Fault Injection脚本

![NFV architecture]({{ site.url }}/image/2016-01-21/FI-shell.png)
