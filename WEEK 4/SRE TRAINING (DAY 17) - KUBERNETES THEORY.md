# Kubernetes Notes

## Kubernetes Architecture
Kubernetes follows a master-worker architecture where the control plane manages worker nodes that run containerized applications.

### 1. **Control Plane Components**
- **API Server (kube-apiserver)**: Exposes Kubernetes API, the main entry point for all cluster operations.
- **Controller Manager**: Ensures the desired state of the cluster by managing controllers (e.g., node, job, deployment controllers).
- **Scheduler**: Assigns pods to nodes based on resource availability and constraints.
- **etcd**: A distributed key-value store that holds cluster state and configuration data.

### 2. **Worker Node Components**
- **Kubelet**: Agent running on each node, responsible for ensuring containers are running.
- **Container Runtime**: Software that runs containers (e.g., Docker, containerd, CRI-O).
- **Kube Proxy**: Manages networking, forwarding requests inside and outside the cluster.

## Important Kubernetes Concepts

### 1. **kubectl**
Command-line tool to interact with Kubernetes clusters.

Common commands:
```sh
kubectl get pods
kubectl apply -f deployment.yaml
kubectl delete service my-service
```

### 2. **Namespaces**
Logical separation within a cluster to isolate resources.

```sh
kubectl create namespace dev
kubectl get namespaces
```

### 3. **Deployments**
Manages the deployment of applications, ensuring desired replicas are maintained.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx
```

Apply deployment:
```sh
kubectl apply -f deployment.yaml
```

### 4. **ConfigMaps**
Stores non-sensitive configuration data.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
  namespace: default
data:
  app.properties: |
    log_level=INFO
    db_host=localhost
```

### 5. **Secrets**
Stores sensitive data such as passwords, API keys.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  password: cGFzc3dvcmQ=
```

### 6. **Services**
Exposes a set of pods as a network service.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

### 7. **Pods**
The smallest deployable unit in Kubernetes, encapsulating one or more containers.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
```

### 8. **Ingress**
Manages external access to services via HTTP/HTTPS.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
    - host: my-app.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```

Apply Ingress:
```sh
kubectl apply -f ingress.yaml
```

