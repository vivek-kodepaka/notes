# Kubernetes Complete Notes (Basic + Advanced + Interview Questions)

------------------------------------------------------------------------

# PART 1: Kubernetes Basics

## 1. What is Kubernetes?

Kubernetes (K8s) is a container orchestration platform used to: - Deploy
containers - Scale applications - Manage networking - Handle failures
automatically

Interview One-liner: Kubernetes automates deployment, scaling, and
management of containerized applications.

------------------------------------------------------------------------

## 2. Why Kubernetes?

Problems Docker alone cannot solve: - Auto scaling - Self-healing - Load
balancing - Rolling deployments - Service discovery

------------------------------------------------------------------------

## 3. Kubernetes Architecture

Main Components:

### Control Plane

-   API Server
-   Scheduler
-   Controller Manager
-   etcd

### Worker Node

-   Kubelet
-   Kube Proxy
-   Container Runtime

Flow: kubectl → API Server → Scheduler → Node → Pod runs

------------------------------------------------------------------------

## 4. What is a Pod?

Pod = Smallest deployable unit in Kubernetes.

A pod can contain: - One container (most common) - Multiple containers
(sidecar pattern)

------------------------------------------------------------------------

## 5. Deployment

Deployment manages: - Replica count - Rolling updates - Rollbacks

Example YAML:

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
      - name: hello
        image: nginx
        ports:
        - containerPort: 80
```

Apply: kubectl apply -f deployment.yaml

------------------------------------------------------------------------

## 6. Service

Service exposes pods.

Types: - ClusterIP (default) - NodePort - LoadBalancer

Example:

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-service
spec:
  type: NodePort
  selector:
    app: hello
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30007
```

------------------------------------------------------------------------

## 7. Basic kubectl Commands

Check nodes: kubectl get nodes

Check pods: kubectl get pods

Describe pod: kubectl describe pod `<podname>`{=html}

Delete pod: kubectl delete pod `<podname>`{=html}

------------------------------------------------------------------------

# PART 2: Deploying One Microservice (Step-by-Step)

Step 1: Build Docker Image docker build -t hello-service .

Step 2: Push Image docker push username/hello-service

Step 3: Create Deployment YAML Use deployment example above.

Step 4: Expose Service kubectl expose deployment hello-deployment
--type=NodePort --port=80

Step 5: Access Application NodeIP:NodePort

------------------------------------------------------------------------

# PART 3: Advanced Kubernetes Concepts

## 8. ConfigMap

Used to store configuration.

Example:

``` yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: production
```

------------------------------------------------------------------------

## 9. Secrets

Used to store passwords securely.

------------------------------------------------------------------------

## 10. Horizontal Pod Autoscaler (HPA)

Automatically scales pods based on CPU usage.

------------------------------------------------------------------------

## 11. Liveness and Readiness Probes

Liveness: Checks if container is alive.

Readiness: Checks if container is ready to serve traffic.

------------------------------------------------------------------------

## 12. Rolling Updates

Kubernetes gradually replaces pods without downtime.

------------------------------------------------------------------------

## 13. Namespace

Used to isolate environments: - dev - test - prod

------------------------------------------------------------------------

## 14. Ingress

Used to expose HTTP services with routing and SSL.

------------------------------------------------------------------------

## 15. StatefulSet

Used when: - Stable network identity required - Databases like MySQL,
Kafka

------------------------------------------------------------------------

# PART 4: Kubernetes Networking

Pod to Pod communication: Flat networking model.

Service provides stable IP.

------------------------------------------------------------------------

# PART 5: Kubernetes Storage

Volumes: - emptyDir - hostPath - PersistentVolume (PV) -
PersistentVolumeClaim (PVC)

------------------------------------------------------------------------

# PART 6: Most Asked Interview Questions

1.  Difference between Pod and Container?
2.  What is Deployment?
3.  What is Service?
4.  What is Ingress?
5.  How Kubernetes handles failure?
6.  What is ReplicaSet?
7.  Difference between ConfigMap and Secret?
8.  What is HPA?
9.  What is Namespace?
10. Rolling update vs Recreate?

------------------------------------------------------------------------

# PART 7: Tricky Interview Questions

1.  What happens if a pod crashes? Kubernetes restarts it automatically.

2.  Can two pods communicate without service? Yes, using pod IP but not
    recommended.

3.  Why not expose pods directly? Pod IP changes.

4.  Difference between Deployment and StatefulSet? Deployment for
    stateless apps, StatefulSet for stateful apps.

------------------------------------------------------------------------

# PART 8: Real Interview Summary

Kubernetes is a container orchestration system that manages deployment,
scaling, networking, and reliability of containerized applications in
production environments.
