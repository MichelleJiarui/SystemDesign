我来为您详细讲解课程的每一个知识点，用最通俗易懂的语言，中英文对照说明。内容很多，我会分章节系统讲解。

---

# 🏗 第一部分：系统设计基础 · Section 1: System Design Basics

---

## 1. 单体架构 vs 微服务 · Monolith vs Microservices

### 什么是单体架构？What is a Monolith?

单体架构就是把整个应用程序写在一起，所有功能（用户界面、业务逻辑、数据库操作）都在同一个程序里运行。

A monolith is a single program where the UI, business logic, and database access all run together as one unit.

**单体的优点 Pros:**
- 开始时非常简单，不用复杂的设计 · Simple at first, no over-engineering
- 只有一个代码库，好管理 · Single codebase, easy to manage
- 小规模时资源利用率高 · Resource efficient at small scale

**单体的缺点 Cons:**
- 随着功能增多，代码越来越难维护 · Modularity is hard to enforce as the app grows
- 扩展困难：只有"购买"功能很忙，但你必须扩展整个应用 · Scaling is a challenge — you must scale the entire app
- 部署是"全或无"，改一个功能要重新部署整个系统 · All-or-nothing deployment
- 发布周期长 · Long release cycles

**关键误解澄清：** API ≠ 微服务！单体应用也可以有API。单体可以通过一个入口函数检查URL路径然后执行对应逻辑。

**Key misconception:** APIs do NOT equal microservices! A monolith can have APIs — an entry function checks the URL path and executes logic accordingly.

---

### 什么是微服务？What are Microservices?

微服务就是把一个大应用拆分成多个小的、独立的服务，每个服务负责一个具体功能。

Microservices split an app into small, independent services. Each service handles one specific function.

**生活类比：** 单体就像一个大饭馆，所有菜都在一个厨房做。微服务就像美食广场，每个摊位专门做一种食物——哪个摊位忙就加那个摊位的人手，互不干扰。

**Life analogy:** A monolith is like a single kitchen cooking everything. Microservices are like a food court — each stall specializes in one thing, and you can expand only the busy stall.

**微服务的特点：**
- **独立扩展 Independent scaling：** 只有"搜索"功能很忙？只扩展搜索服务，不用扩展整个系统
- **独立部署 Independent deployment：** 改了支付模块，只部署支付服务，不影响其他服务
- **独立测试 Independent testing：** 每个服务可以单独测试
- **多语言持久化 Polyglot persistence：** 每个服务可以用最适合自己的数据库

**在 AWS 上部署微服务的方式：**
1. **EC2 + Auto Scaling Group** — 每个微服务放在独立的 EC2 实例组里，按需自动扩展
2. **Serverless (Lambda)** — Lambda 自动扩展，无需管理服务器
3. **Container (ECS/EKS)** — Docker 容器，用 Kubernetes 编排，通过 ALB Ingress 路由
4. **混合 Mix and Match** — 不同服务用不同方式，例如搜索用 EC2，通知用 Lambda

---

## 2. 负载均衡器 · Load Balancer

### 为什么需要负载均衡器？Why do we need it?

想象你的网站只有一台服务器，IP 是 10.10.100.200。现在用户变多了，你加了几台服务器，但用户怎么知道访问哪个 IP？

Imagine your website has one server at 10.10.100.200. As users grow, you add more servers — but how do users know which IP to use?

**负载均衡器解决这个问题：** 它提供一个统一的域名（如 123xyz.com），自动将流量分发到后面的多台服务器，用户完全不需要知道有多少台服务器。

**The load balancer solves this:** It provides a single domain name (e.g., 123xyz.com) and automatically distributes traffic across multiple backend servers. Users never know how many servers exist.

**负载均衡器的功能：**
- 自动分发流量到多个目标 · Automatically distributes incoming traffic
- 健康检查：检测服务器是否正常，不把流量发送到故障服务器 · Monitors health of targets
- 集成 SSL · Integrates with SSL
- "弹性"：由 AWS 管理，自身高度可用 · "Elastic" — managed by AWS, inherently highly available

---

### 应用负载均衡器 Application Load Balancer (ALB)

ALB 工作在 **OSI 第7层（应用层）**，能理解 HTTP/HTTPS 请求的内容。

ALB operates at **OSI Layer 7 (Application Layer)** — it understands the content of HTTP/HTTPS requests.

**ALB 的能力：**
- **基于 URL 路径路由：** /get 的请求发到 A 服务器组，/post 发到 B 服务器组，/delete 发到 C 服务器组
- **目标组 Target Groups：** 把服务器组织成不同的目标组，ALB 根据规则路由到对应组
- **SSL 终止：** 在 ALB 层解密 HTTPS，后端可以用 HTTP 通信（省去后端加密开销）
- **粘性会话 Sticky Session：** 同一用户的请求总是发到同一台服务器
- 支持 EC2、Lambda、IP 地址作为后端目标

---

### 网络负载均衡器 Network Load Balancer (NLB)

NLB 工作在 **OSI 第4层（传输层）**，基于 TCP/UDP 协议和端口号路由。

NLB operates at **OSI Layer 4 (Transport Layer)** — routes based on protocol and port.

**NLB 的特点：**
- SSL 透传（不解密，直接转发给后端处理）
- 暴露静态 IP 地址（ALB 没有固定 IP）
- 处理突发流量（spiky traffic）更好
- 支持 EC2 实例和 IP 地址作为后端（不支持 Lambda）

---

### ALB vs NLB 如何选择？

| 场景 | 选择 |
|------|------|
| 突发流量（spiky traffic） | NLB |
| 稳定的高流量 | ALB |
| 需要固定 IP | NLB |
| 需要按 URL 路径路由 | ALB |
| API Gateway REST API 私有集成 | NLB + PrivateLink |
| 后端是 Lambda | ALB |

---

## 3. API 网关 · API Gateway

### 什么是 API？What is an API?

**现实类比：** 餐厅里的服务员就是 API。你（客户/调用方）告诉服务员你要什么（输入），服务员去厨房（后端系统）取来食物（输出）。你不需要知道厨房里发生了什么。

**Real-world analogy:** A waiter in a restaurant is an API. You (the client) tell the waiter what you want (input), the waiter goes to the kitchen (backend system) and brings back food (output). You don't need to know what happens in the kitchen.

**为什么要用 API？Why use APIs?**
- 流量管理和负载均衡
- 特定的输入/输出格式控制
- 认证与授权（AuthN/Z）
- 将后端系统与外部调用者解耦

---

### AWS API Gateway vs ALB 详细对比

**API Gateway（推荐用于对外 API）：**
- 完全托管、无服务器，自动扩展
- 可设置限速和突发限制（默认 10k rps，5k burst）
- 集成 AWS WAF 保护
- 请求验证、请求/响应映射
- 丰富的认证集成：API Key、IAM、Cognito、外部 IdP（OIDC）
- 响应缓存
- 超时限制：**30秒**
- 支持 Swagger/OpenAPI 3.0 导入导出
- **按使用量付费 Pay per use**

**ALB（推荐用于内部服务间通信）：**
- 基础设施由 AWS 管理，高可用，弹性
- 无限速和突发控制功能
- 无请求验证/映射能力
- 无响应缓存
- 超时最长 **4000秒**（更适合长连接）
- 有健康检查
- 可以获取静态 IP（配合 Global Accelerator）
- **按使用时间付费 Pay for idle**（有闲置也收费）

---

## 4. 垂直扩展 vs 水平扩展 · Vertical vs Horizontal Scaling

### 垂直扩展（向上扩展）Vertical Scaling (Scale Up)

把一台服务器换成更大、更强的服务器。比如从 m5.large 升级到 m5.12xlarge。

Replace a server with a bigger, more powerful one. E.g., upgrade from m5.large to m5.12xlarge.

**挑战 Challenges:**
- 扩容/缩容需要更长时间（需要停机或蓝绿部署）
- 扩容切换期间可能丢失事务
- 有上限：机器再大也有极限
- 非常昂贵

---

### 水平扩展（横向扩展）Horizontal Scaling (Scale Out)

增加更多同样大小的服务器，用负载均衡器分发流量。

Add more servers of the same size and use a load balancer to distribute traffic.

**优点 Advantages:**
- 扩容/缩容更快（直接添加/移除实例）
- 理论上可以无限扩展
- 更具成本效益（小机器比大机器划算）
- 故障影响范围小

**挑战：** 老代码可能需要重构才能支持水平扩展（要求应用是无状态的 stateless）

---

### 面试中的"好答案"vs"一般答案"

**一般答案：** "把 VM 放到 Auto Scaling Group 里，用负载均衡器"

**好答案还要提到：**
- 提前预热负载均衡器（Pre-warm Load Balancers）
- 运行 IEM（基础设施事件管理）
- 定时扩容（Scheduled Scaling）
- 使用轻量级 AMI（Lightweight AMI）
- 使用数据库代理（RDS Proxy，避免连接池耗尽）
- 增加账户限制（Increase Account Limits）
- 多账户多区域组合（Multi Account + Region）
- 考虑拆分为微服务

---

### 不同部署方式的扩展

**Serverless 扩展（Lambda）：**
- 使用数据库代理（RDS Proxy）
- API Gateway 启用缓存
- 使用 HTTP API（比 REST API 更快更便宜）
- Lambda 预置并发（Provisioned Concurrency，避免冷启动）
- 用 X-Ray 优化代码
- 用 CloudWatch Insights 优化配置

**容器/Kubernetes 扩展：**
- 预热负载均衡器
- 使用 ReplicaSet 运行多个 Pod 副本
- 使用 Cluster Overprovisioner 预先准备节点
- 数据库代理

---

## 5. 同步 vs 异步（事件驱动）架构 · Sync vs Async Architecture

### 同步架构 Synchronous Architecture

每个服务调用下一个服务并等待响应，就像打电话——你必须等对方接听。

Each service calls the next and waits for a response, like a phone call — you wait for the other person to answer.

**问题 Problems:**
- 所有组件必须同时扩展（一个达到上限，整个链路都崩溃）
- 如果某个服务宕机，整个请求失败
- 客户端必须重新发送请求才能重试
- 高并发时非常昂贵

---

### 事件驱动/异步架构 Event-Driven / Async Architecture

服务通过消息队列（如 SQS）通信。调用方发出消息后不等待，继续处理其他事情。就像发短信——发完就走，不用等回复。

Services communicate via a message queue (e.g., SQS). The caller sends a message and moves on. Like texting — send and go, no waiting.

**优点 Advantages:**
- 每个组件可以独立扩展
- 内置重试机制（SQS 自动重试）
- 比同步架构更具成本效益

---

### 最佳实践：两者结合使用

**例子：电商订单系统**
- **下订单（写操作）** → 异步：发消息到 SQS，立即返回"订单已接收"，后台慢慢处理
- **查询订单状态（读操作）** → 同步：用户要立即看到结果

**PubSub vs Queue：**
- Queue（队列）：点对点，一条消息只被一个消费者处理
- PubSub（发布订阅）：一条消息可以被多个订阅者接收

**Streaming vs Messaging：**
- Streaming（如 Kinesis）：实时数据流，有序，可重放
- Messaging（如 SQS）：消息队列，处理后删除

---

## 6. SQL vs NoSQL 数据库

### 关系型数据库（SQL）

有固定的表结构（Schema），数据通过表之间的关系（join）关联。强调 ACID 特性：
- **Atomicity 原子性：** 事务要么全部成功，要么全部失败
- **Consistency 一致性：** 数据始终处于有效状态
- **Isolation 隔离性：** 并发事务互不干扰
- **Durability 持久性：** 已提交的事务永久保存

通常**垂直扩展**（加大机器）。

AWS 选项：**Amazon Aurora**（MySQL/PostgreSQL兼容，比标准MySQL快5倍，比PostgreSQL快3倍，成本仅1/10）、**Amazon RDS**

---

### NoSQL 数据库

无固定结构（Schemaless），可以存储结构化和非结构化数据。遵循 **CAP 定理**：
- **Consistency（一致性）**
- **Availability（可用性）**
- **Partition Tolerance（分区容忍性）**
- *三者只能同时满足两个*

通常**水平扩展**（加更多机器）。

AWS 选项：**Amazon DynamoDB**（键值+文档，任何规模下个位数毫秒延迟，自动跨3个AZ复制）、**DocumentDB**、**Amazon Keyspaces（Cassandra）**

---

### Aurora vs DynamoDB 详细对比

| 特性 | Aurora | DynamoDB |
|------|--------|----------|
| 类型 | 关系型 | 键值/文档 NoSQL |
| 兼容性 | MySQL / PostgreSQL | 原生 AWS |
| 性能 | MySQL 5x, PostgreSQL 3x | 个位数毫秒，任意规模 |
| Multi-Master | MySQL 支持 | 支持 |
| 跨区域复制 | Active-Passive（MySQL Global DB） | Active-Active（Global Tables） |
| 高可用 | Multi-AZ + Read Replicas | 自动跨3个AZ |
| 扩展方式 | 垂直为主 | 自动水平扩展 |
| 最适合 | 复杂查询、事务、关联数据 | 高吞吐量读写、灵活结构 |

**核心原则：Right tool for right job（用正确的工具做正确的事）**

---

## 7. WebSocket · 实时双向通信

### 普通 HTTP 请求的局限

普通 HTTP 是"请求-响应"模式：**只有客户端可以发起请求**，服务器不能主动推送消息给客户端。

Normal HTTP is request-response: **only the client can initiate**. The server can't push data to the client.

---

### WebSocket 解决什么问题？

WebSocket 在客户端和服务器之间建立一条**持久的双向连接**，连接建立后，**服务器可以随时主动推送消息给客户端**，无需客户端轮询。

WebSocket establishes a **persistent, bidirectional connection**. Once connected, **the server can push messages to the client at any time**.

**类比：** HTTP = 按门铃（每次都要按一次），WebSocket = 开了个免提对讲机（随时可以说话）

**使用场景：**
- 聊天应用（WhatsApp、Telegram）
- 聊天机器人（Chatbots）
- 实时仪表盘
- 在线游戏
- 协作编辑（如 Google Docs）

**AWS 实现：** API Gateway WebSocket API + Lambda + DynamoDB（存储连接ID）

---

## 8. 缓存 · Caching

### 为什么需要缓存？Why Cache?

每次用户请求都去数据库查询会很慢且昂贵（数据库需要运行复杂查询）。如果把常用数据存在内存里（缓存），下次直接从内存读取，速度极快，成本极低。

Every request hitting the database is slow and expensive. Storing frequently accessed data in memory (cache) means the next request is lightning fast and cheap.

**类比：** 不用每次都去图书馆借书，把常用的书放在书桌上随时拿。

---

### 缓存可以放在哪里？Where can caches live?

缓存不只在后端！每一层都可以有缓存：
- **CDN 层：** CloudFront 缓存静态内容（图片、JS、CSS）
- **API Gateway 层：** 缓存 API 响应，相同请求直接返回缓存结果
- **后端层：** ElastiCache（Redis/Memcached）缓存数据库查询结果
- **数据库层：** DynamoDB DAX（DynamoDB Accelerator）微秒级响应

---

### 缓存如何填充？How is cache populated?

**懒加载 Lazy Loading（最常见）：**
1. 请求来了，先查缓存
2. 缓存命中（Cache Hit）→ 直接返回，快！
3. 缓存未命中（Cache Miss）→ 查数据库，把结果写入缓存，下次就有了

**写穿透 Write-Through：**
- 每次写入数据库时，**同时**更新缓存
- 缓存始终是最新的，但每次写操作都需要额外写缓存，开销更大

---

### 缓存如何删除？TTL

**TTL（Time To Live，生存时间）：** 每个缓存条目都有一个过期时间，到期自动删除。比如设置 TTL = 1小时，1小时后该数据自动从缓存中清除，下次请求会重新从数据库获取。

也可以在后端代码中主动更新或删除缓存条目。

---

### Redis vs Memcached

| 特性 | Redis | Memcached |
|------|-------|-----------|
| 数据类型 | 复杂（List, Set, Hash, SortedSet等） | 简单（String） |
| 排序/排名 | 支持（适合排行榜） | 不支持 |
| 数据复制 | 支持 | 不支持 |
| 自动故障转移 | 支持 | 不支持 |
| 备份/恢复 | 支持 | 不支持 |
| 发布/订阅 | 支持 | 不支持 |
| 多数据库 | 支持 | 不支持 |
| 多线程扩展 | 单线程（但性能已够） | 多线程，更适合大节点 |

**一般选 Redis**，除非你只需要最简单的键值缓存且要多线程扩展。

**ElastiCache 使用场景：**
- 缓存频繁访问的数据（用户资料、商品描述、偏好设置）
- 游戏排行榜（Redis SortedSet）
- 实时推荐系统
- 消息系统

---

## 9. 高可用 vs 容错 · High Availability vs Fault Tolerance

### 什么是高可用？What is High Availability?

系统在某些组件故障时**仍然继续运行**，但可能以降低的容量运行。系统保证一定百分比的正常运行时间（uptime）。

The system **continues functioning** even when some components fail, possibly at reduced capacity. Guarantees a certain percentage of uptime.

**单点故障（SPOF）分析：** 你必须分析每个组件，看它是否是单点故障：
- 应用服务器 → 多 AZ 部署
- 数据库 → Multi-AZ 配置
- 负载均衡器 → AWS ELB 本身已是高可用（由 AWS 管理）

**实现 HA：** 跨多个可用区（AZ）部署，使用 Auto Scaling Group。注意：Auto Scaling Group 提供**扩展性**，不是高可用本身——因为新实例启动需要时间。

---

### 什么是容错？What is Fault Tolerance?

即使发生故障，系统也能**维持 100% 的完整处理能力**。代价：需要更多资源（更贵）。

Even during failure, the system **maintains 100% full processing capacity**. Cost: requires more idle capacity (more expensive).

**例子：**
- 你需要 100 TPS（每秒100个事务）
- **高可用做法：** 每个 AZ 跑 50 TPS，共2个AZ，总计100 TPS。一个AZ挂了，剩下50 TPS（降级运行）
- **容错做法：** 每个 AZ 都跑 100 TPS，3个AZ。一个AZ挂了，另外两个每个还有100 TPS → 保持完整能力

**重要提示：** 容错比高可用贵得多。设计和面试时，不要过度纠结成本，重点展示你理解两者的区别。

---

## 10. 分布式系统与哈希 · Distributed Systems & Hashing

### 集中式 vs 分布式系统

**集中式系统：** 所有东西在一台服务器上运行。简单，但有单点故障，扩展有上限。

**分布式系统：** 系统分布在多台服务器上。没有单点故障，可以水平扩展，是现代系统的主流。

---

### 哈希函数 Hash Function

哈希函数接受**任意长度的输入**，产生**固定长度的输出**（哈希值）。

- 相同输入 → 永远相同输出
- 输入稍有变化 → 输出完全不同
- 哈希函数应该很快

**类比：** 就像一台"绞肉机"，不管你放什么食材进去，出来的都是固定大小的肉末。

**系统设计中的应用：**
- DynamoDB 用分区键（Partition Key）的哈希值决定数据存在哪个分区
- 数据库分片（Sharding）用哈希决定数据存在哪个分片
- 负载均衡中的一致性哈希

---

### DynamoDB 分区原理

DynamoDB 的表在底层被分成多个物理分区（Partition）。当你保存一条记录时：
1. 拿到该记录的**分区键（Partition Key）**
2. 对分区键运行**哈希函数**
3. 哈希值决定数据存在哪个分区
4. 查询时同样的哈希计算，直接定位到正确分区

这就是为什么 DynamoDB 在任何规模下都能实现个位数毫秒延迟——通过哈希直接定位，不需要扫描整张表。

---

### 数据库分片 Database Sharding（水平分区）

把一个大数据库表拆分到多台服务器上，每台服务器存储一个"分片（Shard）"。

Split one large database table across multiple servers, each holding a "shard."

**优点：**
- 支持水平扩展
- 更快的查询响应时间（数据更少）
- 限制爆炸半径（一个分片故障不影响其他）

**缺点：**
- 分片可能不均衡（热点问题）
- 重新分片（resharding）非常痛苦
- 需要额外实现分片逻辑

---

## 11. 灾难恢复 · Disaster Recovery

### RPO — 恢复点目标 Recovery Point Objective

**RPO = 灾难发生时，你最多能接受丢失多少数据（用时间衡量）**

例如：你每小时备份一次数据，下午1点备份完，2点发生灾难。那么1点到2点之间的数据全部丢失，RPO = 1小时。

**如何减少 RPO？**
- 更频繁备份（30分钟、15分钟……）
- 实时复制（Replication）→ RPO ≈ 0

---

### RTO — 恢复时间目标 Recovery Time Objective

**RTO = 灾难发生后，应用最多能停机多久**

例如：下午1点系统崩溃，下午2点恢复服务，RTO = 1小时。

RTO 和 RPO 越低，成本越高，因为需要更多的备用资源。

---

### 四种 DR 策略（从便宜到最贵）

**1. Backup & Restore（备份恢复）**
- 定期备份数据到 S3
- 灾难时重新从备份恢复环境
- RPO 高（取决于备份频率），RTO 高（需要时间重建）
- 最便宜，适合不那么关键的系统

**2. Pilot Light（试点灯）**
- 在 DR 区域保持最小化的核心系统运行（数据库持续复制）
- 灾难时快速扩展
- RPO 低，RTO 中等

**3. Warm Standby（温备份）**
- 在 DR 区域运行一个缩小版的生产环境（可以处理流量，但容量不足）
- 灾难时扩展到完整容量
- RPO 很低，RTO 较低

**4. Multi-site Active/Active（多站点主主）**
- 在多个区域同时运行完整的生产环境，流量同时分发
- RPO ≈ 0，RTO ≈ 0
- 最贵，但最可靠

**核心原则：** Active-Active 不是唯一的 DR 解决方案！RPO/RTO 的要求决定你用哪种策略。

---

## 12. 十二因素应用 · The Twelve-Factor App

2012年 Heroku 工程师提出的云原生应用最佳实践，帮助构建可扩展、可移植、快速恢复的应用。

**面试注意：** 不需要记住12个因素对应的数字，理解概念更重要。

### 最重要的几个因素：

**I. 代码库（Codebase）：**
一个代码库，多个部署目标（Dev/Staging/Prod）。目标：代码无需修改即可部署到任何环境。不同应用 = 不同代码库，不要把多个应用放在同一代码库。

**II. 依赖（Dependencies）：**
明确声明并隔离所有依赖。使用 requirements.txt、package.json、Dockerfile 等。不依赖系统预装的包。容器（Docker）天然解决这个问题。

**III. 配置（Config）：**
把配置（数据库密码、API Key）存在**环境变量**中，不要硬编码在代码里。AWS 用 **Secrets Manager** 管理敏感配置。

**IV. 后端服务（Backing Services）：**
把数据库、队列、缓存等后端服务视为"附加资源"。同一份代码应该能连接本地数据库或云端数据库，只需更换配置，无需改代码。

**VI. 进程（Processes）：**
应用作为**无状态进程**运行，不在进程内存中存储用户数据。需要持久化的数据存到数据库或缓存。违反此原则的例子：使用粘性会话（Sticky Sessions）。

**IX. 可弃置性（Disposability）：**
进程随时可以启动或停止。**优雅关闭（Graceful Shutdown）：**
1. 停止接受新请求
2. 等待当前请求处理完
3. 退出
不能优雅关闭时，把任务放回队列。

**X. 开发/生产环境一致性（Dev/Prod Parity）：**
开发和生产环境尽可能相似。容器（Docker）解决了工具差异问题。使用相同的后端服务（开发也用 PostgreSQL，不要开发用 SQLite、生产用 PostgreSQL）。

**XI. 日志（Logs）：**
把日志写到**标准输出（stdout）**，不要在应用里直接写文件或连接日志系统。由运行环境的日志代理（daemon）收集并转发到日志后端（CloudWatch、Splunk、ES）。好处：代码不需要在不同环境间修改日志配置。

---

## 13. 单元化架构 · Cell-Based Architecture

将整个系统分成多个独立的"单元（Cell）"，每个单元是一个完整的、独立运行的工作负载，服务一部分用户。

**组成：**
- **Cell Router（单元路由器）：** 最薄的一层，只负责把请求路由到正确的单元
- **Cell（单元）：** 包含运行所需的所有组件，完全独立运行
- **Control Plane（控制面）：** 负责创建单元、删除单元、迁移用户

**主要目标：** 限制爆炸半径（Failure Containment）。如果一个单元故障，只影响该单元的用户，其他用户不受影响。就像把鸡蛋放在不同篮子里。

**AWS 实现：** Route 53 + AWS Global Accelerator 做路由，每个 Cell 是独立的 VPC + 应用 + 数据库。

---

# 🔧 第二部分：可复用架构模式 · Section 2: Reusable Patterns

---

## 1. AWS 架构良好框架 · AWS Well-Architected Framework

**5 大支柱（不只适用于 AWS，是通用的架构原则）：**

1. **运营卓越（Operational Excellence）：** 运行和监控系统的能力，持续改进流程
2. **安全性（Security）：** 保护数据、系统和资产
3. **可靠性（Reliability）：** 系统从故障中恢复并满足需求的能力
4. **性能效率（Performance Efficiency）：** 高效使用计算资源
5. **成本优化（Cost Optimization）：** 避免不必要的支出

**面试用法：** 当面试官问"你如何保证设计是好的？"时，用这5个支柱来回答，展示你的系统性思维。同时要了解每个应用的优先级——不是所有应用都把安全排第一，有些可能成本优化更重要。

---

## 2. 三层架构详解 · Three-Tier Architecture

这是最常见、最基础的架构模式，几乎所有 Web 应用都是这个结构。

**三层：**
- **展示层（Presentation Layer / Frontend）：** 用户界面，用户直接交互的部分
- **应用层（Application Layer / Backend）：** 业务逻辑，处理请求、执行计算
- **数据层（Database Layer）：** 存储数据

---

### EC2 版三层架构（完整实现）

```
互联网用户
    ↓
[Route 53] (DNS)
    ↓
[CloudFront] (CDN，缓存静态内容)
    ↓
公共子网 (Public Subnet)
[ALB - 外部负载均衡器]
    ↓
私有子网 (Private Subnet)
[Auto Scaling Group: EC2 Webservers] AZ1 + AZ2
    ↓
私有子网
[ALB - 内部负载均衡器]
    ↓
私有子网
[Auto Scaling Group: EC2 Appservers] AZ1 + AZ2
    ↓
私有子网
[Amazon Aurora Multi-AZ]
```

**安全层：**
- NACL（网络访问控制列表）：子网级别的防火墙
- Security Group：实例级别的防火墙
- WAF（Web Application Firewall）：与 ALB 集成，防止 SQL 注入、XSS 等

**数据库优化：**
- **读写分离（Read Replica）：** 读请求发到只读副本，减轻主库压力
- **缓存层（ElastiCache）：** 热点数据缓存
- **查询优化（Query Tuning）**
- **Multi-AZ：** 自动故障转移

---

### 无服务器版三层架构

```
展示层：
  静态内容 → S3 + CloudFront
  
应用层：
  动态内容 → API Gateway + AWS Lambda
  
数据层：
  Amazon Aurora（关系型事务）
```

**优点：** Lambda 和 API Gateway 自动高可用，无需配置 Auto Scaling Group。按使用量付费，零流量时零成本。

---

### Kubernetes 版三层架构

```
展示层：
  ALB Ingress → Cluster Autoscaler → AZ1 + AZ2
  
应用层：
  ALB Ingress → Cluster Autoscaler → AZ1 + AZ2
  
数据层：
  Amazon Aurora
```

Kubernetes 的 Cluster Autoscaler 自动增减节点，HPA（Horizontal Pod Autoscaler）自动增减 Pod 数量。

---

## 3. 数据分析架构 · Data Analytics Architecture

**数据分析的4个步骤：**

**1. 收集数据（Collect）:**
- **Amazon Kinesis Data Firehose/Streams：** 实时数据流
- **Amazon MSK（Managed Streaming for Kafka）：** 托管的 Kafka 服务
- **Amazon S3：** 批量数据存储

**2. 转换数据（Transform / ETL）:**
- **AWS Glue：** 无服务器 ETL 工具
  - Glue Crawler：自动扫描数据，生成元数据目录
  - 可视化创建 ETL 流程（支持Python/Spark/Scala）
  - Glue DataBrew：无需编码清洗数据
- **Amazon EMR：** 托管大数据平台（Spark、Hive、HBase、Flink、Presto）

**3. 查询数据（Query）:**
- **Amazon Athena：** 直接用 SQL 查询 S3 中的数据，无服务器
- **Amazon Redshift：** 数据仓库，适合大规模 BI 查询
- **Redshift Spectrum：** 直接查询 S3 数据而不加载到 Redshift

**4. 报告与洞察（Reports & Insights）:**
- **Amazon QuickSight：** BI 可视化工具
- **Amazon SageMaker：** 机器学习，预测分析
- **Amazon Elasticsearch Service：** 搜索和日志分析

---

### 常见架构模式：

**架构1 — 点击流查询报告：**
Kinesis Firehose → S3 → Glue Crawler → Athena → QuickSight

**架构2 — ETL 和数据仓库：**
Kinesis Firehose → S3 → AWS Glue → Redshift → 报告

**架构3 — 统一数据目录：**
多种数据源（RDS、S3、Redshift、EC2上的DB）→ Glue Data Catalog → Athena → QuickSight

**数据湖（Data Lake）：** 以 S3 为核心，存储所有原始数据，按需使用不同工具处理和查询。S3 既是数据湖的存储层，也是所有 ETL 和查询工具的数据源。

---

## 4. 加密 · Encryption

### 加密基础

**加密过程：** 明文（Bob 1234）+ 加密密钥 + 加密算法 → 密文（A4$xacvf4）

---

### 客户端加密 vs 服务器端加密

**客户端加密（Client-Side Encryption）：**
- 数据在你的应用里**加密后才发送到 AWS**
- 你完全控制密钥，AWS 存储的始终是密文
- AWS 看不到你的明文数据
- 安全性最高，但密钥管理复杂

**服务器端加密（Server-Side Encryption）：**
- 数据通过 HTTPS 发到 AWS（传输加密），AWS 在存储时加密
- AWS 管理加密过程
- 你的应用代码简单，但 AWS 理论上可以解密数据

---

### 信封加密 Envelope Encryption

**问题：** 用一个密钥直接加密大量数据，密钥需要频繁轮换，轮换成本高。

**解决方案：信封加密**
1. 有一个**主密钥（CMK - Customer Master Key）**，存在 KMS 中，从不离开 KMS
2. 每次加密数据时，生成一个**数据密钥（Data Key）**
3. 用**数据密钥**加密实际数据
4. 用**主密钥**加密数据密钥
5. 存储：加密后的数据 + 加密后的数据密钥（装在一个"信封"里）

**好处：** 主密钥不直接接触大量数据，轮换主密钥只需要重新加密数据密钥，不需要重新加密所有数据。

---

### AWS KMS（密钥管理服务）

**功能：**
- 完全托管
- 集中密钥管理
- 与几乎所有 AWS 服务集成
- 内置审计（CloudTrail 记录每次密钥使用）
- 安全合规

**两种 CMK 类型：**

| | AWS 托管 CMK | 客户托管 CMK |
|---|---|---|
| 标识 | aws/服务名 | 自定义名称 |
| 创建者 | AWS | 客户 |
| 删除 | 不可删除 | 可删除/启用/禁用 |
| IAM 角色集成 | 不能绑定自定义角色 | 可以绑定自定义角色 |
| 轮换 | 每3年自动轮换 | 每年自动轮换，或手动 |

---

## 5. 传输安全 · Security in Transit (TLS/SSL)

### HTTP vs HTTPS

**HTTP：** 所有数据以明文传输，任何人在网络中都能看到。不能在生产环境使用！

**HTTPS：** 所有数据加密传输。使用 TLS（Transport Layer Security，传输层安全协议），TLS 是 SSL 的升级版，更快更安全。

---

### TLS 握手流程（详细步骤）

1. **客户端 Hello：** 浏览器告诉服务器"我支持哪些加密算法"
2. **服务器 Hello + 证书：** 服务器选择加密算法，发回自己的 SSL 证书（包含公钥）
3. **客户端验证证书：** 浏览器用本地存储的CA（证书颁发机构）列表验证证书的合法性
4. **客户端密钥交换：** 浏览器生成预主密钥（pre-master key），用服务器的公钥加密后发送
5. **服务器解密：** 服务器用自己的私钥解密预主密钥
6. **生成共享密钥：** 双方都用预主密钥计算出相同的共享密钥（对称密钥）
7. **加密通信开始：** 后续所有通信都用这个共享密钥加密

**类比：** 就像两个人用不同语言交流，先约定用什么语言，再交换密码本，最后用密码本通话。

---

### 双向 TLS (mTLS - Mutual TLS)

**普通 TLS：** 只有客户端验证服务器的证书（你验证银行网站是真的）

**mTLS：** 客户端和服务器**互相验证**对方的证书

**使用场景：**
- B2B（企业对企业）通信，机器之间相互认证
- 微服务之间的通信（服务网格）
- 普通 TLS 用于面向用户的浏览器（用户不持有证书）

---

### AWS 中各服务的 TLS 处理

**API Gateway：**
- 必须使用 HTTPS
- 默认使用 AWS 证书，自定义域名可使用自己的证书（ACM）
- TLS 在 API Gateway 终止，到后端默认走 HTTP（内网内）
- 可以配置 API Gateway 对后端也使用 HTTPS

**ALB：**
- 可接受 HTTP 或 HTTPS 流量
- **SSL 卸载（SSL Offloading）：** TLS 在 ALB 终止，到后端走 HTTP（省去后端加密开销）
- 也可以配置到后端走 HTTPS

**NLB：**
- **SSL 透传（SSL Passthrough）：** 不能在 NLB 层终止 TLS
- 加密流量直接转发到后端，后端负责解密
- 后端需要有证书

---

## 6. IDS / IPS · 入侵检测与预防系统

### IDS（入侵检测系统）

- 扫描 L3-L7 层流量（从网络层到应用层）
- 检测可疑/恶意流量并发送告警
- **不阻止**流量，只是"发现并报告"
- 就像安保摄像头，录下来但不阻止

**AWS 实现：**
- 独立 EC2 实例运行 IDS 软件（监控旁路流量）
- EC2 实例上安装 IDS Agent（监控本机流量）
- CloudWatch + CloudTrail + 日志分析（基于日志的 IDS）

---

### IPS（入侵预防系统）

- 扫描 L3-L7 层流量
- 检测可疑流量并**主动阻止/隔离**
- 就像安保人员，发现威胁直接拦截

**AWS 实现：** AWS Network Firewall（内置 IPS 功能）

---

### IDS/IPS vs NACL/Security Group

| | NACL/Security Group | IDS/IPS |
|---|---|---|
| 工作层 | L3/L4（IP/端口） | L3-L7（包括应用内容） |
| 拒绝规则 | Security Group 无拒绝规则，NACL 有 | IPS 有拒绝规则 |
| 智能程度 | 静态规则（IP/端口白名单黑名单） | 动态智能规则，定期更新 |
| 性能影响 | 几乎无 | 可能引入延迟 |

---

# 🏗 第三部分：实战系统设计 · Section 3: Real System Design

---

## 面试必备知识清单

在进行任何系统设计面试前，确保你掌握：
1. 微服务设计原则
2. 负载均衡器 vs API Gateway 的选择
3. 同步 vs 异步模式
4. SQL vs NoSQL 数据库选择
5. 缓存（数据库缓存 + CDN）
6. 安全（认证/授权，静态加密，传输加密）
7. 如何使设计可扩展和高可用

---

## 案例1：URL 短链接系统 · URL Shortener (BitLy/TinyURL)

### 基础功能

**保存（Saving）：**
用户提交长链接 → 生成7位短码 → 存入数据库（短码 = 主键）→ 返回短链接

**检索（Retrieving）：**
用户访问短链接 → 在数据库中查找对应长链接 → 301/302 重定向到长链接

---

### 核心数学

```
可用字符：
  a-z = 26
  A-Z = 26
  0-9 = 10
  共计 = 62 个字符

7位短码：62^7 = 3.5万亿种组合 = 42位 (2^42)
```

即使每秒生成1000个短链接，3.5万亿个组合可以用1000年以上。

---

### 三种短码生成方案

**方案1：随机生成（Bad Randomizer）—— 不推荐**
- 随机生成7位字符
- 检查是否已存在
- 存在则重新生成
- **问题：** 高并发时，多台服务器可能同时生成相同的随机码，且越来越多的码被用，碰撞概率越来越高

**方案2：MD5 哈希 —— 一般**
- MD5 算法对长链接生成128位哈希值
- 取前42位，转换为 Base62（62进制）
- **优点：** 相同链接始终生成相同短码（自动去重）
- **问题：** 同一用户多次提交同一链接，只存一条记录。两个不同链接理论上可能碰撞（极小概率）

**方案3：计数器范围法（Range-Based Counter）—— 最佳**

**核心思想：** 如果我们有一个从1数到3.5万亿的全局计数器，每次生成短码时取一个数，把这个数转换为 Base62，就永远不会重复。

**问题：** 这个计数器放在哪里？

- **方案A：** 放在一台 EC2 上 → 单点故障！
- **方案B：** 放在 Redis（ElastiCache）里，用原子操作 → 好，但还是可能成为瓶颈

**最佳实现 — 范围分配：**
1. 把3.5万亿分成350万个段，每段100万个数
2. 使用 **Apache ZooKeeper**（运行在 Amazon EMR 上）管理段分配
3. 每台 Appserver 启动时，向 ZooKeeper 申请一个段（如 1-1,000,000）
4. 该 Appserver 在自己的段内独立计数（不需要协调），用完再申请新段
5. 把当前数字转换为 Base62 = 短码

**优点：** 
- 服务器之间不需要实时协调
- 无碰撞，数学上保证唯一
- 高可用（ZooKeeper 本身高可用）
- 横向扩展容易

**转换示例：**
```
42位数字 → Base62
数字 12345 = ?
12345 / 62 = 199 余 7
199 / 62 = 3 余 13
3 / 62 = 0 余 3
所以 12345 = Base62 的 "3dH" (每位映射到a-zA-Z0-9)
```

---

### 完整架构

```
用户访问 bit.ly/3dH5xAB
    ↓
Route 53（DNS）
    ↓
ALB
    ↓
Auto Scaling Group：EC2 Appservers（每台持有一段范围）
    ↓
    ├── ElastiCache for Redis（缓存热门短链接，毫秒级响应）
    └── Amazon Aurora（存储短码 → 长链接映射）
    
ZooKeeper (Amazon EMR)（管理段分配）
```

---

## 案例2：电商系统 · E-Commerce System (Amazon/Flipkart Style)

### 功能需求

- 商品目录（Product Catalog）
- 购物车（Shopping Cart）
- 购买商品（Buy Product）
- 商品推荐（Product Recommendation）

### 设计规格

- 可扩展（Scalable）
- 高可用（Highly Available）
- 成本效益（Cost Efficient）
- 安全（Secure）

---

### 架构（无服务器版本）

```
展示层（Presentation Layer）：
  静态内容（图片、JS、CSS）→ S3 + CloudFront
  
应用层（Application Layer）：
  动态 API → Amazon API Gateway → AWS Lambda
  
  Lambda 微服务：
  ├── Browse Service（浏览/搜索）
  └── Buy Service（购买/结账）
  
数据层（Database Layer）：
  ├── ElastiCache for Redis（购物车缓存、会话）
  ├── Amazon DynamoDB（商品目录、购物车数据）
  └── Amazon Aurora（订单、交易数据）

静态资源：
  Amazon S3（商品图片、文件）
```

---

### 数据库选择逻辑

**为什么购物车用 DynamoDB？**
- 购物车读写频率极高，结构简单（CartID + ProductID + Quantity）
- DynamoDB 无需固定 Schema，灵活添加字段
- 毫秒级延迟，任意规模

**为什么交易/订单用 Aurora？**
- 订单涉及扣减库存、扣款、创建订单等多步操作
- 必须保证 ACID 特性（原子性：要么全成功，要么全失败）
- 如果扣了钱但没创建订单，这是不可接受的

**购物车数据库设计示例：**

```
Product 表（DynamoDB）：
ProductID | Name       | Price | AvailableCount
100       | TV         | $450  | 5
200       | Face Mask  | $5    | 1000

ShoppingCart 表（DynamoDB）：
CartID | PersonLogin  | ProductID
10000  | john.wilson  | 100
10000  | john.wilson  | 200
20000  | tina.smith   | 300
```

---

### 高流量场景处理

**场景：** 双十一大促，流量是平时的100倍

**设计策略：**
1. **异步下单：** 订单请求发到 SQS 队列，立即返回"订单已接收"，后台 Lambda 慢慢处理
2. **读写分离：** 浏览用 DynamoDB + 缓存，下单写 Aurora
3. **多个微服务：** 浏览服务、购物车服务、支付服务各自独立扩展
4. **第三方 API：** 支付、物流等第三方 API 调用也通过 SQS 异步处理

```
高流量架构：
API Gateway → [浏览Lambda] → DynamoDB + ElastiCache
           → [下单Lambda] → SQS → [处理Lambda] → Aurora
```

---

### 商品推荐

**协同过滤（Collaborative Filtering）：**
- "买了这个商品的用户也买了……"
- 找到和你行为相似的用户，把他们买的推荐给你

课程建议参考 Tinder 系统设计的推荐算法部分了解更多细节。

---

### 安全设计

**认证（Authentication）：**
- 使用 Amazon Cognito 管理用户登录
- 支持社交登录（Google、Facebook）
- Cognito 返回 JWT Token，API Gateway 验证 Token

**传输安全：**
- API Gateway 必须 HTTPS
- CloudFront 默认 HTTPS
- HTTPS 到 S3

**第三方 API 凭证：**
- 把 API Key、Secret 存在 AWS Secrets Manager
- Lambda 在运行时从 Secrets Manager 读取，不硬编码在代码里

**静态数据加密：**
- DynamoDB：使用 KMS 加密
- Aurora：使用 KMS 加密
- S3：服务器端加密

---

### 数据库进阶问题（面试高频）

**分片（Sharding）：**
- 用户量大后，单个 DynamoDB 表可能成为瓶颈
- 使用分片键（如用户ID的哈希）分散到多个表

**读副本（Read Replica）：**
- Aurora 写主库，读从只读副本
- 减轻主库压力，提高读性能

**全局数据库（Global Database）：**
- Aurora Global Database：多区域读，一个主写区域，其他区域只读
- DynamoDB Global Tables：多区域主主写，任意区域都可以写

**缓存策略：**
- 热门商品数据缓存在 ElastiCache
- CDN 缓存商品图片
- API Gateway 缓存不常变化的列表查询

---

## 性能调优框架 · Performance Tuning Framework

无论遇到任何性能问题，都用这个框架：

### 三步法：监控 → 度量 → 修复

**1. 监控（Monitor）：**
- **日志（Logs）：** 收集所有服务的日志（CloudWatch Logs）
- **指标（Metrics）：** CPU 利用率、内存、网络、延迟（CloudWatch Metrics）
- **链路追踪（Traces）：** 一个请求经过哪些服务，每个服务耗时多少（AWS X-Ray）

**2. 度量（Measure）：**
- 定义 KPI（关键性能指标）：如 P99 延迟 < 200ms
- 设置 CloudWatch Alarm（阈值告警）

**3. 修复（Remediate）：**
- 配置修改（Configuration）：换更大实例、调整连接池、增加缓存
- 代码修改（Code）：优化查询、减少不必要的计算

---

### 实战案例1：EC2 应用性能问题

**发现问题：** CPU 使用率持续很高

**排查过程：**
1. CloudWatch 查看 CPU/内存指标
2. X-Ray 追踪哪个 API 最慢
3. CloudWatch Logs Insights 分析日志

**可能的修复：**
- 配置：使用 AWS Compute Optimizer 推荐的 EC2 实例类型
- 代码：优化 SQL 查询，添加数据库索引

---

### 实战案例2：Lambda 性能问题

**发现问题：** Lambda 出现节流（Throttling），但内存设置已经很高

**排查过程：**
1. 监控：Lambda 指标显示并发数达到上限
2. 追踪：启用 X-Ray 追踪，发现某个代码段耗时特别长
3. 发现：数据库连接代码放在 Lambda **handler 函数内部**

**根本原因：** 每次 Lambda 调用都重新建立数据库连接，这非常耗时（连接创建的开销很大）

**修复方案：** 把数据库连接代码移到 **handler 函数外部**（全局作用域）

```python
# 错误做法 - 每次调用都连接
def handler(event, context):
    conn = create_db_connection()  # 每次都重新连接！
    result = conn.query(...)
    return result

# 正确做法 - 连接复用
conn = create_db_connection()  # Lambda 容器启动时连接一次

def handler(event, context):
    result = conn.query(...)  # 复用已有连接
    return result
```

Lambda 容器是持久的（在一定时间内），连接放在全局区域可以在多次调用中复用，大大减少延迟。

---

# 总结 · Summary

这门课程涵盖了系统设计面试和实际工作中最重要的核心概念。用一句话总结每个主题：

| 主题 | 核心要点 |
|------|---------|
| 微服务 | 独立部署、独立扩展，但不是银弹 |
| 负载均衡器 | ALB = 智能路由（L7），NLB = 快速转发（L4） |
| API Gateway vs ALB | API Gateway功能多付费少，ALB长连接付费不等用 |
| 垂直扩展 | 简单但有上限且贵 |
| 水平扩展 | 无限扩展但需无状态应用 |
| 同步 vs 异步 | 两者结合，读同步写异步 |
| SQL vs NoSQL | ACID事务用SQL，高吞吐灵活结构用NoSQL |
| 缓存 | 多层缓存，TTL控制新鲜度 |
| 高可用 vs 容错 | HA允许降级，FT维持100%，后者更贵 |
| 哈希+分片 | 分布式数据存储的核心机制 |
| 灾难恢复 | RPO=数据损失，RTO=停机时长，4种策略 |
| 12因素应用 | 云原生最佳实践，无状态+配置外部化 |
| 单元化架构 | 隔离故障，限制爆炸半径 |
| 加密 | 信封加密+KMS是AWS标准做法 |
| TLS/mTLS | 传输加密，B2B用mTLS双向认证 |
| URL短链接 | 计数器范围法+ZooKeeper是最优解 |
| 电商系统 | DynamoDB(目录/购物车)+Aurora(交易)+SQS(异步) |

如果你想深入了解某个具体话题，比如想让我画一个架构图，或者模拟一道面试题，随时告诉我！
