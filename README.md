# How to Install Docker  on Ubuntu 22.04
sudo apt update

sudo apt install docker.io -y

sudo systemctl enable docker

sudo systemctl status docker

sudo systemctl start docker

# How to Install Kubernetes on Ubuntu 22.04

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update

sudo apt install kubeadm kubelet kubectl

sudo apt-mark hold kubeadm kubelet kubectl

kubeadm version



# Deploy kubernetes

Disable all swap spaces with the swapoff command:

sudo swapoff -a

sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
/etc/sysctl.d/kubernetes.conf
vi /etc/modules-load.d/containerd.conf
overlay
br_netfilter

sudo modprobe overlay

sudo modprobe br_netfilter

vi /etc/sysctl.d/kubernetes.conf

net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1

sudo sysctl --system

Change unique host name 

sudo hostnamectl set-hostname master-node

sudo hostnamectl set-hostname worker01

vi /etc/default/kubelet
KUBELET_EXTRA_ARGS="--cgroup-driver=cgroupfs"

sudo systemctl daemon-reload && sudo systemctl restart kubelet

/etc/docker/daemon.json

{
      "exec-opts": ["native.cgroupdriver=systemd"],
      "log-driver": "json-file",
      "log-opts": {
      "max-size": "100m"
   },
       "storage-driver": "overlay2"
       }

 sudo systemctl daemon-reload && sudo systemctl restart docker

 vi /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

 Environment="KUBELET_EXTRA_ARGS=--fail-swap-on=false"

 sudo systemctl daemon-reload && sudo systemctl restart kubelet

 sudo kubeadm init --control-plane-endpoint=master-node --upload-certs

 mkdir -p $HOME/.kube

 sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

 sudo chown $(id -u):$(id -g) $HOME/.kube/config

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
