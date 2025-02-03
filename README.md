# kubernetes
Kubernetes Deployment
Kubernetes manifests for frontend, backend, and database.

Step1:  Deployment for Vue.js Frontend
Create frontend-deployment.yaml:


Step 2: Deployment for Laravel API
Create backend-deployment.yaml:


Step 2: Environment Variables using ConfigMap (configmap.yaml)
Step 3:  Deploy MySQL/PostgreSQL Database


🔹 Deploy to Kubernetes

Step 1: Build & Push Images

# Build & push Vue.js frontend
docker build -t tarequl/frontend:v1 .
docker login -u tarequl -p password
docker push tarequl/frontend:v1


# Build & push Laravel backend
docker build -t tarequl/backend:v1 .
docker push tarequl/backend:v1

Step 2: Apply Manifests

kubectl apply -f configmap.yaml
kubectl apply -f frontend-deployment.yaml
kubectl apply -f backend-deployment.yaml
kubectl apply -f database.yaml

Step 3: Verify Deployments

kubectl get pods
kubectl get services
