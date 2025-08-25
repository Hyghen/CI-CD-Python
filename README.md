CI-CD-Python — README.md

This README explains how to run, build, and deploy the Python Flask app used in the CI/CD pipeline project.

Repository

https://github.com/Hyghen/CI-CD-Python

Overview

A small Python Flask app that demonstrates a full CI/CD pipeline: local run, Docker image build, GitHub Actions CI to build and push images to Docker Hub, and local deployment on Minikube.


This repo contains:

-- app.py — Flask application (listens on port 5000)

-- requirements.txt — Python dependencies

-- Dockerfile — to build the app image

-- docker-compose.yml — for local container running

-- .github/workflows/ci-cd.yml — GitHub Actions workflow (CI + build and push)


Prerequisites

-- Git

-- Docker & Docker Hub account

-- GitHub account and the repository fork or push access

-- Minikube & kubectl (for local Kubernetes deployment)

-- Python 3.11 (for local testing)


Quick start (local development)

Clone the repository:

-- git clone https://github.com/Hyghen/CI-CD-Python.git

-- cd CI-CD-Python

Create a virtualenv and install dependencies (optional but recommended):

-- python3 -m venv .venv
-- source .venv/bin/activate
-- pip install -r requirements.txt

Run locally:

-- python app.py
# open http://192.168.1.9:5000

-- Using Docker Compose (local container)

-- Build & run with docker-compose:

-- docker compose up --build
# open http://localhost:5000

Stop:

-- docker compose down

-- Build & push image manually (to Docker Hub)

Replace yourdockerhub with your username.

-- docker build -t chitransh8824/ci-cd-python:latest .
-- docker push chitransh8824/ci-cd-python:latest
-- Configure GitHub Actions (CI/CD)

Create repository secrets on GitHub: DOCKERHUB_USERNAME, DOCKERHUB_TOKEN, DOCKERHUB_REPO (e.g. yourdockerhub/ci-cd-python).

Workflow facts:

-- test job: installs dependencies and runs tests

-- build-and-push job: logs into Docker Hub, builds image, and pushes latest and ${{GITHUB_SHA::7}} tag


Once you push to main, Actions will run automatically.

Deploy to Minikube


Start Minikube:

-- minikube start


Deploy using kubectl (simple approach):

-- kubectl create deployment python-app --image=yourdockerhub/ci-cd-python:latest

-- kubectl expose deployment python-app --type=NodePort --port=5000

-- minikube service python-app --url

Open the returned URL in the browser.

-- Alternative: create k8s/deployment.yaml and k8s/service.yaml and apply them with kubectl apply -f k8s/.


Test & verify

-- Check pod status: kubectl get pods

-- Logs: kubectl logs deploy/python-app

-- Access app: use curl <minikube-url> or kubectl port-forward deploy/python-app 8080:5000 then open http://localhost:8080.


Troubleshooting

-- ImagePullBackOff: ensure image name and tag match Docker Hub and repo is public or credentials available.

-- CrashLoopBackOff: kubectl logs to inspect errors; ensure PORT and app start command are correct.

-- Service not reachable: try kubectl port-forward or minikube service <svc> --url.
