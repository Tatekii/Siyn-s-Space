## 结构
1. Concept
	![](../../assets/Pastedimage 20241125103133.png.png)
2. Stracture
	![[Pasted image 20241125103500.png]]
3. Cluster Networking
	![[Pasted image 20241125223229.png]]
4. Ingress Controller
	![[Pasted image 20241125222923.png]]
## 与Pod通信
### Ingress
- Ingress-nginx
	- 映射endpoint到clusterIP的name

### ExternalName Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: prod
spec:
  type: ExternalName
  externalName: my.database.example.com
```
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



