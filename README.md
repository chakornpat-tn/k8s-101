# Kubernetes 101 - Todo Application

ตัวอย่างแอพพลิเคชัน Todo List ที่ deploy บน Kubernetes พร้อมการจัดการ **Auto Scaling**, **Load Balancing**, และ **Ingress** สำหรับการเรียนรู้ Kubernetes เบื้องต้น

## 🏗️ Architecture

```
Internet → Ingress Controller → Services → Pods
                                   ↓
                               Database (MongoDB)
```

### Components:

- **Frontend**: React Todo App (`dekcode/todo-app:v4`)
- **Backend**: Node.js API (`dekcode/todo-api:v5`)
- **Database**: MongoDB with Persistent Storage
- **Ingress**: NGINX Ingress Controller
- **Auto Scaling**: Horizontal Pod Autoscaler (HPA)

## 📁 Files Overview

| File                    | Description                                |
| ----------------------- | ------------------------------------------ |
| `client-deployment.yml` | Frontend React app (2 replicas)            |
| `server-deployment.yml` | Backend API + Service + HPA (1-3 replicas) |
| `db-deployment.yml`     | MongoDB database + Service                 |
| `mongo-pvc.yml`         | Persistent Volume Claim for MongoDB        |
| `ingress.yml`           | Ingress routing configuration              |
| `sample-pod.yml`        | Example Pod for testing                    |

## 🚀 Quick Start

### Prerequisites

- Kubernetes cluster (local/cloud)
- kubectl configured
- NGINX Ingress Controller

### 1. Install NGINX Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.13.3/deploy/static/provider/cloud/deploy.yaml
```

### 2. Deploy the Application

```bash
# Deploy database with persistent storage
kubectl apply -f mongo-pvc.yml
kubectl apply -f db-deployment.yml

# Deploy backend API with auto-scaling
kubectl apply -f server-deployment.yml

# Deploy frontend
kubectl apply -f client-deployment.yml

# Setup ingress routing
kubectl apply -f ingress.yml
```

### 3. Access the Application

Add to your `/etc/hosts` (Linux/Mac) or `C:\Windows\System32\drivers\etc\hosts` (Windows):

```
127.0.0.1 todo.com
```

**Frontend**: http://todo.com  
**API**: http://todo.com/api/

## 📊 Auto Scaling Features

Backend API มีการตั้งค่า **Horizontal Pod Autoscaler (HPA)**:

- **Min Replicas**: 1 Pod
- **Max Replicas**: 3 Pods
- **Scaling Triggers**:
  - CPU > 70%
  - Memory > 80%

### Monitor Auto Scaling

```bash
# ดูสถานะ HPA
kubectl get hpa

# ดูแบบ real-time
kubectl get hpa -w

# ดู details
kubectl describe hpa server-hpa
```

## 🔍 Useful Commands

### Check Application Status

```bash
# ดู Pods ทั้งหมด
kubectl get pods

# ดู Services
kubectl get services

# ดู Deployments
kubectl get deployments

# ดู Ingress
kubectl get ingress
```

### Debug Commands

```bash
# ดู logs ของ Pod
kubectl logs <pod-name>

# เข้าไปใน Pod
kubectl exec -it <pod-name> -- /bin/bash

# ดู events
kubectl get events --sort-by=.metadata.creationTimestamp
```

### Load Testing (ทดสอบ Auto Scaling)

```bash
# สร้าง load เพื่อทดสอบ HPA
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh

# ใน Pod, run:
while true; do wget -q -O- http://server-service/api/health; done
```

## 🔧 Configuration Details

### Resource Limits

- **Frontend**: 128Mi-256Mi RAM, 250m-500m CPU
- **Backend**: 128Mi-256Mi RAM, 250m-500m CPU
- **Database**: 256Mi-512Mi RAM, 500m-1000m CPU

### Storage

- **MongoDB**: 1Gi Persistent Volume

### Networking

- **Frontend Service**: ClusterIP (Port 80)
- **Backend Service**: ClusterIP (Port 80)
- **Database Service**: ClusterIP (Port 27017)

## 🧹 Cleanup

ลบ resources ทั้งหมด:

```bash
kubectl delete -f .
```

## 📚 Learning Objectives

หลังจากใช้โปรเจคนี้ คุณจะเข้าใจ:

- ✅ **Deployments & ReplicaSets**
- ✅ **Services & Load Balancing**
- ✅ **Persistent Volumes & Claims**
- ✅ **Ingress & Routing**
- ✅ **Horizontal Pod Autoscaler (HPA)**
- ✅ **Resource Management**
- ✅ **Multi-tier Application Deployment**

## 🆘 Troubleshooting

### Common Issues:

**Pods ไม่ start:**

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

**Ingress ไม่ทำงาน:**

```bash
kubectl get ingress
kubectl describe ingress todo-ingress-service
```

**HPA ไม่ scale:**

```bash
kubectl top nodes
kubectl top pods
kubectl describe hpa server-hpa
```

---

## 🤝 Contributing

Fork → Make Changes → Create Pull Request

## 📄 License

MIT License - ใช้เพื่อการศึกษาได้อย่างอิสระ
