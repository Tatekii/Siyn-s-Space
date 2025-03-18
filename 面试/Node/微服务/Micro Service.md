按照`feature`拆分整个应用，每个微服务服务特定的`feature`，相互独立，避免全盘崩。
## Key Rule
1. （必要的话）每个`service`拥有单独的`database`【database-per-service pattern】
2. `service`之间不能互相访问`database`

## 微服务架构的数据管理 
单独拆分的数据库之间在业务上必然会有关联。
### service之间的通信
- `Sync`
	- service之间直接通信
	- ![](../../../assets/Pasted%20image%2020241115111733.png)
- `Async`
	- service之间通过事件`events`通信
	- ![](../../../assets/Pasted%20image%2020241115140813.png)
