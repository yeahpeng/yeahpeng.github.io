---
title: 经典论文阅读-Raft
date: 2019-06-14 22:36:34
categories: paper
tags:
- Raft
- 一致性算法
---

Raft 是一种管理日志的一致性算法, 为非拜占庭问题提出解决方案.它提供了和 Paxos 算法相同的功能和性能，但是它的算法结构和 Paxos 不同，使得 Raft 算法更加容易理解并且更容易构建实际的系统.
## 架构

## 复制状态机

## 选举和日志复制
**状态**：

|状态|所有服务器上持久存在的|
|-------|------|
|currentTerm | 服务器最后一次知道的任期号（初始化为 0，持续递增）|
|votedFor | 在当前获得选票的候选人的 Id|
| log[] | 日志条目集；每一个条目包含一个用户状态机执行的指令，和收到时的任期号 |

|状态|所有服务器上经常变的|
|-------|------|
| commitIndex| 已知的最大的已经被提交的日志条目的索引值|
| lastApplied| 最后被应用到状态机的日志条目索引值（初始化为 0，持续递增）|

| 状态 | 在领导人里经常改变的 （选举后重新初始化）|
|----|--------|
| nextIndex[] | 对于每一个服务器，需要发送给他的下一个日志条目的索引值（初始化为领导人最后索引值加一）|
| matchIndex[] | 对于每一个服务器，已经复制给他的日志的最高索引值|


**附加日志 RPC**：

由领导人负责调用来复制日志指令；也会用作heartbeat

| 参数 | 解释 |
|----|----|
|term| 领导人的任期号|
|leaderId| 领导人的 Id，以便于跟随者重定向请求|
|prevLogIndex|新的日志条目紧随之前的索引值|
|prevLogTerm|prevLogIndex 条目的任期号|
|entries[]|准备存储的日志条目（表示心跳时为空；一次性发送多个是为了提高效率）|
|leaderCommit|领导人已经提交的日志的索引值|

| 返回值| 解释|
|---|---|
|term|当前的任期号，用于领导人去更新自己|
|success|跟随者包含了匹配上 prevLogIndex 和 prevLogTerm 的日志时为真|

接收者实现：

1. 如果 `term < currentTerm` 就返回 false 
2. 如果日志在 prevLogIndex 位置处的日志条目的任期号和 prevLogTerm 不匹配，则返回 false 
3. 如果已经存在的日志条目和新的产生冲突（索引值相同但是任期号不同），删除这一条和之后所有的 
4. 附加日志中尚未存在的任何新条目
5. 如果 `leaderCommit > commitIndex`，令 commitIndex 等于 leaderCommit 和 新日志条目索引值中较小的一个

**请求投票 RPC**：

由候选人负责调用用来征集选票

| 参数 | 解释|
|---|---|
|term| 候选人的任期号|
|candidateId| 请求选票的候选人的 Id |
|lastLogIndex| 候选人的最后日志条目的索引值|
|lastLogTerm| 候选人最后日志条目的任期号|

| 返回值| 解释|
|---|---|
|term| 当前任期号，以便于候选人去更新自己的任期号|
|voteGranted| 候选人赢得了此张选票时为真|

接收者实现：

1. 如果`term < currentTerm`返回 false
2. 如果 votedFor 为空或者为 candidateId，并且候选人的日志至少和自己一样新，那么就投票给他

**所有服务器需遵守的规则**：

所有服务器：

* 如果`commitIndex > lastApplied`，那么就 lastApplied 加一，并把`log[lastApplied]`应用到状态机中
* 如果接收到的 RPC 请求或响应中，任期号`T > currentTerm`，那么就令 currentTerm 等于 T，并切换状态为跟随者

跟随者：

* 响应来自候选人和领导者的请求
* 如果在超过选举超时时间的情况之前都没有收到领导人的心跳，或者是候选人请求投票的，就自己变成候选人

候选人：

* 在转变成候选人后就立即开始选举过程
	* 自增当前的任期号（currentTerm）
	* 给自己投票
	* 重置选举超时计时器
	* 发送请求投票的 RPC 给其他所有服务器
* 如果接收到大多数服务器的选票，那么就变成领导人
* 如果接收到来自新的领导人的附加日志 RPC，转变成跟随者
* 如果选举过程超时，再次发起一轮选举

领导人：

* 一旦成为领导人：发送空的附加日志 RPC（心跳）给其他所有的服务器；在一定的空余时间之后不停的重复发送，以阻止跟随者超时
*  如果接收到来自客户端的请求：附加条目到本地日志中，在条目被应用到状态机后响应客户端
*  如果对于一个跟随者，最后日志条目的索引值大于等于 nextIndex，那么：发送从 nextIndex 开始的所有日志条目：
	* 如果成功：更新相应跟随者的 nextIndex 和 matchIndex
	* 如果因为日志不一致而失败，减少 nextIndex 重试
* 如果存在一个满足`N > commitIndex`的 N，并且大多数的`matchIndex[i] ≥ N`成立，并且`log[N].term == currentTerm`成立，那么令 commitIndex 等于这个 N 

## 注意点
**之前任期内未提交的日志需要和新日志一起广播**



## 参考

1. [英文 paper](https://ramcloud.atlassian.net/wiki/download/attachments/6586375/raft.pdf)
2. [中文 paper](https://github.com/maemual/raft-zh_cn/blob/master/raft-zh_cn.md)
3. [动画](http://thesecretlivesofdata.com/raft/)
4. [6.824 Lab 2](http://nil.csail.mit.edu/6.824/2018/labs/lab-raft.html)
5. [Braft](https://github.com/brpc/braft)
6. [sofa-jraft](https://github.com/sofastack/sofa-jraft) 
