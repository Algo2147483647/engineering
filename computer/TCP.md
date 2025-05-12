# Transmission Control Protocol (TCP)

[TOC]

## Standard

<img src="assets/format,png-20230309230534096.png" alt="TCP 头格式" style="zoom: 28%;" />

* TCP segment header
    | Bit     | 意义                               |
    | ------- | ---------------------------------- |
    | 0-15    | Source port                        |
    | 16-31   | Destination port                   |
    | 32-63   | Sequence number                    |
    | 64-95   | Acknowledgment number (if ACK set) |
    | 96-99   | Data offset                        |
    | 100-102 | Reserved 000                       |
    | 103     | NS                                 |
    | 104     | CWR                                |
    | 105     | ECE                                |
    | 106     | URG                                |
    | 107     | ACK                                |
    | 108     | PSH                                |
    | 109     | RST                                |
    | 110     | ACK                                |
    | 111     | FIN                                |
    | 112-127 | Window Size                        |
    | 128-143 | Checksum                           |
    | 144-160 | Urgent pointer (if URG set)        |
    |         |                                    |

## Process

### 三次握手

<img src="assets/TCP三次握手.drawio.png" alt="TCP 三次握手" style="zoom:25%;" />

### 四次挥手

<img src="assets/format,png-20230309230614791.png" alt="客户端主动关闭连接 —— TCP 四次挥手" style="zoom:25%;" />

#### Time wait

## Feature

### 拥塞避免

  * 连接
    * 连接建立 —— Three-way Handshake

  * 流量控制 —— Sliding Window Protocol  
    - 滑动窗口，接收端所能提供的缓冲区大小, 发送方根据这个数据来计算自己最多能发送多长的数据. 
    - 流量控制，主要是接收方传递信息给发送方，使其不要发送数据太快，是一种端到端的控制。主要的方式就是返回的ACK中会包含自己的接收窗口的大小，并且利用大小来控制发送方的数据发送

  * 拥塞控制  
    拥塞窗口 cwnd (congestion window)  
    当cwnd < ssthresh时，使用慢开始算法。  
    当cwnd > ssthresh时，改用拥塞避免算法。  
    当cwnd = ssthresh时，慢开始与拥塞避免算法任意。  

    * 慢开始  
      不要一开始就发送大量的数据，先探测一下网络的拥塞程度，也就是说由小到大逐渐增加拥塞窗口的大小。

    * 拥塞避免  
      - 加法增大
      - 乘法减小

    * 快重传, 快恢复  

### 流量控制 —— Sliding Window Protocol 

## Application

使用TCP的协议：

* FTP
* Telnet
* SMTP
* POP3
* HTTP

## Problem

### SYN泛洪攻击  

### TCP粘包  

### TCP心跳包