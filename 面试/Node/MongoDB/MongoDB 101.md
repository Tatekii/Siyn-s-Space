## MongoDB 基础概念

### **文档（Document）**
MongoDB 的核心数据单元，相当于关系型数据库中的“行”。文档是 `BSON（Binary JSON）`格式的键值对集合，具有高度灵活性，可以包含嵌套的文档和数组。
    
```js
{ name: "Alice", age: 30, city: "New York", interests: ["coding", "hiking"] }
```
        
### **集合（Collection）**
相当于关系型数据库中的“表”。集合是文档的组，可以包含多个文档。与关系型数据库不同，集合中的文档不强制要求拥有相同的结构（这体现了其**无模式（Schema-less）**的特性）。
    
### **数据库（Database）**
一个 MongoDB 实例可以包含多个数据库，每个数据库包含独立的集合。
    
### **\_id 字段**
每个 MongoDB 文档在插入时都会自动生成一个唯一的 `_id` 字段，作为其主键。如果插入文档时没有指定 `_id`，MongoDB 会自动生成一个 `ObjectId` 类型的值。
    
### **内嵌文档与数组**
MongoDB 允许在文档中嵌入其他文档或数组，这使得复杂的数据结构可以存储在一个独立的文档中，减少了传统关系型数据库中多表 JOIN 的需求。
    
```json
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

## MongoDB Shell 操作
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


## 索引（Indexes）

索引对于 MongoDB 的查询性能至关重要，与关系型数据库中的索引概念类似。

### **创建索引**
```js
db.collection.createIndex({ field_name: 1 })` (1 表示升序，-1 表示降序)。
```
    
### **复合索引**
```js
db.collection.createIndex({ field1: 1, field2: -1 })
```

    
### **唯一索引**
```js
db.collection.createIndex({ email: 1 }, { unique: true })`，确保 `email` 字段的值唯一。
```
    
### **稀疏索引（Sparse Indexes）**
只为存在索引字段的文档创建索引。
```js
db.users.createIndex( { email: 1 }, { unique: true, sparse: true } )
```
在这个例子中：
- 如果一个文档是 `{ _id: 1, name: "Alice", email: "alice@example.com" }`，它会包含在索引中。
- 如果一个文档是 `{ _id: 2, name: "Bob", email: "bob@example.com" }`，它会包含在索引中。
- 如果一个文档是 `{ _id: 3, name: "Charlie" }`（没有 `email` 字段），它**不会**包含在索引中。
- 现在，你可以插入两个 `email` 字段都为空或不存在的文档，而不会违反唯一性约束，例如：
    - `db.users.insertOne({ name: "David" })`
    - `db.users.insertOne({ name: "Eve" })`
    - 但你不能插入两个 `email` 都是 `"frank@example.com"` 的文档。

### **TTL 索引（Time-To-Live Indexes）**
**TTL（Time-To-Live）索引**是一种特殊的单字段索引，MongoDB 利用它来**自动删除**超过一定“寿命”的文档。这在处理时效性数据（如日志、会话、缓存）时非常有用。

TTL 索引基于日期类型的字段，MongoDB 会定期检查该字段的值，并删除那些值早于当前时间加上一个指定存活时间的文档。

示例：
```js
db.log_entries.createIndex( { "createdAt": 1 }, { expireAfterSeconds: 86400 } // 24小时 = 86400秒 )
```
在这个例子中：

- `createdAt` 必须是一个 **日期类型**（Date）的字段。
- 当一个文档插入后，MongoDB 会查看 `createdAt` 字段的值。
- 在 `createdAt` 值的基础上加上 86400 秒后，如果当前时间超过了这个计算出来的时间点，该文档就会被 MongoDB 的后台 TTL 清理进程自动删除。
    
#### TTL 索引的工作机制

- **后台进程**：MongoDB 有一个专门的后台进程会定期（默认为 60 秒一次）运行，扫描包含 TTL 索引的集合。
    
- **删除策略**：当进程发现某个文档的过期时间已到，就会将其从集合中删除。删除操作类似于常规的删除，但由数据库自动触发。
    
- **单字段限制**：TTL 索引只能定义在**单个字段**上。
    
- **复合索引不能是 TTL 索引**：你不能在一个复合索引上指定 `expireAfterSeconds`。
    
- **字段类型**：被索引的字段必须是 **BSON 日期类型**。如果该字段是数组，MongoDB 会使用数组中最小的（最老的）日期来计算过期时间。如果字段不存在或不是日期类型，文档不会被删除。

#### 注意事项：

- TTL 索引只删除**整个文档**，不能删除文档中的某个字段。
    
- 删除操作是异步的，所以文档在过期后可能不会立即被删除。这通常是分钟级到秒级的延迟。
    
- TTL 索引不能用于复制集的主节点之外的节点，从节点会复制主节点的删除操作。

### ⏱️**分析查询**
使用 `db.collection.explain().find(...)` 可以查看查询的执行计划，了解是否使用了索引以及查询效率。
    
## 聚合框架（Aggregation Framework）

MongoDB 的聚合框架是一个强大的工具，用于对文档进行数据处理和分析，类似于 SQL 中的 `GROUP BY`、`JOIN`、`SUM` 等操作。它通过管道（Pipeline）的形式工作，每个阶段对输入文档执行特定的操作，然后将结果传递给下一个阶段。

### **常用管道阶段**：

#### `$match`
过滤文档（相当于 `WHERE`）。
```js
db.orders.aggregate([ { $match: { status: "completed", amount: { $gt: 100 } } } ])
```
####  `$project`
选择、重命名或添加/删除字段（相当于SELECT）。
```js
db.users.aggregate([ { $project: { _id: 0, 
// 不显示 _id 字段 
fullName: { $concat: ["$firstName", " ", "$lastName"] }, // 合并字段 email: 1 // 显示 email 字段 } } ])
```

#### `$group`
对文档进行分组，并执行聚合计算（如 `$sum`, `$avg`, `$min`, `$max`）。
**类比 SQL**：`GROUP BY` 子句，以及 `SUM()`, `COUNT()`, `AVG()` 等聚合函数。
```js
db.products.aggregate([ 
	{ $group: { 
		_id: "$category", // 按 category 字段分组 
		totalProducts: { $sum: 1 }, // 计算每个分类的产品数
		averagePrice: { $avg: "$price" } // 计算每个分类的平均价格 
		} 
	} 
])
```

#### `$sort`
对结果进行排序。
**类比 SQL**：`ORDER BY` 子句。
```js
db.employees.aggregate([ { $sort: { salary: -1, name: 1 } } // 先按薪水降序，再按姓名升序 ])
```

#### `$limit`
限制返回的文档数量。
        
#### `$skip`
跳过指定数量的文档。

#### `$unwind`
将文档中的数组字段“解构”，为数组中的每个元素生成一个新文档。
```js
// 原始文档: 
{ _id: 1, name: "Product A", tags: ["电子", "家居"] } 

db.products.aggregate([ { $unwind: "$tags" } ]) 
// 结果示例: 
{ _id: 1, name: "Product A", tags: "电子" } 
{ _id: 1, name: "Product A", tags: "家居" }
```
#### `$lookup`
执行左外连接（left outer join）操作，将来自另一个集合的文档添加到结果中（MongoDB 4.0 引入，部分弥补了无 JOIN 的劣势）。
```js
// 假设有 orders 集合和 products 集合 
db.orders.aggregate([ 
	{ $lookup: { 
		from: "products", // 要连接的集合 
		localField: "productId", // orders 集合中的字段 
		foreignField: "_id", // products 集合中的字段 
		as: "productInfo" // 结果中添加的新字段名，包含匹配的 products 文档} 
	} 
])
```
### **示例**：计算每个用户的订单总金额
    
```js
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

### 性能考虑
- **尽早过滤**：将 `$match` 阶段放在聚合管道的前面，可以减少后续阶段处理的文档数量。

- **索引利用[`$match`](#`$match`) 和 [`$sort`](#`$sort`) 阶段可以利用索引来加速操作。
    
- **内存限制**：聚合操作在处理大量数据时可能会消耗大量内存。如果操作超出内存限制（通常是 100MB），MongoDB 会报错。你可以通过设置 `allowDiskUse: true` 选项来允许聚合操作使用磁盘进行处理，但性能会下降。
    
- **避免全表扫描**：设计聚合管道时尽量利用索引。
## 💿复制集（Replica Sets）
**复制集（Replica Set）**是 MongoDB 提高数据**高可用性（High Availability）**和**数据冗余（Data Redundancy）**的核心机制。它由一组 MongoDB 实例组成，这些实例维护着相同的数据集，确保即使某个实例发生故障，数据服务也能持续可用。
### **主节点（Primary）**
- **写入操作的唯一入口：** 复制集中所有的**写入操作**（插入、更新、删除）都必须通过主节点进行。
    
- **读取操作的默认目标：** 默认情况下，读取操作也会发送到主节点，但客户端也可以配置将读取分发到从节点。
    
- **日志记录：** 主节点会将所有的写入操作记录到一个特殊的日志文件——**操作日志（Oplog）**中。这个 Oplog 是从节点同步数据的基础。
    
- **高可用性的关键：** 当主节点发生故障时，复制集中的其他成员会通过选举（Election）过程，推选出一个新的主节点，从而实现自动故障转移（Automatic Failover）。
    
### **从节点（Secondary）**
- **数据复制者：** 从节点通过异步地读取主节点的 Oplog，并将其应用到自己的数据集中，从而保持与主节点的数据同步。
    
- **读操作的潜在目标：** 从节点可以配置为处理读取操作，从而分担主节点的读取压力，实现**读写分离**。
    
- **参与选举：** 当主节点下线时，从节点会参与选举新的主节点。
    
- **数据冗余：** 它们提供了数据副本，即使主节点数据丢失，从节点也能提供数据备份。
### **仲裁节点（Arbiter）**

- **不存储数据：** 仲裁节点是一个特殊的复制集成员，它**不存储数据副本**，因此不需要大量的磁盘空间。
    
- **只参与选举：** 仲裁节点的主要作用是参与主节点的选举过程。当复制集成员数量为偶数时，或者需要一个轻量级节点来帮助达到多数派投票条件时，可以使用仲裁节点。
    
- **避免脑裂：** 在网络分区或成员数量为偶数的情况下，仲裁节点有助于复制集成员形成明确的多数派，从而避免“脑裂”（Split-Brain）问题——即多个主节点同时存在的情况。
    
- **推荐用于非数据节点：** 通常不建议在包含偶数数据节点的复制集中使用仲裁节点，而是建议拥有奇数个数据节点。
### **选举机制**
当主节点出现故障时，复制集中的从节点会通过选举选出一个新的主节点，从而保证服务的高可用性。
    
### **读写分离**
可以将读请求分发到从节点，减轻主节点压力，提高读取性能。
1. z
    
### **搭建**
通常需要至少 3 个节点（一个主节点，两个从节点）来实现多数派选举，保证数据一致性。
## 分片（Sharding）

分片是 MongoDB 实现水平扩展（Scale Out）的方式，用于处理大数据量和高并发场景。

### **分片键（Shard Key）**
决定数据如何分布到不同分片上的字段。选择一个好的分片键至关重要，它会影响数据的分布均匀性和查询效率。
    
### **分片集群组件**：
    
####  **分片（Shard）**
存储数据，每个分片通常是一个复制集。
        
####  **mongos（查询路由器）**
应用程序通过 `mongos` 连接到分片集群，`mongos` 负责将请求路由到正确的碎片。
        
#### **Config Server（配置服务器）**
存储集群的元数据（分片信息、数据分布情况）。
        
#### **工作原理**
当数据写入时，`mongos` 会根据分片键将数据路由到对应的分片。查询时，`mongos` 也会根据分片键将查询路由到包含数据的分片，或将查询广播到所有相关分片并聚合结果。

## 数据模型设计

这是使用 MongoDB 最需要投入精力的地方。虽然 MongoDB 是无模式的，但良好的数据模型设计仍然至关重要。

### **内嵌（Embedding）与引用（Referencing）**
    
#### **内嵌**
将相关数据存储在同一个文档中（例如，订单文档中内嵌订单项）。适用于一对一或一对少量的关系，可以减少 JOIN 操作，提高读取性能。
        
#### **引用**
通过 `_id` 字段引用另一个集合的文档（类似于关系型数据库的外键）。适用于一对多（多对多）的关系，当被引用的数据需要独立更新或非常大时。
        
### **避免巨型文档**
虽然 MongoDB 允许内嵌，但单个文档的大小有限制（当前为 16MB）。同时，过大的文档在更新时可能效率低下。
    
### **预聚合与冗余**
为了优化特定查询，可以考虑在文档中存储一些冗余或预聚合的数据，以避免复杂的聚合操作。

## 安全性

### **启用认证**
为 MongoDB 实例配置用户和角色，要求客户端在连接时进行认证。
    
### **最小权限原则**
为用户授予完成其工作所需的最小权限。
    
### **网络访问控制**
限制哪些 IP 地址可以连接到 MongoDB 实例。
    
### **数据加密**
考虑传输中的数据加密（TLS/SSL）和静态数据加密。

## 备份与恢复
了解如何备份和恢复 MongoDB 数据至关重要，以防数据丢失或损坏。

-  **mongodump/mongorestore**
	官方提供的命令行工具，用于备份和恢复整个数据库或集合。
- **文件系统快照**
	对数据库目录进行快照备份。
-  **MongoDB Cloud Manager / Ops Manager**
	提供更全面的自动化备份和恢复解决方案。