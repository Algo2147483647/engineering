# Address Space Abstraction

[TOC]

## Context



## Problem

物理地址直接暴露:  程序直接使用物理地址，导致进程间内存无法隔离（一个程序崩溃可能破坏整个系统）、内存碎片严重（外部碎片）、程序大小受物理内存限制、重定位困难。

需要地址空间隔离

- 消除外部碎片
- 简化内存分配与管理
- the problem need to resolve: 物理内存碎片, 进程间内存隔离与保护
- 程序规模受限于物理内存大小

随着多道程序设计和分时系统的普及，操作系统需要一种机制让**每个程序认为自己独占内存**，同时实现物理资源的共享与保护。这催生了地址空间抽象的概念。

核心矛盾：隔离 vs. 效率 vs. 灵活性 vs. 扩展性

### Evaluation



## Resolution

### Address virtualization: Logic Address

Address virtualization is the fundamental solution to the problem of exposing physical addresses directly, with the logical address serving as its key mechanism. **Logical address** provides a program with an independent, contiguous and protected virtual memory space, freeing it from concerns about the actual layout and limitations of the physical memory.

- **Isolation**: This isolation ensures that the address space of different processes do not interfere with each other, effectively preventing programmer errors or malicious actions from corrupting the memorial data of other processes or the kernel.
- **Contiguity**: The contiguity of logical addresses is an abstraction presented to the program; the actual physical memorial may be fragmented. The mapping table dynamically allocates physical page frames, mapping non-contiguous physical addresses to contiguous virtual addresses.

**The relationship between logical and physical address**: The mapping from a logical address to an actual physical memory address is a maintained by a mapping table. Various approaches implement this mapping table (such as page tables or segment tables), which lies at the core of memory virtualization system designs
$$
\text{logic address}  \xrightarrow{\text{mapping table}} \text{physical address}
$$


### History Evolution

静态重定位: 物理地址 = 逻辑地址 + 基址寄存器



Base and Bounds: 每个进程拥有一个基址寄存器（存储其物理内存起始地址）和一个界限寄存器（存储其最大长度）。CPU 将进程发出的地址加上基址得到物理地址，并检查是否越界。

- 内部碎片： 进程分配的内存块可能远大于其实际需求。
- 外部碎片： 进程加载/卸载后，物理内存中留下大量难以利用的小空隙。
- 难以共享： 共享内存（如代码库）困难。
- 地址空间不灵活： 堆、栈、代码等不同类型数据难以独立增长/保护。
- 要求连续内存分配

#### Segmentation

Segmentation:  将进程的地址空间划分为逻辑段（代码段、数据段、堆段、栈段等）。每个段有独立的基址和界限寄存器（段表）。逻辑地址 = `<段号, 段内偏移>`。硬件通过段号查段表找到基址和界限，加上偏移得到物理地址并检查界限。解决了基址-界限的单一线性地址空间问题，地址空间更符合程序逻辑结构。解决了基址-界限的单一线性地址空间问题，地址空间更符合程序逻辑结构。将程序逻辑结构（代码、数据、堆栈等）划分为独立段（Segment），每段在内存中连续存储。逻辑地址 = 段号（Segment Number） + 段内偏移（Offset）。

进程的地址空间：按照程序自身的逻辑关系划分为若干个段，每个段都有一个段名（在低级语言中，程序员使用段名来编程），每段从0开始编址。
内存分配规则：以段为单位进行分配，每个段在内存中占连续空间，但各段之间可以不相邻。

- **内存分配复杂：** 为变长段寻找合适空间是复杂的动态存储分配问题（首次适应、最佳适应等算法效果有限）。
- **地址转换开销相对大：** 每次访问需查段表（在内存中）。
- **外部碎片依然严重：** 段的大小可变，加载/卸载后物理内存中产生不规则空隙，需要复杂的碎片整理（紧缩），代价高昂且通常需停止所有进程。
- **通用性差：** 段的最大尺寸受限于物理内存大小（或段长寄存器位数）。

#### Segmentation with Paging

### Paging

Paging: **将物理内存和虚拟地址空间都划分为固定大小的块（页框和页）**，通过**页表**建立虚拟页到物理页框的映射。分页方案的成功在于它通过固定大小的页这一核心抽象，系统性地、优雅地解决了内存虚拟化的核心矛盾

消除外部碎片： 页是固定大小的单元。任何空闲的物理页框都可以分配给任何需要的虚拟页。内存分配器只需管理一个简单的空闲页框列表。外部碎片几乎不存在（只有最后不足一页的内部碎片）。彻底解决了变长分配导致的复杂外部碎片问题。

- 彻底解决外部碎片问题
- 支持虚拟内存扩展
- 成为现代OS标准方案

#### Multilevel Page Tables

**多级页表**:  单级页表需要覆盖整个虚拟地址空间（如 64 位下巨大无比），即使大部分区域未使用也会占用海量内存。将页表树形化（如二级、三级、四级页表）。只有实际被使用的顶级目录和中间目录需要分配内存。**稀疏地址空间下节省大量内存。**

### Translation Lookaside Buffer (TLB)

转换后备缓冲器 (TLB): 问题： 即使单级页表，每次内存访问都需查页表（在内存中），速度太慢。解决： 在 CPU 内引入专用的高速硬件缓存 (TLB)，存储最近使用的 VPN -> PPN 映射。TLB 命中时，地址转换无需访问内存中的页表，速度极快。大幅抵消了多级页表和多级页表带来的潜在访问延迟，使分页效率极高。固定大小的页是 TLB 高效工作的前提。