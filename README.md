# Kubernetes - Apuntes y Laboratorio

## ¿Qué es Kubernetes?

Kubernetes (K8s) es una plataforma de orquestación de contenedores que permite desplegar, administrar, escalar y mantener aplicaciones distribuidas.

Mientras Docker ejecuta contenedores, Kubernetes administra grandes cantidades de contenedores distribuidos en múltiples máquinas.

---

# Arquitectura General

```text
                    Control Plane
+------------------------------------------------+
| API Server | Scheduler | Controller Manager    |
+------------------------------------------------+
                      |
                      |
      ---------------------------------------
      |                  |                 |
      v                  v                 v

+-------------+  +-------------+  +-------------+
| Worker 1    |  | Worker 2    |  | Worker 3    |
|             |  |             |  |             |
| Pods        |  | Pods        |  | Pods        |
+-------------+  +-------------+  +-------------+
```

---

# Componentes del Control Plane

## API Server

Es el punto de entrada de Kubernetes.

Todas las acciones realizadas mediante `kubectl` pasan primero por el API Server.

Ejemplo:

```bash
kubectl apply -f deployment.yaml
```

Funciones:

* Recibir solicitudes.
* Validar configuraciones.
* Exponer la API de Kubernetes.
* Guardar el estado del cluster.

---

## etcd

Base de datos distribuida de Kubernetes.

Almacena:

* Pods
* Deployments
* Services
* ConfigMaps
* Estado del cluster

Es la fuente de verdad del sistema.

---

## Scheduler

Decide en qué nodo se ejecutará cada Pod.

Analiza:

* CPU disponible
* Memoria
* Restricciones
* Afinidades

Ejemplo:

```text
Pod A -> Worker 1
Pod B -> Worker 2
Pod C -> Worker 3
```

---

## Controller Manager

Mantiene el estado deseado del cluster.

Si se solicitan:

```text
3 Pods
```

y uno falla:

```text
2 Pods
```

el Controller Manager crea automáticamente un nuevo Pod para volver al estado deseado.

---

# Worker Nodes

Los Worker Nodes son las máquinas donde realmente se ejecutan las aplicaciones.

Contienen:

* kubelet
* kube-proxy
* Container Runtime (containerd, Docker, CRI-O)

Ejemplo:

```text
Worker Node
├── API Pod
├── Redis Pod
└── Nginx Pod
```

---

# Pods

## ¿Qué es un Pod?

Es la unidad mínima desplegable de Kubernetes.

Kubernetes no ejecuta contenedores directamente.

Ejecuta Pods.

```text
Pod
└── Contenedor
```

---

## Pod con un contenedor

```text
Pod
└── Nginx
```

---

## Pod con múltiples contenedores

```text
Pod
├── Aplicación
└── Sidecar
```

Los contenedores dentro del mismo Pod:

* Comparten IP
* Comparten localhost
* Pueden compartir almacenamiento

---

# Primer Pod

Archivo:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod-1

spec:
  containers:
    - name: nginx-pod
      image: nginx
```

Crear:

```bash
kubectl apply -f nginx-pod.yaml
```

Ver Pods:

```bash
kubectl get pods
```

Ver detalles:

```bash
kubectl describe pod nginx-pod-1
```

Ver logs:

```bash
kubectl logs nginx-pod-1
```

---

# kubectl

Herramienta de línea de comandos para administrar Kubernetes.

Ver nodos:

```bash
kubectl get nodes
```

Ver Pods:

```bash
kubectl get pods
```

Ver todos los recursos:

```bash
kubectl get all
```

Eliminar Pod:

```bash
kubectl delete pod nginx-pod-1
```

Aplicar configuración:

```bash
kubectl apply -f archivo.yaml
```

---

# YAML en Kubernetes

Kubernetes utiliza archivos YAML para describir recursos.

Ejemplo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx
```

Extensiones válidas:

```text
.yaml
.yml
```

No existe diferencia funcional entre ambas.

---

# Relación entre Docker y Kubernetes

Docker:

```text
docker run nginx
```

Kubernetes:

```text
Pod
└── nginx
```

Equivalencia conceptual:

| Docker         | Kubernetes            |
| -------------- | --------------------- |
| Imagen         | Image                 |
| Contenedor     | Pod                   |
| Docker Compose | Deployment + Services |
| Red Docker     | Service Networking    |
| Volumen Docker | Persistent Volume     |

---

# Flujo de Trabajo

```text
Desarrollador
      |
      v
kubectl apply
      |
      v
API Server
      |
      v
etcd
      |
      v
Controller Manager
      |
      v
Scheduler
      |
      v
Worker Nodes
      |
      v
Pods
```

---

# Conceptos Clave

* Kubernetes es un orquestador de contenedores.
* El Control Plane toma decisiones.
* Los Worker Nodes ejecutan aplicaciones.
* Los Pods son la unidad mínima desplegable.
* El Scheduler decide dónde ejecutar Pods.
* El Controller Manager mantiene el estado deseado.
* kubectl permite administrar el cluster.
* Kubernetes utiliza archivos YAML para describir recursos.
* En entornos reales los Pods suelen ser administrados mediante Deployments.

---

# Próximos Temas

* Deployments
* ReplicaSets
* Services
* ConfigMaps
* Secrets
* Ingress
* Volúmenes Persistentes
* Escalado Horizontal
* Rolling Updates
* Helm
