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