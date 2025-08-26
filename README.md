# $$$$ First Project  ------>   CI/CD Pipeline with GitHub Actions & Docker (CI-CD-Python) 

This README explains how to run, build, and deploy the Python Flask app used in the CI/CD pipeline project.

# Repository

https://github.com/Hyghen/CI-CD-Python

# Overview

A small Python Flask app that demonstrates a full CI/CD pipeline: local run, Docker image build, GitHub Actions CI to build and push images to Docker Hub, and local deployment on Minikube.


# This repo contains:

-- app.py — Flask application (listens on port 5000)

-- requirements.txt — Python dependencies

-- Dockerfile — to build the app image

-- docker-compose.yml — for local container running

-- .github/workflows/ci-cd.yml — GitHub Actions workflow (CI + build and push)


# Prerequisites

-- Git

-- Docker & Docker Hub account

-- GitHub account and the repository fork or push access

-- Minikube & kubectl (for local Kubernetes deployment)

-- Python 3.11 (for local testing)


# Quick start (local development)

# Clone the repository:

-- git clone https://github.com/Hyghen/CI-CD-Python.git

-- cd CI-CD-Python

# Create a virtualenv and install dependencies (optional but recommended):

-- python3 -m venv .venv

-- source .venv/bin/activate

-- pip install -r requirements.txt

# Run locally:

-- python app.py
   open http://192.168.1.9:5000

-- Using Docker Compose (local container)

-- Build & run with docker-compose:

-- docker compose up --build
   open http://localhost:5000

# Stop:

-- docker compose down

-- Build & push image manually (to Docker Hub)

# Replaced name with my dockerhub account

-- docker build -t chitransh8824/ci-cd-python:latest .

-- docker push chitransh8824/ci-cd-python:latest

<img width="1262" height="813" alt="Pushed-" src="https://github.com/user-attachments/assets/bd4b0fb3-bdf8-444b-9e3c-f7c4a8e5abce" />


-- Configure GitHub Actions (CI/CD)

Create repository secrets on GitHub: DOCKERHUB_USERNAME, DOCKERHUB_TOKEN, DOCKERHUB_REPO (e.g. yourdockerhub/ci-cd-python).

# Workflow facts:

-- test job: installs dependencies and runs tests

-- build-and-push job: logs into Docker Hub, builds image, and pushes latest and ${{GITHUB_SHA::7}} tag


<img width="1182" height="311" alt="CICD-Workflow" src="https://github.com/user-attachments/assets/31c7686c-1749-40d8-a77e-ea496e86b3cf" />


Once you push to main, Actions will run automatically.

# Deploy to Minikube


# Start Minikube:

-- minikube start


# Deploy using kubectl (simple approach):

-- kubectl create deployment python-app --image=yourdockerhub/ci-cd-python:latest

-- kubectl expose deployment python-app --type=NodePort --port=5000

-- minikube service python-app --url

# Open the returned URL in the browser.

-- Alternative: create k8s/deployment.yaml and k8s/service.yaml and apply them with kubectl apply -f k8s/.


# Test & verify

-- Check pod status: kubectl get pods

-- Logs: kubectl logs deploy/python-app

-- Access app: use curl <minikube-url> or kubectl port-forward deploy/python-app 8080:5000 then open http://localhost:8080.


# Troubleshooting

-- ImagePullBackOff: ensure image name and tag match Docker Hub and repo is public or credentials available.

-- CrashLoopBackOff: kubectl logs to inspect errors; ensure PORT and app start command are correct.

-- Service not reachable: try kubectl port-forward or minikube service <svc> --url.






# $$$$ Second Project  ------->  Local DevOps Sandbox for Monitoring & Alerting (All-in-One VM)

### Objective

Set up a monitoring sandbox environment on **RHEL 9** with Prometheus, Grafana, Node Exporter, and Alertmanager. Preconfigure alerts for CPU, disk, and service health so this environment can be reused for DevOps learning and testing.


### Tools & Stack

* **Prometheus** (metrics collection)

* **Node Exporter** (system metrics)

* **Grafana** (visualization)

* **Alertmanager** (alerting)



### Setup Steps


# 1. Installed Dependencies

-- sudo dnf install -y wget tar systemd


# 2. Created User Accounts

-- sudo useradd --no-create-home --shell /bin/false prometheus

-- sudo useradd --no-create-home --shell /bin/false node_exporter


# 3. Installed Prometheus & Node Exporter

* Download official tarballs from Prometheus site.

* Extract, copy binaries to `/usr/local/bin/`.

* Create data directories under `/var/lib/prometheus/`.


# 4. Installed Grafana

-- sudo dnf install -y https://dl.grafana.com/oss/release/grafana-10.4.0-1.x86_64.rpm

-- sudo systemctl enable --now grafana-server


# 5. Installed Alertmanager

* Download official tarball.

* Extract and move binaries to `/usr/local/bin/`.

* Create working directory at `/etc/alertmanager/`.


# 6. Configured Services

* Place Prometheus, Node Exporter, and Alertmanager configs under `/etc/`.

* Set proper ownership (`chown prometheus:prometheus`).

* Create systemd unit files for Prometheus, Node Exporter, and Alertmanager.

* Reload systemd and enable services:

  -- sudo systemctl daemon-reexec

  -- sudo systemctl enable --now prometheus node_exporter alertmanager grafana-server
  

# 7. Firewall Rules

-- sudo firewall-cmd --add-port=9090/tcp --permanent   # Prometheus

-- sudo firewall-cmd --add-port=9100/tcp --permanent   # Node Exporter

-- sudo firewall-cmd --add-port=9093/tcp --permanent   # Alertmanager

-- sudo firewall-cmd --add-port=3000/tcp --permanent   # Grafana

-- sudo firewall-cmd --reload


# 8. Validation & Testing


* **Prometheus:** `http://192.168.1.9:9090`

<img width="1372" height="666" alt="Prometheus-console" src="https://github.com/user-attachments/assets/6e5ef577-db5f-4d3e-811e-4123737a1135" />


* **Node Exporter:** `http://192.168.1.9:9100/metrics`

<img width="962" height="516" alt="Node-exporter-console" src="https://github.com/user-attachments/assets/c6ee881d-05ee-4b89-abba-1773a7b7dffd" />


* **Grafana:** `http://192.168.1.9:3000` (default login: admin/admin)

<img width="1386" height="907" alt="Grafana-console" src="https://github.com/user-attachments/assets/ad3a30bc-7760-4582-b542-36d8087205f2" />


* **Alertmanager:** `http://192.168.1.9:9093`

<img width="1507" height="677" alt="Alert-manager-console" src="https://github.com/user-attachments/assets/c216ee2b-fc47-4ed2-aa61-6f6fbbd5d7d8" />


**Test Alerts:**

* Create a CPU usage alert rule in Prometheus.

* Stop Node Exporter (`sudo systemctl stop node_exporter`) and check Alertmanager.

* Grafana dashboards should show dropped metrics.


# 9. Troubleshooting

* Check logs:

    -- sudo journalctl -u prometheus -u node_exporter -u grafana-server -u alertmanager -f

* Ensure firewall ports are open.
* Verify config files have correct paths and permissions.
