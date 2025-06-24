## MongoDB 基础概念

### **文档（Document）**：
MongoDB 的核心数据单元，相当于关系型数据库中的“行”。文档是 `BSON（Binary JSON）`格式的键值对集合，具有高度灵活性，可以包含嵌套的文档和数组。
    
```js
{ name: "Alice", age: 30, city: "New York", interests: ["coding", "hiking"] }
```
        
- **集合（Collection）**：相当于关系型数据库中的“表”。集合是文档的组，可以包含多个文档。与关系型数据库不同，集合中的文档不强制要求拥有相同的结构（这体现了其**无模式（Schema-less）**的特性）。
    
- **数据库（Database）**：一个 MongoDB 实例可以包含多个数据库，每个数据库包含独立的集合。
    
- **_id 字段**：每个 MongoDB 文档在插入时都会自动生成一个唯一的 `_id` 字段，作为其主键。如果插入文档时没有指定 `_id`，MongoDB 会自动生成一个 `ObjectId` 类型的值。
    
- **内嵌文档与数组**：MongoDB 允许在文档中嵌入其他文档或数组，这使得复杂的数据结构可以存储在一个独立的文档中，减少了传统关系型数据库中多表 JOIN 的需求。
    
    - **例子**：
        
        JSON
        
        ```
        {
          "order_id": 123,
          "customer": { "name": "Bob", "email": "bob@example.com" },
          "items": [
            { "product_id": 1, "name": "Laptop", "qty": 1, "price": 1200 },
            { "product_id": 2, "name": "Mouse", "qty": 1, "price": 25 }
          ],
          "total_amount": 1225
        }
        ```
        

---

## 2. MongoDB Shell 操作

掌握 MongoDB Shell 是与数据库交互的基础。

- **连接数据库**：
    
    - `mongo`：连接到默认的 `test` 数据库。
        
    - `mongo --port 27018`：连接到指定端口。
        
- **显示数据库**：`show dbs`
    
- **切换/创建数据库**：`use mydatabase` (如果数据库不存在，会自动创建)
    
- **显示集合**：`show collections`
    
- **插入文档**：
    
    - `db.collection.insertOne({ name: "Doc A" })`
        
    - `db.collection.insertMany([{ name: "Doc B" }, { name: "Doc C" }])`
        
- **查询文档**：
    
    - `db.collection.find()`：查询所有文档。
        
    - `db.collection.find({ name: "Doc A" })`：按条件查询。
        
    - `db.collection.findOne({ age: { $gt: 25 } })`：查询单个文档，`$gt` 是大于操作符。
        
    - **投影**：`db.collection.find({}, { name: 1, age: 1, _id: 0 })`：只返回 `name` 和 `age` 字段，不返回 `_id`。
        
- **更新文档**：
    
    - `db.collection.updateOne({ name: "Doc A" }, { $set: { age: 30 } })`：更新匹配到的第一个文档。
        
    - `db.collection.updateMany({ age: { $lt: 30 } }, { $inc: { age: 1 } })`：更新所有匹配的文档，`$inc` 是递增操作符。
        
- **删除文档**：
    
    - `db.collection.deleteOne({ name: "Doc A" })`
        
    - `db.collection.deleteMany({ age: { $gt: 50 } })`
        
- **删除集合**：`db.collection.drop()`
    
- **删除数据库**：`db.dropDatabase()`
    

---

## 3. 索引（Indexes）

索引对于 MongoDB 的查询性能至关重要，与关系型数据库中的索引概念类似。

- **创建索引**：`db.collection.createIndex({ field_name: 1 })` (1 表示升序，-1 表示降序)。
    
- **复合索引**：`db.collection.createIndex({ field1: 1, field2: -1 })`。
    
- **唯一索引**：`db.collection.createIndex({ email: 1 }, { unique: true })`，确保 `email` 字段的值唯一。
    
- **稀疏索引（Sparse Indexes）**：只为存在索引字段的文档创建索引。
    
- **TTL 索引（Time-To-Live Indexes）**：用于自动删除过期文档，常用于日志或会话数据。
    
- **分析查询**：使用 `db.collection.explain().find(...)` 可以查看查询的执行计划，了解是否使用了索引以及查询效率。
    

---

## 4. 聚合框架（Aggregation Framework）

MongoDB 的聚合框架是一个强大的工具，用于对文档进行数据处理和分析，类似于 SQL 中的 `GROUP BY`、`JOIN`、`SUM` 等操作。它通过**管道（Pipeline）**的形式工作，每个阶段对输入文档执行特定的操作，然后将结果传递给下一个阶段。

- **常用管道阶段**：
    
    - `$match`：过滤文档（相当于 `WHERE`）。
        
    - `$project`：选择、重命名或添加/删除字段。
        
    - `$group`：对文档进行分组，并执行聚合计算（如 `$sum`, `$avg`, `$min`, `$max`）。
        
    - `$sort`：对结果进行排序。
        
    - `$limit`：限制返回的文档数量。
        
    - `$skip`：跳过指定数量的文档。
        
    - `$lookup`：执行左外连接，将来自另一个集合的文档添加到结果中（MongoDB 4.0 引入，部分弥补了无 JOIN 的劣势）。
        
- **示例**：计算每个用户的订单总金额
    
    JavaScript
    
    ```
    db.orders.aggregate([
      { $match: { status: "completed" } }, // 过滤已完成订单
      { $group: {
          _id: "$customer_id", // 按 customer_id 分组
          total_spent: { $sum: "$total_amount" }, // 计算总金额
          order_count: { $sum: 1 } // 计算订单数量
      }},
      { $sort: { total_spent: -1 } }, // 按总金额降序排序
      { $limit: 10 } // 只返回前10名
    ])
    ```
    

---

## 5. 复制集（Replica Sets）

复制集是 MongoDB 提供高可用性和数据冗余的基础。

- **主节点（Primary）**：负责所有的写入操作。
    
- **从节点（Secondary）**：复制主节点的数据，可以处理读取操作，并在主节点故障时进行选举，晋升为新的主节点。
    
- **选举机制**：当主节点出现故障时，复制集中的从节点会通过选举选出一个新的主节点，从而保证服务的高可用性。
    
- **读写分离**：可以将读请求分发到从节点，减轻主节点压力，提高读取性能。
    
- **搭建**：通常需要至少 3 个节点（一个主节点，两个从节点）来实现多数派选举，保证数据一致性。
    

---

## 6. 分片（Sharding）

分片是 MongoDB 实现水平扩展（Scale Out）的方式，用于处理大数据量和高并发场景。

- **分片键（Shard Key）**：决定数据如何分布到不同分片上的字段。选择一个好的分片键至关重要，它会影响数据的分布均匀性和查询效率。
    
- **分片集群组件**：
    
    - **分片（Shard）**：存储数据，每个分片通常是一个复制集。
        
    - **mongos（查询路由器）**：应用程序通过 `mongos` 连接到分片集群，`mongos` 负责将请求路由到正确的碎片。
        
    - **Config Server（配置服务器）**：存储集群的元数据（分片信息、数据分布情况）。
        
- **工作原理**：当数据写入时，`mongos` 会根据分片键将数据路由到对应的分片。查询时，`mongos` 也会根据分片键将查询路由到包含数据的分片，或将查询广播到所有相关分片并聚合结果。
    

---

## 7. 数据模型设计

这是使用 MongoDB 最需要投入精力的地方。虽然 MongoDB 是无模式的，但良好的数据模型设计仍然至关重要。

- **内嵌（Embedding）与引用（Referencing）**：
    
    - **内嵌**：将相关数据存储在同一个文档中（例如，订单文档中内嵌订单项）。适用于一对一或一对少量的关系，可以减少 JOIN 操作，提高读取性能。
        
    - **引用**：通过 `_id` 字段引用另一个集合的文档（类似于关系型数据库的外键）。适用于一对多（多对多）的关系，当被引用的数据需要独立更新或非常大时。
        
- **避免巨型文档**：虽然 MongoDB 允许内嵌，但单个文档的大小有限制（当前为 16MB）。同时，过大的文档在更新时可能效率低下。
    
- **预聚合与冗余**：为了优化特定查询，可以考虑在文档中存储一些冗余或预聚合的数据，以避免复杂的聚合操作。
    

---

## 8. 安全性

- **启用认证**：为 MongoDB 实例配置用户和角色，要求客户端在连接时进行认证。
    
- **最小权限原则**：为用户授予完成其工作所需的最小权限。
    
- **网络访问控制**：限制哪些 IP 地址可以连接到 MongoDB 实例。
    
- **数据加密**：考虑传输中的数据加密（TLS/SSL）和静态数据加密。
    

---

## 9. 备份与恢复

了解如何备份和恢复 MongoDB 数据至关重要，以防数据丢失或损坏。

- **mongodump/mongorestore**：官方提供的命令行工具，用于备份和恢复整个数据库或集合。
    
- **文件系统快照**：对数据库目录进行快照备份。
    
- **MongoDB Cloud Manager / Ops Manager**：提供更全面的自动化备份和恢复解决方案。