---
data: 2026-05-18
---
[基础篇-03.初识Redis-认识Redis_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cr4y1671t?vd_source=43c9de78f6e5f2b05790188e274ad943&spm_id_from=333.788.videopod.episodes&p=4)

![[Pasted image 20260518205650.png]]

# Redis 基础笔记

## 一、Redis 简介

- 诞生时间：2009 年
- 全称：**Remote Dictionary Server（远程词典服务器）**
- 定位：基于内存的键值型 NoSQL 数据库

---

## 二、Redis 核心特征

1. **键值（key-value）型存储**
    
    - value 支持多种数据结构（字符串、列表、哈希、集合、有序集合等），功能丰富。
    
2. **单线程模型，命令原子性**
    
    - 单线程处理所有命令，不存在并发竞争问题，每个命令天然具备原子性。
    
3. **低延迟、高性能**
    
    - 核心原因：==基于内存存储== + IO 多路复用 + 高效编码实现。 （
    
4. **支持数据持久化**
    
    - 可通过 RDB、AOF 两种方式将内存数据持久化到磁盘，防止数据丢失。
    
5. **支持集群模式**
    
    - 支持主从集群（主从复制、哨兵）、分片集群（Redis Cluster），实现高可用与水平扩展。
    
6. **多语言客户端支持**
    
    - 几乎支持所有主流编程语言（C/C++、Java、Python、Go、Node.js 等）。
    

---

## 补充：Redis 作者

- Salvatore Sanfilippo，网名 **ANTIREZ**，Redis 的原始作者与核心维护者。
