# Kubernetes Lab - Recursos Básicos

## 🔍 Comandos básicos para explorar el clúster

```bash
# Ver la versión del cliente y del servidor
kubectl version --short

# Ver el estado general del clúster
kubectl cluster-info

# Ver los nodos del clúster
kubectl get nodes

# Ver detalles de un nodo
kubectl describe node <nombre-del-nodo>

# Ver todos los recursos en todos los namespaces
kubectl get all --all-namespaces

# Ver los namespaces
kubectl get namespaces

# Cambiar de namespace
kubectl config set-context --current --namespace=<nombre-del-namespace>

# Ver los contextos disponibles y el activo
kubectl config get-contexts
kubectl config current-context
```

# Kubernetes Lab - Recursos Básicos

Este laboratorio te guía para desplegar recursos básicos de Kubernetes usando tanto comandos imperativos como manifiestos declarativos. Incluye:

- ConfigMap
- Pod
- ReplicaSet
- Deployment
- Service

---

## 1. ConfigMap

### Imperativo
```bash
kubectl create configmap mi-configmap --from-literal=saludo=hola
```

### Declarativo (`configmap.yaml`)
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mi-configmap
data:
  saludo: hola
```

### Validación
```bash
kubectl get configmaps
kubectl describe configmap mi-configmap
kubectl get configmap mi-configmap -o yaml
```

---

## 2. Pod

### Imperativo
```bash
kubectl run mi-pod --image=nginx --restart=Never --env="saludo=hola"
```

### Declarativo (`pod.yaml`)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mi-pod
spec:
  containers:
    - name: nginx
      image: nginx
      env:
        - name: saludo
          valueFrom:
            configMapKeyRef:
              name: mi-configmap
              key: saludo
```

### Validación
```bash
kubectl get pods
kubectl describe pod mi-pod
kubectl logs mi-pod
kubectl exec mi-pod -- env | grep saludo
```

---

## 3. ReplicaSet

### Declarativo (`replicaset.yaml`)
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: mi-replicaset
spec:
  replicas: 2
  selector:
    matchLabels:
      app: mi-app
  template:
    metadata:
      labels:
        app: mi-app
    spec:
      containers:
        - name: nginx
          image: nginx
```

### Validación
```bash
kubectl apply -f replicaset.yaml
kubectl get replicasets
kubectl describe replicaset mi-replicaset
kubectl get pods -l app=mi-app
```

---

## 4. Deployment

### Imperativo
```bash
kubectl create deployment mi-deployment --image=nginx
kubectl scale deployment mi-deployment --replicas=3
```

### Declarativo (`deployment.yaml`)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mi-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mi-app
  template:
    metadata:
      labels:
        app: mi-app
    spec:
      containers:
        - name: nginx
          image: nginx
```

### Validación
```bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl describe deployment mi-deployment
kubectl get pods -l app=mi-app
kubectl rollout status deployment mi-deployment
```

---

## 5. Service (NodePort)

### Imperativo
```bash
kubectl expose deployment mi-deployment --port=80 --target-port=80 --type=NodePort
```

### Declarativo (`service.yaml`)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mi-servicio
spec:
  selector:
    app: mi-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

### Validación
```bash
kubectl apply -f service.yaml
kubectl get services
kubectl describe service mi-servicio
```

---

## Limpieza de Recursos
```bash
kubectl delete configmap mi-configmap
kubectl delete pod mi-pod
kubectl delete replicaset mi-replicaset
kubectl delete deployment mi-deployment
kubectl delete service mi-servicio
```

---

## Estructura de Archivos
```
k8s-lab/
├── configmap.yaml
├── pod.yaml
├── replicaset.yaml
├── deployment.yaml
└── service.yaml
```

Puedes aplicar todos los manifiestos con:
```bash
kubectl apply -f .
```
