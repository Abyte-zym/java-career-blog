# 🔥 2026 Java实习生面试：Redis 10道高频题+标准答案

> 发布日期：2026-05-22 | SEO关键词：Redis面试题 Java实习 2026

---

Redis是Java后端面试必考题。我整理了2026年最高频的10道Redis面试题，每道附带标准答案。背完这些，Redis部分面试基本稳了。

---

### 1. Redis为什么这么快？

**答案：** 四点——①纯内存操作，读写速度10W+QPS ②单线程模型，避免上下文切换和锁竞争 ③IO多路复用，一个线程处理多个连接 ④RESP协议简单，解析开销极小。

> 💡 面试亮点：提到"单线程"时补一句——"Redis 6.0引入了多线程IO，但命令执行仍然是单线程的"。

---

### 2. Redis有哪些数据类型？各自使用场景？

| 类型 | 场景 |
|------|------|
| **String** | 缓存对象、计数器（incr）、分布式锁（setnx） |
| **Hash** | 存储用户信息、购物车（field-value） |
| **List** | 消息队列（lpush/rpop）、最新消息列表 |
| **Set** | 标签（sadd）、共同好友（sinter）、抽奖（srandmember） |
| **ZSet** | 排行榜（zadd+zrevrange）、延迟队列（score=时间戳） |
| **Stream** | 可靠消息队列（Redis 5.0+） |
| **BitMap** | 签到统计、用户在线状态 |
| **Geo** | 附近的人、地理位置计算 |

---

### 3. Redis缓存穿透、击穿、雪崩怎么解决？

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| **穿透** | 查不存在的数据，绕过缓存打DB | 布隆过滤器、缓存空对象（短TTL） |
| **击穿** | 热点key过期，并发打DB | 互斥锁重建缓存、逻辑过期（永不过期+后台更新） |
| **雪崩** | 大量key同时过期，DB压力暴增 | TTL加随机值（如300s±60s）、多级缓存、限流降级 |

---

### 4. Redis持久化方式RDB和AOF的区别？

| 维度 | RDB | AOF |
|------|-----|-----|
| 原理 | 全量快照（fork子进程dump） | 追加写命令 |
| 性能 | 高 | 较低（取决于fsync策略） |
| 数据安全 | 可能丢最后一次快照后的数据 | 最多丢1秒（everysec） |
| 文件大小 | 小 | 大，需BGREWRITEAOF |
| 恢复速度 | 快 | 慢 |
| **最佳实践** | 两者同时开启，RDB冷备+AOF增量恢复 |

---

### 5. 怎么用Redis实现分布式锁？

**答案：**
```java
// 加锁：SET lock_key unique_value NX EX 30
String result = redis.set("lock:order:123", uuid, "NX", "EX", 30);

// 解锁：Lua脚本原子校验+删除
String lua = "if redis.call('get',KEYS[1])==ARGV[1] then return redis.call('del',KEYS[1]) else return 0 end";
redis.eval(lua, Collections.singletonList("lock:order:123"), Collections.singletonList(uuid));
```

> 💡 面试亮点：提到Redisson的Watch Dog机制——锁快过期时自动续期，防止业务没跑完锁就释放。

---

### 6. Redis主从、哨兵、集群有什么区别？

|  | 主从 | 哨兵 | 集群 |
|---|------|------|------|
| 职责 | 读写分离、数据备份 | 自动故障转移 | 数据分片+高可用 |
| 数据分片 | ❌ | ❌ | ✅ 16384槽 |
| 自动故障转移 | ❌ 手动 | ✅ | ✅ Gossip自动 |
| 节点数 | ≥2 | ≥3(奇数哨兵) | ≥6(3主3从) |

---

### 7. Redis过期删除策略是什么？

**答案：** 惰性删除 + 定期删除。

- **惰性删除**：访问key时检查是否过期，过期则删
- **定期删除**：每100ms随机抽20个key检查，过期比例>25%继续抽

> 💡 如果大量key同时过期且没被访问，可能会内存堆积。Redis 4.0+引入了`lazyfree`——异步删除大key。

---

### 8. Redis内存淘汰策略有哪些？

| 策略 | 行为 |
|------|------|
| **noeviction** | 不淘汰，写满报错（默认） |
| **allkeys-lru** | 所有key中淘汰最近最少使用的 |
| **volatile-lru** | 有过期时间的key中淘汰LRU |
| **allkeys-random** | 所有key中随机淘汰 |
| **volatile-ttl** | 有过期时间的key中淘汰TTL最短的 |
| **allkeys-lfu** | Redis 4.0+ LFU算法 |

> 💡 缓存场景一般用`allkeys-lru`或`allkeys-lfu`。

---

### 9. 怎么保证Redis和MySQL数据一致性？

**答案：** 先更新数据库，再删除缓存（Cache Aside Pattern）。

```
1. 更新MySQL
2. 删除Redis缓存（不是更新！）
3. 下次读时自动重建缓存
```

> 💡 追问"删除失败怎么办？"→ 延迟双删 + 消息队列重试 + 设置过期时间兜底。

---

### 10. Redis大Key和热Key怎么处理？

|  | BigKey | HotKey |
|---|--------|--------|
| 定义 | value >10KB 或集合元素上万 | QPS单key >10W/s |
| 危害 | 阻塞网络、慢查询、迁移困难 | 单节点打满 |
| 发现 | `redis-cli --bigkeys` / `MEMORY USAGE key` | `redis-cli --hotkeys` / monitor采样 |
| 处理 | 拆分（hash分桶）、大集合用scan代替smembers | 本地缓存(Caffeine) + 读写分离 + key散列(murmurhash) |

---

## 🎯 面试速记口诀

```
纯内存单线程多路复用——快
穿透布隆击穿锁雪崩随机——三穿
RDB快照AOF命令——两持久
惰性删定期扫lazyfree——过期
Cache Aside先写后删——一致性
```

---

💡 **这篇帮到你了？** 扫码赞赏一杯咖啡 ☕

![微信赞赏](qrcode-wx.png)

📚 更多面试题：\
[Spring AI面试全攻略](spring-ai-intern-guide-2026.md)\
[Java面试速成手册](java-intern-handbook.md)\
[AI方向Java面试3问](ai-java-interview-2026.md)
