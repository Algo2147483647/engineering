# Address Space Abstraction

[TOC]

## Context

In a multi-processes environment, the entire physical memory is shared by multiple processes.

## Problem

**Bare Physical Addressing**: Direct use of physical addresses by programs leads to severe issues.

- **Inter-process memory interference**. Errors or malicious actions cannot be contained, potentially damaging other processes or kernel memory. For example, a single program crash may corrupt the entire system; processes may erroneously assess or corrupt others processes' memory. 

- **External memory fragmentation**. External memory fragmentation refers to the wasted "gaps" scattered between allocated memory blocks, which are free memory but too fragmented to be used. Frequent allocation and of physical memory create numerous small, unusable fragments, drastically lowering memory utilization, and large blocks of memory can no longer be allocated.
- **Inefficient memory management**. Due to physical memory is divided into countless scattered blocks, the system cannot coordinate memory space holistically. In this chaotic memory space, programs cannot accurately locate their own memory regions.
- **Relocation Difficulty**. Programs bound to physical addresses cannot be dynamically moved in memory. If the operating system needs to relocate a program (e.g., to defragment memory or reallocate resources), or physical address is referenced by program become invalid, causing crashes or data corruption.

- **Program scale limited by physical memory capacity**. Both individual program size and the total memory footprint of concurrently running processes are constrained by available physical RAM. Programs cannot exceed physical memory limits, and the system cannot run workloads whose combined memory demands surpass installed RAM—even if portions of memory are idle.



一个现代内存管理系统需在以下相互制约的目标之间寻求平衡：隔离 vs. 效率 vs. 灵活性 vs. 扩展性

**Isolation**: 确保每个进程（或虚拟机）拥有自己独立的、受保护的地址空间。一个进程的错误或恶意行为不能访问或破坏其他进程或内核的内存。

**Efficiency**: 最大化内存访问速度，最小化地址转换带来的开销（时间、空间）。目标是让虚拟内存访问的速度尽可能接近直接访问物理内存的速度。

**Flexibility**: 内存管理系统能够适应多样化的应用需求、硬件架构和内存使用模式。例如：支持不同大小的内存区域（段、页、大页）、高效的内存共享（进程间、内核与用户态）、稀疏地址空间、动态内存分配/回收策略、与新型硬件（如NVM、异构内存）的集成等。

**Scalability**: 内存管理系统能够有效地支持不断增长的系统资源规模，包括：物理内存容量、CPU核心数量、并发进程/线程数量、地址空间大小等。性能（吞吐量、延迟）不应随规模增长而显著劣化。

## Resolution

### Address virtualization: Logic Address

**Decoupling of address space and physical storage**: In a multi-processes environment the operating system requires a mechanism that allows each program to believe it has a exclusive use of memory, while simultaneously enabling the sharing and protection a physical resources. Actual data may be scattered across physical memory and disk storage. This necessity gave rise to the concept of address space abstraction (address virtualization) which the couples address from physical storage. Address virtualization is the fundamental solution to the problem of exposing physical addresses directly, with the logical address serving as its key mechanism. 

**Logical address** provides a program with an independent, contiguous and protected virtual memory space, freeing it from concerns about the actual layout and limitations of the physical memory.

- **Isolation**: This isolation ensures that the address space of different processes do not interfere with each other, effectively preventing programmer errors or malicious actions from corrupting the memorial data of other processes or the kernel.
- **Contiguity**: The contiguity of logical addresses is an abstraction presented to the program; the actual physical memorial may be fragmented. The mapping table dynamically allocates physical page frames, mapping non-contiguous physical addresses to contiguous virtual addresses.

**The relationship between logical and physical address**: The mapping from a logical address to an actual physical memory address is a maintained by a mapping table. Various approaches implement this mapping table (such as page tables or segment tables), which lies at the core of memory virtualization system designs
$$
\text{logic address}  \xrightarrow{\text{mapping table}} \text{physical address}
$$

### History Evolution

那么最简单的方式就是虚拟内存是一块连续的地址然后映射到实际内存也是一个连续的区段这些区段之间是之间的间隔, 那么我们需要考虑的内容是内存的扩展性当虚拟内存需要添加的时候怎么样在实际内存中实现这个内存的扩展这是我们需要考虑的关键之一

第二点是资源利用的效率能够更高效地利用所有的物理内存而不产生碎片和无法利用的空间

第三点则是能够保证不同进程之间内存的隔离性和安全性





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