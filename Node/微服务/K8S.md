## 结构
1. Concept
	![[Pasted image 20241125103133.png]]
2. Stracture
	![[Pasted image 20241125103500.png]]
2. Cluster Networking
	![[Pasted image 20241125223229.png]]
3. Ingress Controller
	![[Pasted image 20241125222923.png]]
## 与Pod通信
### Ingress
- Ingress-nginx
	- 映射endpoint到clusterIP的name

### cluster内
ClusterService
- 同namespace
	- `/SERVICE`
- 不同namespace
	- 访问ingress-nginx
		- `/SERVICE.NAMESPACE.svc.local`
		- `/ingress-nginx-controller.ingress-nginx.svc.local`

### cluster外
NodeService

