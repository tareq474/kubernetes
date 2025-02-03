# kubernetes
Kubernetes Deployment
Kubernetes manifests for frontend, backend, and database.

Step1:  Deployment for Vue.js Frontend
Create frontend-deployment.yaml:


apiVersion: apps/v1
kind: Deployment
metadata:
  name: vue-frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: vue-frontend
  template:
    metadata:
      labels:
        app: vue-frontend
    spec:
      containers:
        - name: vue-frontend
          image: your-dockerhub/vue-frontend:latest
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: vue-frontend
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
  selector:
    app: vue-frontend

Step 2: Deployment for Laravel API
Create backend-deployment.yaml:


apiVersion: apps/v1
kind: Deployment
metadata:
  name: laravel-backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: laravel-backend
  template:
    metadata:
      labels:
        app: laravel-backend
    spec:
      containers:
        - name: laravel-backend
          image: your-dockerhub/laravel-backend:latest
          envFrom:
            - configMapRef:
                name: laravel-config
          ports:
            - containerPort: 9000
---
apiVersion: v1
kind: Service
metadata:
  name: laravel-backend
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 9000
  selector:
    app: laravel-backend

Define Environment Variables using ConfigMap (configmap.yaml)


apiVersion: v1
kind: ConfigMap
metadata:
  name: laravel-config
data:
  APP_ENV: "production"
  APP_DEBUG: "false"
  DB_HOST: "your-rds-endpoint"
  DB_DATABASE: "your-db-name"
  DB_USERNAME: "your-db-user"
  DB_PASSWORD: "your-db-password"

Step 3:  Deploy MySQL/PostgreSQL Database
If you use RDS, skip this step.


apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: db-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-db
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql-db
  template:
    metadata:
      labels:
        app: mysql-db
    spec:
      containers:
        - name: mysql-db
          image: mysql:8
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "rootpassword"
            - name: MYSQL_DATABASE
              value: "laravel"
            - name: MYSQL_USER
              value: "user"
            - name: MYSQL_PASSWORD
              value: "password"
          ports:
            - containerPort: 3306
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-db
spec:
  type: ClusterIP
  ports:
    - port: 3306
      targetPort: 3306
  selector:
    app: mysql-db

🔹 Deploy to Kubernetes

Step 1: Build & Push Images

# Build & push Vue.js frontend
docker build -t your-dockerhub/vue-frontend .
docker push your-dockerhub/vue-frontend

# Build & push Laravel backend
docker build -t your-dockerhub/laravel-backend .
docker push your-dockerhub/laravel-backend

Step 2: Apply Manifests

kubectl apply -f configmap.yaml
kubectl apply -f frontend-deployment.yaml
kubectl apply -f backend-deployment.yaml
kubectl apply -f database.yaml

Step 3: Verify Deployments

kubectl get pods
kubectl get services
