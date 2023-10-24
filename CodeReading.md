# miniBase 源码阅读笔记

## KV 写入链路：
    1. 写逻辑简单，顺序写，吞吐率高
## KV 查询链路
    1. 查询逻辑复杂，重点是 Iter 高效实现，如何快速定位 Key 位置

## 核心数据结构
    1. KeyValue:
    2. DiskFile:
    3. MemStore： 主要存储对象是 MemStore ，通过内存councurrent_list 存储，算法动作是Flush 操作
    4. DiskStore：  主要存储对象是 DiskFile 文件结构 ,算法动作是Compact
    5. MStore:  继承实现 MiniBase, 启动compact 守护线程


## 核心算法实现：
       1. 查询/Compact/Flush 均需要使用的核心逻辑
            a. Iter<KeyValue>: 在MiniBase 中定义此接口，后续封装实现haseNext,next 等来完成查询数据
            b. MultiIter： 整个KV查询逻辑的关键，scan 查询直接调用，其中it.seekTo() 指定查询起点  
            c. SeekIter: 直接定位到指定位置，在MStore 中定义此接口， 具体实现是在MemStore 和 DiskFile 中实现
            d. InternalIterator: 在DiskFile中实现，主要是根据起始点定位key TODO: 待确认核心逻辑



# TODO-List
    核心动作
    写入链路
    1. flush：每次写入都要进行判断： 判断内存文件大小，当超过阀值，就进行Flush
    2. compact：
    查询链路
    1. 核心看Scan 过程 <-----> 对应HBase 实际实现
    2. BloomFilter
    3. Seek HFile【多版本影响查询CPU】