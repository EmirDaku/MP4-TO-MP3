# 🎥 YouTube Downloader - Kubernetes & CI/CD Deployment

A Python-based YouTube downloader application built with **Streamlit** and **FFmpeg**, containerized using **Docker**, automated with **GitHub Actions (CI/CD)**, and orchestrated on **Kubernetes (Minikube)** with **Horizontal Pod Autoscaler (HPA)** for dynamic scaling based on CPU utilization.

---

## 🏗️ System Architecture

* **Application Stack:** Python 3.13-slim & Streamlit (Port `8501`).
* **Containerization:** Dockerized application image published to Docker Hub.
* **CI/CD Pipeline:** Automated build-and-push workflow triggered on every `git push` to the `main` branch.
* **Kubernetes Orchestration:** 
  * Deployment configured with **3 base replicas**.
  * `NodePort` Service configured with **Session Affinity (`ClientIP`)** to maintain sticky sessions for Streamlit web connections.
* **Autoscaling:** Horizontal Pod Autoscaler (HPA) configured to scale dynamically between **3 to 5 pods** based on target CPU utilization (50%).

---

## 📁 Repository Structure

```text
yt-downloader-k8s/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions CI/CD Pipeline
├── k8s/
│   ├── deployment.yaml         # Kubernetes Deployment Manifest
│   ├── service.yaml            # Kubernetes NodePort Service Manifest
│   └── hpa.yaml                # Horizontal Pod Autoscaler Manifest
├── Dockerfile                  # Multi-stage Docker image definition
├── requirements.txt            # Python dependencies
├── youtube_downloader.py       # Main Streamlit application code
└── README.md                   # Project documentation



🚀 Complete Setup & Operational Guide
1. Prerequisites
Ensure you have the following tools installed on your system:

Docker Desktop

Minikube

kubectl

Git
2. CI/CD Setup (GitHub Actions)
To enable automated container image builds to Docker Hub:

Go to your GitHub repository: Settings -> Secrets and variables -> Actions.

Add the following repository secrets:

DOCKERHUB_USERNAME: Your Docker Hub username.

DOCKERHUB_TOKEN: Your Docker Hub Personal Access Token (PAT).

Every commit pushed to the main branch will trigger .github/workflows/deploy.yml to build and publish the container image automatically.
3. Local Cluster Setup & Deployment
Step 1: Start Minikube
Bash
minikube start
Step 2: Enable Metrics Server
The metrics-server addon is required for the Horizontal Pod Autoscaler to collect CPU and memory metrics:
```

```Bash
minikube addons enable metrics-server
```
Verification: Wait approximately 30 seconds and confirm metrics collection is active:

```Bash
kubectl top pods
```
Step 3: Deploy to Kubernetes
Apply all manifests contained within the k8s/ directory in a single command:

```Bash
kubectl apply -f k8s/
```
Alternatively, apply them individually:

```Bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/hpa.yaml
```
4. Accessing the Application
Expose and open the Streamlit web application directly in your browser:

```Bash
minikube service yt-downloader-service
```
5. Testing Horizontal Pod Autoscaler (HPA)
To verify dynamic scaling under high CPU load:

Monitor HPA status in real time (Terminal 1):

Bash
kubectl get hpa -w
Generate artificial load (Terminal 2):

Run a temporary pod that generates continuous HTTP requests to the Streamlit service:

Bash
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while true; do wget -q -O- http://yt-downloader-service:8501; done"
Expected Behavior:

When CPU utilization exceeds 50%, HPA will automatically scale up REPLICAS from 3 to 5.

Once the load generator is stopped (Ctrl + C), HPA will cool down and scale back down to 3 replicas.
