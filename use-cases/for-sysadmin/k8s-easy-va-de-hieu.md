# K8s easy và dễ hiểu

&#x20;

#### 🧭 **Sơ đồ minh họa Kubernetes đơn giản** <a href="#compass-so-do-minh-hoa-kubernetes-don-gian" id="compass-so-do-minh-hoa-kubernetes-don-gian"></a>

```
+------------------------------+
|        Kubernetes Cluster    |
|                              |
|  +--------+   +-----------+  |
|  | Node 1 |   |  Node 2   |  |
|  +--------+   +-----------+  |
|     |               |        |
|  +-------+      +-------+    |
|  |  Pod  |      |  Pod  |    |
|  | nginx |      | redis |    |
|  +-------+      +-------+    |
|                              |
+------------------------------+
```

***

#### 📄 **YAML chạy 1 Pod đơn giản (nginx)** <a href="#page_facing_up-yaml-chay-1-pod-don-gian-nginx" id="page_facing_up-yaml-chay-1-pod-don-gian-nginx"></a>

Tạo file tên `nginx-pod.yaml` với nội dung:

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-demo
spec:
  containers:
    - name: nginx
      image: nginx:1.25
      ports:
        - containerPort: 80
```

***

#### 🚀 **Chạy thử với Minikube** <a href="#rocket-chay-thu-voi-minikube" id="rocket-chay-thu-voi-minikube"></a>

```
# 1. Khởi động Minikube
minikube start

# 2. Áp dụng file YAML
kubectl apply -f nginx-pod.yaml

# 3. Kiểm tra pod đã chạy chưa
kubectl get pods

# 4. Xem chi tiết pod
kubectl describe pod nginx-demo

# 5. Truy cập vào pod
kubectl exec -it nginx-demo -- /bin/bash
```

```
# 1. Khởi động Minikube
minikube start --driver=docker

# 2. Check node 
kubectl get nodes

# 3. Check pods
kubectl get pods -A

# 4. Áp dụng file YAML
kubectl apply -f nginx-pod.yaml

# 5. Kiểm tra pod đã chạy chưa
kubectl get pods

# 6. Xem chi tiết pod
kubectl describe pod nginx-demo

# 7. Truy cập vào pod
kubectl exec -it nginx-demo -- /bin/bash
```

***

#### 🌐 **Truy cập nginx từ máy bạn** <a href="#globe_with_meridians-truy-cap-nginx-tu-may-ban" id="globe_with_meridians-truy-cap-nginx-tu-may-ban"></a>

Expose port tạm thời để duyệt trình duyệt:

```
kubectl port-forward pod/nginx-demo 8080:80
```

```
kubectl port-forward pod/nginx-demo 8080:80
```

→ Truy cập tại: [http://localhost:8080](http://localhost:8080)
