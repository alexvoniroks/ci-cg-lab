# CI/CD Lab Project 🚀

This project demonstrates **CI/CD pipelines** implemented with **three different tools**:  
- **GitHub Actions**  
- **CircleCI**  
- **Jenkins**  

The application is a simple **FastAPI app** containerized with Docker.

---

## 🔹 Project Goals
- Show knowledge of Infrastructure as Code and CI/CD best practices.  
- Demonstrate using **multiple CI/CD platforms**.  
- Automate testing, building, and pushing Docker images.  
- Deploy automatically using CircleCI.

---

## 🔹 Tech Stack
- **FastAPI** (Python)  
- **Docker & Docker Hub**  
- **GitHub Actions**  
- **CircleCI**  
- **Jenkins** (on VM with Poll SCM)  

---

## 🔹 Pipelines Overview

### ✅ GitHub Actions (CI)
- Trigger: push to `main`.  
- Steps:  
  1. Checkout code.  
  2. Install dependencies.  
  3. Run tests with `pytest`.  
  4. Build Docker image.  
  5. Push image to Docker Hub → tagged as `:gh-actions`.  

Workflow file: `.github/workflows/ci.yml`

---

### ✅ CircleCI (CI + CD)
- Trigger: push to `main`.  
- Steps:  
  1. Checkout repo.  
  2. Run tests.  
  3. Build Docker image.  
  4. Push image to Docker Hub → tagged as `:circleci`.  
  5. Deploy to server (via SSH or container restart).  

Config file: `.circleci/config.yml`

---

### ✅ Jenkins (CI only)
- Installed directly on a VM.  
- Build Trigger: **Poll SCM** every 5 minutes (no public webhook required).  
- Steps:  
  1. Checkout code from GitHub (via PAT).  
  2. Create Python virtual environment.  
  3. Install dependencies + run `pytest`.  
  4. (Optional) Build & push Docker image → tagged as `:jenkins`.  
     - For this lab, Jenkins focuses on CI only (to avoid duplicate pushes).  

Pipeline config: `Jenkinsfile`

---

## 🔹 Credentials & Secrets
- **GitHub Actions** → secrets: `DOCKERHUB_USER`, `DOCKERHUB_TOKEN`.  
- **CircleCI** → project env vars: `DOCKERHUB_USER`, `DOCKERHUB_TOKEN`.  
- **Jenkins** → credentials:  
  - `github-https-creds` (GitHub username + PAT)  
  - `dockerhub-creds` (Docker Hub username + password/token)  

---

## 🔹 Docker Hub Images
- Built via GitHub Actions → `username/ci-cd-lab:gh-actions`  
- Built via CircleCI → `username/ci-cd-lab:circleci`  
- Built via Jenkins (optional) → `username/ci-cd-lab:jenkins`

---

## 🔹 Network Diagram

       ┌───────────────┐
       │   Developer    │
       └──────┬────────┘
              │ git push
              ▼
      ┌───────────────────┐
      │    GitHub Repo    │
      └─────┬─────────────┘
┌───────────┼───────────────┐
│           │               │
▼           ▼               ▼
┌────────┐ ┌──────────┐ ┌──────────┐
│ GitHub │ │ CircleCI │ │ Jenkins  │
│ Actions│ │ (CI + CD)│ │ (CI only)│
└───┬────┘ └─────┬────┘ └────┬─────┘
│ ┌──────────────────────────┐     │             
│ ▼              ▼           ▼     │
│      Docker Hub (Images)         │
│                │                 │
└──────────────────────────────────┘
🔹 Notes

In production, only one CI/CD system should be responsible for building/pushing images.

In this lab, all three are included for demonstration.

Jenkins uses Poll SCM (every 5 minutes) since the VM is private and can’t receive GitHub webhooks.