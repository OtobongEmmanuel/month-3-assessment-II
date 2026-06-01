# MuchToDo API - Docker & Kubernetes Deployment

## Project Overview

MuchToDo is a RESTful ToDo API built with Go (Golang) that provides secure user authentication, JWT-based authorization, and CRUD operations for managing tasks.

This project has been containerized using Docker and deployed to Kubernetes using Kind (Kubernetes in Docker). The deployment demonstrates container orchestration, service exposure, configuration management, secrets management, and persistent storage.

---

## Features

* User Registration and Authentication
* JWT-Based Authorization
* CRUD Operations for ToDo Items
* MongoDB Database Integration
* Structured JSON Logging
* Health Check Endpoint
* Docker Containerization
* Docker Compose Deployment
* Kubernetes Deployment with Kind
* ConfigMaps and Secrets
* Persistent Volume Claims (PVC)
* NodePort Service Exposure

---

## Technology Stack

| Component                | Technology               |
| ------------------------ | ------------------------ |
| Backend                  | Go (Golang)              |
| Database                 | MongoDB 8.0              |
| Containerization         | Docker                   |
| Container Orchestration  | Kubernetes               |
| Local Kubernetes Cluster | Kind                     |
| Configuration Management | ConfigMaps               |
| Secret Management        | Kubernetes Secrets       |
| Storage                  | Persistent Volume Claims |

---

## Architecture

Client
↓
Backend API (Go)
↓
MongoDB

### Kubernetes Components

* Namespace
* Backend Deployment (2 replicas)
* Backend Service (NodePort)
* MongoDB Deployment
* MongoDB Service (ClusterIP)
* ConfigMaps
* Secrets
* Persistent Volume Claim

---

# Docker Deployment

## Build Image

```bash
docker build -t muchtodo-backend:v1 .
```

## Run Using Docker Compose

```bash
docker compose up -d
```

Verify:

```bash
docker compose ps
```

Expected:

```bash
muchtodo-backend   Up
muchtodo-mongodb   Up
```

---

## Verify Application

```bash
curl http://localhost:8080/health
```

Expected Response:

```json
{
  "database":"ok",
  "cache":"disabled"
}
```

---

# Kubernetes Deployment

## Prerequisites

* Docker
* kubectl
* Kind

Verify installations:

```bash
docker --version
kind version
kubectl version --client
```

---

## Create Kind Cluster

```bash
kind create cluster --name muchtodo --config kind-config.yaml
```

Verify:

```bash
kubectl get nodes
```

---

## Deploy Namespace

```bash
kubectl apply -f kubernetes/namespace.yaml
```

---

## Deploy MongoDB

```bash
kubectl apply -f kubernetes/mongodb/
```

Verify:

```bash
kubectl get pods -n muchtodo
```

---

## Load Backend Image Into Kind

```bash
kind load docker-image muchtodo-backend:v1 --name muchtodo
```

---

## Deploy Backend

```bash
kubectl apply -f kubernetes/backend/
```

---

## Verify Deployments

```bash
kubectl get deployments -n muchtodo
```

Expected:

```bash
NAME      READY
backend   2/2
mongodb   1/1
```

---

## Verify Pods

```bash
kubectl get pods -n muchtodo
```

Expected:

```bash
backend-xxxxx   Running
backend-xxxxx   Running
mongodb-xxxxx   Running
```

---

## Verify Services

```bash
kubectl get svc -n muchtodo
```

Expected:

```bash
backend-service   NodePort
mongodb           ClusterIP
```

---

## Verify Health Endpoint

```bash
curl http://localhost:8080/health
```

Expected:

```json
{
  "database":"ok",
  "cache":"disabled"
}
```

---

# Kubernetes Resources

## Backend

* Deployment (2 replicas)
* ConfigMap
* Secret
* NodePort Service

## MongoDB

* Deployment
* Service
* ConfigMap
* Secret
* Persistent Volume Claim

---

# Project Structure

```text
MuchToDo/
│
├── Dockerfile
├── docker-compose.yml
├── kind-config.yaml
│
├── kubernetes/
│   ├── namespace.yaml
│   │
│   ├── backend/
│   │   ├── backend-configmap.yaml
│   │   ├── backend-secret.yaml
│   │   ├── backend-deployment.yaml
│   │   └── backend-service.yaml
│   │
│   └── mongodb/
│       ├── mongodb-configmap.yaml
│       ├── mongodb-secret.yaml
│       ├── mongodb-pvc.yaml
│       ├── mongodb-deployment.yaml
│       └── mongodb-service.yaml
│
├── evidence/
│
└── README.md
```

---

# Deployment Evidence

All required assessment screenshots are available in the `evidence/` directory.

Contents include:

* Docker Build Completion
* Docker Compose Running
* Application Responding Through Docker Compose
* Kind Cluster Creation
* Kubernetes Deployments Running
* Kubernetes Pods Running
* Kubernetes Services
* NodePort Service Verification
* Application Accessible Through Kubernetes
* Kubectl Resource Verification

---

# Verification Commands

```bash
kubectl get all -n muchtodo

kubectl get pods -n muchtodo

kubectl get deployments -n muchtodo

kubectl get svc -n muchtodo

kubectl get pvc -n muchtodo
```

---

# Author

Otobong Emmanuel

Cloud / DevOps Engineer
