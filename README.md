# SkillPulse 🚀

A full-stack web application with a complete CI/CD pipeline supporting deployment to both a self-hosted runner and AWS EC2.

---

## 📌 Table of Contents

- [About the Project](#about-the-project)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [CI/CD Pipeline](#cicd-pipeline)
- [Deployment Options](#deployment-options)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Project Structure](#project-structure)

---

## About the Project

SkillPulse is a containerized full-stack application built to demonstrate end-to-end DevOps practices including containerization, automated CI/CD pipelines, and multi-environment deployments using GitHub Actions.

---

## Architecture

```
┌─────────────────────┐
│  Frontend           │  ← HTML/CSS/JS (Port 80)
│  (Nginx)            │
└────────┬────────────┘
         │
┌────────▼────────────┐
│  Backend            │  ← Go API (Port 8080)
└────────┬────────────┘
         │
┌────────▼────────────┐
│  MySQL 8.4          │  ← Database (Port 3306)
│  + Persistent Vol   │
└─────────────────────┘
```

All services run as Docker containers orchestrated via Docker Compose.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | HTML, CSS, JavaScript |
| Backend | Go |
| Database | MySQL 8.4 |
| Containerization | Docker, Docker Compose |
| CI/CD | GitHub Actions |
| Registry | DockerHub |
| Cloud | AWS EC2 |

---

## CI/CD Pipeline

### Pipeline Overview

```
Push to main / Manual Trigger
          │
          ▼
┌─────────────────────────────────┐
│           CI Pipeline           │
│  - Checkout code                │
│  - Build backend Docker image   │
│  - Build frontend Docker image  │
│  - Push to DockerHub            │
│    manish12588/skillpulse-*     │
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│           CD Pipeline           │
│  - Clone/pull repo OR           │
│    pull images from DockerHub   │
│  - Create .env from secrets     │
│  - docker compose up -d         │
│  - Prune old images             │
└─────────────────────────────────┘
```

### Workflow Files

| File | Purpose |
|------|---------|
| `ci.yml` | Build and push Docker images to DockerHub |
| `cd-selfhosted-github.yml` | Deploy to self-hosted runner via Git clone |
| `cd-selfhosted-dockerhub.yml` | Deploy to self-hosted runner via DockerHub pull |
| `cd-ec2-github.yml` | Deploy to AWS EC2 via Git clone over SSH |
| `cd-ec2-dockerhub.yml` | Deploy to AWS EC2 via DockerHub pull over SSH |
| `pipeline-selfhosted.yml` | Full pipeline: CI + CD to self-hosted runner |
| `pipeline-ec2.yml` | Full pipeline: CI + CD to AWS EC2 |

---

## Deployment Options

### Option 1: Self-Hosted Runner

The GitHub Actions runner runs directly on your local machine. No SSH required — commands execute on the machine itself.

```
GitHub Actions → Self-Hosted Runner → docker compose up -d
```

### Option 2: AWS EC2

CI runs on GitHub-hosted runner, then SSHes into your EC2 instance to deploy.

```
GitHub Actions (ubuntu-latest) → SSH → EC2 → docker compose up -d
```

---

## Getting Started

### Prerequisites

- Docker and Docker Compose installed
- GitHub account
- DockerHub account

### Local Setup

```bash
# Clone the repository
git clone https://github.com/Manish12588/skillpulse.git
cd skillpulse

# Create environment file
cp .env.example .env
# Edit .env with your values
nano .env

# Start all services
docker compose up -d

# Verify services are running
docker compose ps
```

App will be available at `http://localhost`

### GitHub Actions Setup

1. Fork the repository
2. Add the following **Secrets** in **Settings → Secrets and variables → Actions**:

| Secret | Description |
|--------|-------------|
| `DOCKERHUB_USERNAME` | Your DockerHub username |
| `DOCKERHUB_TOKEN` | DockerHub access token |
| `PAT_TOKEN` | GitHub Personal Access Token (repo scope) |
| `MYSQL_ROOT_PASSWORD` | MySQL root password |
| `DB_HOST` | Database host |
| `DB_PASSWORD` | Application DB password |
| `EC2_HOST` | EC2 public IP address |
| `EC2_USER` | EC2 SSH username (e.g. `ubuntu`) |
| `EC2_SSH_KEY` | EC2 private key (full PEM content) |

3. Add the following **Variables** in **Settings → Secrets and variables → Actions**:

| Variable | Description |
|----------|-------------|
| `DB_USER` | Database username |
| `DB_NAME` | Database name |

4. Trigger the pipeline from **Actions → Select workflow → Run workflow**

---

## Environment Variables

```env
# Database
MYSQL_ROOT_PASSWORD=your_root_password
DB_HOST=db
DB_USER=skilluser
DB_PASSWORD=your_password
DB_NAME=skillpulse

# DockerHub
DOCKERHUB_USERNAME=your_dockerhub_username
```

---

## Project Structure

```
skillpulse/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── cd-selfhosted-github.yml
│       ├── cd-selfhosted-dockerhub.yml
│       ├── cd-ec2-github.yml
│       ├── cd-ec2-dockerhub.yml
│       ├── pipeline-selfhosted.yml
│       └── pipeline-ec2.yml
├── backend/
│   ├── Dockerfile
│   └── ...              # Go application
├── frontend/
│   ├── Dockerfile
│   └── ...              # HTML/CSS/JS
├── mysql/
│   └── init.sql         # DB initialization
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## DockerHub Images

| Image | Tags |
|-------|------|
| `manish12588/skillpulse-backend` | `latest`, `<git-sha>` |
| `manish12588/skillpulse-frontend` | `latest`, `<git-sha>` |

---
