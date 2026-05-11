
# EBS Dynamic Provisioning  Steps:
1. We need to install the EBS CSI drivers in EKS cluster.
   kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=release-1.32"
2. Your nodes should have access to connect with EBS volumes. Attach EBS CSI policy to the EC2 instance role.
3. someone on behalf of you should create EBC Volume in AWS and equivalent PV in K8s automatically --> dynamic provisioning that one is Storage class.

$kubectl get sc
$kubectl api-resources

$kubectl get pods
$kubectl exec -it ebs-dynamic-nginx-pod -- bash
$cd /usr/share/nginx/html/
$echo "<h1>Hello from EBS Dynamic volumes</h1>" > index.html



# Note: We usually choose ebc with dynamic volume provision because volume and servers are placed at same location.

* How to create and delete all pods?
```
kubectl apply -f namespace.yaml
```
```
kubectl apply -f expense-ebs-dynamic-sc.yaml
```

```
kubectl apply -f mysql/manifest.yaml
```
```
kubectl apply -f backend/manifest.yaml
```
```
kubectl apply -f frontend/manifest.yaml
```
```
kubectl apply -f debug/manifest.yaml
```

```
kubectl delete pods --all -n your-namespace
```

# How to test or debug mysql data using Debug pod
```
kubectl exec -it debug -n expense -- /bin/bash
```
```
mysql -h mysql -u root -pExpenseApp@1
```



```
kubectl exec -it backend -n expense -- /bin/bash
```
```
ps -ef
```
```
netstat -tlnp
```

# General Kubernetes Basics (must-know)

# List pods
```
kubectl get pods -n expense
```

# Describe pod (events, errors)
```
kubectl describe pod <pod-name> -n expense
```

# Pod logs
```
kubectl logs <pod-name> -n expense
```

# Exec into pod
```
kubectl exec -it <pod-name> -n expense -- /bin/bash
```


# MYSQL – Kubernetes Way

1️⃣ Check MySQL pod is running
```
kubectl get pods -n expense
```

2️⃣ Check MySQL container logs
```
kubectl logs mysql-pod-name -n expense
```
Look for:
ready for connections
❌ crash loops / permission errors

3️⃣ Check MySQL process inside pod
```
kubectl exec -it mysql-pod-name -n expense -- ps -ef | grep MySQL
```

4️⃣ Check MySQL port (3306) inside pod
```
kubectl exec -it mysql-pod-name -n expense -- netstat -lntp
```
# OR
```
kubectl exec -it mysql-pod-name -n expense -- ss -lntp
```

5️⃣ Check MySQL Service (IMPORTANT)
Instead of IP/hostname:
```
kubectl get svc -n expense
```

Example:
mysql   ClusterIP   10.96.10.23   <none>   3306/TCP

6️⃣ Test MySQL connection from another pod
```
kubectl exec -it backend-pod -n expense -- \
mysql -h mysql -u root -pExpenseApp@1
```

👉 Use service name (mysql), NOT DNS like db.daws78s.online

7️⃣ Check DB data imported
```
kubectl exec -it mysql-pod -n expense -- mysql -u root -pExpenseApp@1
```
```
show databases;
```
```
use transactions;
```
```
show tables;
```

# BACKEND – Kubernetes Way

1️⃣ Check backend pod
```
kubectl get pods -n expense
```

2️⃣ Check backend logs
```
kubectl logs backend-pod-name -n expense
```

3️⃣ Check backend process
```
kubectl exec -it backend-pod -n expense -- ps -ef | grep backend
```

4️⃣ Check backend port (8080)
```
kubectl exec -it backend-pod -n expense -- netstat -lntp
```

5️⃣ Check backend service
```
kubectl get svc backend -n expense
```

6️⃣ Test MySQL connectivity from backend
```
kubectl exec -it backend-pod -n expense -- telnet mysql 3306
```
OR
```
kubectl exec -it backend-pod -n expense -- nc -zv mysql 3306
```

7️⃣ Ping test (only works if ICMP allowed)
```
kubectl exec -it backend-pod -n expense -- ping -c3 MySQL
```


# FRONTEND – Kubernetes Way
1️⃣ Check frontend pod
```
kubectl get pods -n expense
```

2️⃣ Check nginx logs
```
kubectl logs frontend-pod-name -n expense
```

3️⃣ Check nginx process
```
kubectl exec -it frontend-pod -n expense -- ps -ef | grep nginx
```

4️⃣ Check port 80
```
kubectl exec -it frontend-pod -n expense -- netstat -lntp
```

5️⃣ Check frontend service
```
kubectl get svc frontend -n expense
```
If exposed externally:
```
kubectl get svc frontend -n expense -o wide
```

6️⃣ Test backend connectivity from frontend
```
kubectl exec -it frontend-pod -n expense -- telnet backend 8080
```



