title: Redis 快速开始
date: 2019-07-28 22:39:42
categories: work
tags: redis
---

## Install
```
wget http://download.redis.io/releases/redis-3.0.7.tar.gz
tar xzf redis-3.0.7.tar.gz 
ln -s redis-3.0.7 redis
cd redis # for update
make
make install
```

## Commands
| Command | 说明 |
|---------|------|
|redis-server|启动 Redis server|
|redis-cli|Redis 命令行客户端
|redis-benchmark|Redis  基准测试工具|
|redis-check-aof|Redis AFO 持久化文件检测和修复工具|
|redis-check-dump|Redis RDB 持久化文件检测和修复工具|
|redis-sentinel|启动 Redis Sentinel|
