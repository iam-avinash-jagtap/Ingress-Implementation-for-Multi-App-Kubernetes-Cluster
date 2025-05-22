
# Ingress Implementation for Multi-App Kubernetes Cluster 
In this project you are going to deploy two separate containerized applications — a Django-based Notes App 📝 and a static Nginx web server 🌐 — on a local Kubernetes cluster using kind (Kubernetes in Docker 🐳). Both applications are deployed in a single namespace with proper services and ingress configured to access them via a single exposed port. The project helps understand real-world Kubernetes workflows including namespace isolation, deployment, ingress setup, and multi-node cluster simulation.

## ✅ Pre-requisites:  
### Tools Installed:

- 🐳 Docker (installed and running)  
- 🧱 kind (Kubernetes in Docker)  
- ⚙️ kubectl (Kubernetes CLI)  
- 📝 Text editor VS Code  

### Accounts:

- 🔐 DockerHub account (for pushing and pulling images)  
- ☁️ AWS account (for Kubernetes server setup)  

### Knowledge Required:

- 🐳 Docker Basics  
- ☸️ Kubernetes basic Fundamentals:-  
  - 📦 Deployments  
  - 🔗 Services  
  - 🗂️ Namespaces  
  - 🚪 Ingress Controllers  
- 📄 YAML Syntax basic understanding  
- 🌐 Basic Networking Concepts  

## ✅ Project Structure:  
```bash
project-root/
│
├── kind-installation.sh  # To install Kind cluster 
├── kind-cluster-config.yml # To Create cluster
│
├── nginx/                    # For Nginx app
│   ├── namespace.yml
│   ├── deployment.yml
│   ├── service.yml
│   └── ingress.yml
│
├── notes-app/                # For Django Notes App
│   ├── deployment.yml
│   └── service.yml
```

## 🛠️ Project Architecture:
![Architecture](https://github.com/iam-avinash-jagtap/Ingress-Implementation-for-Multi-App-Kubernetes-Cluster/blob/master/Images/Architecture.png)

## 🏗️ Kind Cluster Creation  
_In this project, you are going to set up a multi-node kind Kubernetes cluster on an AWS EC2 instance for local deployment simulation._

1. [🔗 EC2_Instance_Launch_Steps](https://github.com/iam-avinash-jagtap/Linux-Server-Deployment-on-AWS-E2)  
2. 🛡️ Update Security Group with ports - 80,8080,443,22,8000  
3. 🔐 Get SSH access of Instance  
4. 🖥️ Follow Commands:  
```bash
sudo apt-update 

vim kind-installation.sh            # Kind installation script

chmod 777 kind-installation.sh      # Set ececutable permission to .sh file

./kind-installation.sh              # Run the kind-installer.sh 

sudo apt update                     # update system 

sudo apt-get install docker.io      # Install docker

sudo usermod -aG docker $USER && newgrp docker  # Assign permission to current user

# Verify all installation 

docker --version

kubectl version

kind --version
```

# 🪜 Steps:

## Step 1:- 🛠️ Create a local Kubernetes Cluster using Kind
_In this step, you will create a local multi-node Kubernetes cluster using kind to simulate a real deployment environment._  
1. Create kind cluster from - kind-cluster-config.yml file  
2. Run the command:  
```bash
kind create cluster --config kind-cluster-config.yml --name my-kind-cluster
```

## Step 2:- 🐳 Build and Push Django Notes App Docker Image  
_In this step, you will build a Docker image for the Django Notes App and push it to Docker Hub for deployment._  
1. Clone repository - `git clone https://github.com/iam-avinash-jagtap/Django-notes-application.git`
  
2. Build docker image - `docker build -t notes-app-k8s .`  

3. Check docker images - `docker image`  

4. Verify image - `notes-app-k8s:latest`  

5. Login to DockerHub & create token 🔑  

6. Tag image - `docker image tag notes-app-k8s:latest <Username>/notes-app-k8s:latest`  

7. Push to DockerHub - `docker push <Username>/notes-app-k8s:latest`  

✅ Your Note-app image is ready on DockerHub  

## Step 3:- 📂 Set up nginx server and notes-app application  
_In this step, you will deploy both the Nginx server and Django Notes App using Kubernetes Deployments and Services in a common namespace._  
1. Clone repository - `git clone https://github.com/iam-avinash-jagtap/Ingress-Implementation-for-Multi-App-Kubernetes-Cluster.git`  

### Deploy Nginx App 🌐  
```bash
cd nginx

kubectl apply -f namespace.yml              

kubectl apply -f deployment.yml             

kubectl apply -f service.yml                

kubectl get all -n nginx                    
```

### Deploy Notes App 📝  
```bash

cd ../notes-app

kubectl apply -f deployment.yml             

kubectl apply -f service.yml                

kubectl get all -n nginx                    
```
📌 All resources are in `nginx` namespace for Ingress setup  

## Step 4:- 🌐 Configure Ingress  
_In this step, you will configure Ingress to route external traffic to the Nginx server and Django Notes App based on URL paths._  
```bash

kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml

cd nginx/

kubectl apply -f ingress.yml            

kubectl get ing -n nginx                

kubectl get all -n nginx                
```
![namespace-nginx](https://github.com/iam-avinash-jagtap/Ingress-Implementation-for-Multi-App-Kubernetes-Cluster/blob/master/Images/All%20Resources.png)

## Step 5:- 🔌 Expose the Ingress Port  
_In this step, you will expose the Ingress controller port by mapping it to your host machine, enabling browser access to both applications._  
```bash
sudo -E kubectl port-forward service/ingress-nginx-controller -n ingress-nginx 8080:80 --address=0.0.0.0
```

## Step 6:- 🌍 Access the Applications  
_In this step, you will access both applications in the browser using URL as defined in the Ingress rules._  
- 📥 http://<public_IP>:8080/               → Notes App  

![notes-app](https://github.com/iam-avinash-jagtap/Ingress-Implementation-for-Multi-App-Kubernetes-Cluster/blob/master/Images/Notes-app.png)
   
- 📄 http://<public_IP>:8080/nginx          → Nginx App  

![nginx](https://github.com/iam-avinash-jagtap/Ingress-Implementation-for-Multi-App-Kubernetes-Cluster/blob/master/Images/Nginx-app.png)
---

## 🚀 Project Flow:  
### Multi-App Kubernetes Setup with Ingress  

1. 🔧 Set Up EC2 Instance  
    - Launch Ubuntu EC2, open ports 80, 8080, 443, 22, 8000, and SSH into it.

2. 📦 Install Tools  
    - Install Docker, Kind, and Kubectl using kind-installation.sh.

3. ⚙️ Create Kubernetes Cluster  
    - Use kind-cluster-config.yml to spin up a local Kind cluster.

4. 🐳 Build Django App Image  
    - Clone the Notes App repo, build the Docker image, tag & push it to DockerHub.

5. 🖼️ Deploy Nginx App  
    - Apply namespace.yml, deployment.yml, service.yml in the nginx/ folder.

6. 📝 Deploy Notes App  
    - Apply deployment.yml and service.yml from the notes-app/ folder in the same namespace.

7. 🌐 Setup Ingress  
    - Install NGINX ingress controller and apply ingress.yml for route-based access.

8. 🔌 Port Forwarding  
    - Forward cluster port 80 to host port 8080 using kubectl port-forward.

9. 🌍 Access Your Apps  
    - http://<public_ip>:8080/ → Notes App  
    - http://<public_ip>:8080/nginx → Nginx App

10. ✅ Done!  
    - Multi-app environment deployed successfully on local Kubernetes with a single exposed port using Ingress.

---

## 📄 Summary  

This project shows how to run two different apps — a Django Notes App 📝 and a static Nginx website 🌐 — on a local Kubernetes cluster using Kind (Kubernetes in Docker 🐳). First, you set up an EC2 virtual machine on AWS and install some basic tools like Docker, Kind, and Kubectl. Then, you create a local Kubernetes cluster using a configuration file.

Next, you build a Docker image for the Notes App and push it to DockerHub so it can be used inside the cluster. Both the Nginx app and the Notes app are deployed using Kubernetes files (called manifests). These apps run in the same namespace, which helps group them together and makes it easier to manage traffic between them.

Finally, you set up an Ingress controller that helps direct traffic to the correct app based on the path in the browser URL. You expose the cluster to your computer using port forwarding, so you can open both apps in your browser using a single port. This project helps you understand how real Kubernetes setups work and prepares you to work on bigger cloud projects in the future.
