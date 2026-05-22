# 2026 Java实习面试速成手册

> 免费下载 | 基于50+真实面经整理 | [支持作者☕](https://Abyte-zym.github.io/java-resume-builder/#donate)

---

## 目录
1. 简历模板（3天10个HR主动联系）
2. 10道必考面试题+标准答案
3. RAG项目面试话术
4. 秒杀项目面试话术
5. AI时代Java面试新趋势

---

## 第1章：简历模板

### 技术栈排列公式
```
微服务/分布式 > 中间件 > 容器化 > 数据库调优 > 基础
```

### 项目量化公式
```
动词 + 技术点 + 数字结果
例：基于Redis+Lua实现分布式限流，QPS从200提升到3000
```

### 完整简历模板
```
【求职意向】Java后端开发实习生 | 期望城市：不限 | 6月底到岗 | 可实习6个月

【技术栈】
Spring Cloud Alibaba(Nacos+Sentinel+RocketMQ) | Docker | Redis(缓存穿透/分布式锁) | MySQL(索引优化/分库分表) | JVM(GC调优) | Spring Boot+MyBatis

【项目经历】
项目一：RAG智能知识库系统
- 基于Spring AI+LangChain4j实现RAG架构
- 文档切片优化召回率从60%到85%
- Docker Compose一键部署

项目二：微服务秒杀系统
- Redis+Lua脚本实现库存原子扣减，TPS 5000+
- RocketMQ异步削峰，系统稳定性99.9%
- Sentinel限流熔断保护

【荣誉奖项】
- AIGC创新赛全国三等奖
- 数学建模省一等奖
```

---

## 第2章：10道必考题

### Q1: HashMap底层原理
数组+链表+红黑树。put时计算hash→定位数组索引→冲突时链表/红黑树插入。扩容时容量翻倍，rehash。

### Q2: ConcurrentHashMap如何保证线程安全
JDK8用CAS+synchronized，锁粒度在链表头节点。扩容时多线程并发迁移。

### Q3: MySQL索引失效场景
- 最左前缀不走
- LIKE '%xx' 
- 索引列做函数操作
- OR条件有非索引列

### Q4: Redis缓存穿透/击穿/雪崩
穿透→布隆过滤器；击穿→互斥锁；雪崩→过期时间加随机

### Q5: Spring Boot自动配置原理
@EnableAutoConfiguration → AutoConfigurationImportSelector → 读取spring.factories → @Conditional条件装配

### Q6: JVM垃圾回收算法
标记清除/标记复制/标记整理。CMS低停顿但有碎片，G1分区收集可预测停顿。

### Q7: @Transactional失效场景
- 同类内部方法调用（this.xxx()）
- 非public方法
- 异常被catch未抛出
- rollbackFor设置错误

### Q8: Spring Bean生命周期
实例化→属性注入→Aware接口→BeanPostProcessor前置→init→BeanPostProcessor后置→使用→destroy

### Q9: Redis持久化RDB vs AOF
RDB全量快照性能高但可能丢数据。AOF追加写命令数据安全但文件大。最佳实践：同时开启。

### Q10: 分布式锁实现
Redis setnx+过期时间+WatchDog自动续期。Redisson封装。注意锁续期和可重入。

---

## 第3章：RAG项目话术

**面试官：** "介绍一下你的RAG项目"

> 我做了一个企业智能知识库问答系统。核心架构是检索增强生成——用户提问后，系统先检索最相关的文档片段，再把检索结果作为上下文输入大模型生成回答。技术栈用了Spring AI做AI统一抽象，Redis做向量存储，MySQL存原始文档。最大挑战是检索准确率，通过HyDE技术和重排序机制，把召回率从60%提到了85%。

---

## 第4章：秒杀项目话术

**面试官：** "秒杀系统怎么防超卖？"

> 核心是Redis+Lua脚本实现原子库存扣减。Lua脚本里先检查库存>0，再扣减，整个操作在Redis单线程里原子执行。扣减成功后异步发消息到RocketMQ落库，实现削峰。还有Sentinel在入口做限流保护。上线后TPS从500提升到5000，0超卖。

---

## 第5章：AI时代Java面试新趋势

2026年Java面试三个变化：
1. **Spring AI成标配**——不会Spring AI的Java开发正在被淘汰
2. **RAG比CRUD值钱**——懂AI落地的Java开发薪资高30-80%
3. **Agent是下一个考点**——明年的面试题已经开始问Agent了

---

> 📝 更多资源：
> - [简历生成器](https://Abyte-zym.github.io/java-resume-builder/) — 免费在线工具
> - [面试题库](https://Abyte-zym.github.io/java-interview-quiz/) — 50道高频题
> - [技术博客](https://Abyte-zym.github.io/java-career-blog/) — 持续更新面经

> ☕ 如果这本手册帮到你，[请作者喝杯咖啡](https://Abyte-zym.github.io/java-resume-builder/#donate)
