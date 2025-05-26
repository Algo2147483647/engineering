# Parallel Computing

[TOC]

CAP定理是分布式系统中的一个重要理论，指出在一个分布式数据存储中，最多只能同时满足以下三项中的两项：
一致性（Consistency）：所有节点在同一时间看到的数据是一致的。
可用性（Availability）：每个请求都会在合理的时间内得到响应，无论响应的结果是否最新。
分区容忍性（Partition Tolerance）：系统在网络分区的情况下仍然能够继续操作。

## Synchronous &  Asynchronous

Synchronous: Tasks are performed one after the other, in a sequence. Each task must be completed before the next one starts.

Asynchronous: Tasks are performed independently of each other. A task can start before the previous one is completed.

## Communication between processes

- Information channel between processes
  - Pipes: Unidirectional data channels used for communication between processes on the same machine.
  - Channel
- Communication based on a shared storage between processes
  - Shared Memory: Multiple processes can access a common memory space. Efficient but requires synchronization.
  - Redis
  - Database
- Communication based on Computer Network
  - Sockets: Enable communication between processes over a network, supporting both TCP (reliable) and UDP (unreliable) protocols.
  - Remote Procedure Calls (RPC): Allow a program to execute a procedure on another address space (commonly on a remote server).
  - HTTP
- Third-party communication services
  - Message Queues: Allow processes to exchange messages in a queue format, supporting both asynchronous and synchronous communication.

## Shared resources

### Lock mechanism

- Mutex (Mutual Exclusion): A lock that ensures only one thread can access a resource at a time. 
- Spinlock: A lock where a thread repeatedly checks for a lock to become available, "spinning" in a loop.
- Read-Write Lock: A lock that allows multiple threads to read a resource concurrently, but only one thread to write at a time.
- Semaphore: A signaling mechanism that can control access to a resource by multiple threads.
- Condition Variable: A synchronization mechanism that allows threads to wait for certain conditions to be met.
- reentrant lock

### Deadlock

Deadlock is a situation in parallel computing where two or more processes are unable to proceed because each is waiting for the other to release a resource. A deadlock situation on a resource can arise only if all of the following conditions occur simultaneously in a system.

- Mutual exclusion: multiple resources are not shareable; only one process at a time may use each resource.

- Hold and wait or resource holding: a process is currently holding at least one resource and requesting additional resources which are being held by other processes.
- No preemption: a resource can be released only voluntarily by the process holding it.
- Circular wait: each process must be waiting for a resource which is being held by another process, which in turn is waiting for the first process to release the resource.



## Task Assignment: Load Balancing

### Amdahl’s Law & Gustafson’s Law

## Concurrency

### Concurrency models

#### Context

Concurrency Models 是用于管理和协调多个计算任务同时执行的编程范式或架构。

- 核心矛盾：多执行流（逻辑或物理）与共享资源之间的动态交互

实体构成:

- 资源: 共享资源：内存页、文件句柄、网络套接字; 独占资源：CPU核心、GPU流处理器、FPGA逻辑单元;
- 通信
- 计算

约束

- 安全性（Safety）：无竞态条件、无死锁
- 活性（Liveness）：保证进展性
- 公平性（Fairness）：资源分配合理性

**一致性模型**：

- 强一致性：线性化（Linearizability）
- 弱一致性：最终一致性（Eventual Consistency）

#### Problem

- 如何处理多个任务间的数据共享？
- 如何分配计算资源（CPU时间、线程）？
- 任务间如何交换信息？
- 如何处理任务失败？

#### Solution

- shared memory

  - 需要锁机制

  - Java synchronized: 确保同一时间只有一个线程执行同步代码块，这明显是共享内存模型中解决竞争条件的方法. 共享堆内存，synchronized锁住的是对象监视器，从而保护共享数据

  - C++的std::thread默认共享地址空间，显式使用互斥量来同步。Python的threading模块线程共享内存，但由于GIL，实际上在解释器级别只有一个线程执行字节码，不过对于IO操作，GIL会被释放，所以线程间还是需要同步共享数据。

- message passing: Actor

  - 通过独立的“Actor”作为并发单元，每个 Actor 拥有私有状态，仅通过异步消息传递通信，避免共享内存。

  - Actor模型通过消息传递，不共享内存

  - 通信方式:  异步消息传递; 同步机制: 消息队列、邮箱

- message passing: Communication Sequential Processes

  - 通过“通道”（Channel）在不同进程/协程间传递消息，发送方和接收方必须同步（类似于管道）。

  - 同步机制:  通道同步（发送/接收）


  Don't communicate by sharing memory; instead, share memory by communicating.

- Event loop

  - 单线程或少量线程通过事件循环（Event Loop）处理异步事件（如 I/O 完成、用户输入），使用回调（Callbacks）或 Promise 响应事件。
  - 回调地狱（Callback Hell）

- Data Parallelism

  - 将数据分割为多个块，并行处理相同操作（如 MapReduce）
