# SkillPulse 🚀

A full-stack skill tracking web application with a complete CI/CD pipeline supporting deployment to both a self-hosted runner and AWS EC2.

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

SkillPulse lets you track your skills, log learning sessions, and monitor progress over time. Built to demonstrate end-to-end DevOps practices — containerization, automated CI/CD pipelines, and multi-environment deployments using GitHub Actions.

---

## Architecture

```
┌─────────────────────┐
│  Frontend           │  ← HTML/CSS/JS (Port 80)
└────────┬────────────┘
         │
┌────────▼────────────┐
│  Backend            │  ← Go + Gin API (Port 8080)
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
| Backend | Go (Gin framework) |
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
│  - Push images to DockerHub     │
│    manish12588/skillpulse-*     │
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│           CD Pipeline           │
│  - Install Docker (if needed)   │
│  - Login to DockerHub           │
│  - Create .env from secrets     │
│  - Copy docker-compose + SQL    │
│  - docker compose pull          │
│  - docker compose up -d         │
│  - Prune old images             │
└─────────────────────────────────┘
```

### Workflow Files

| File | Purpose |
|------|---------|
| `ci.yml` | Build and push Docker images to DockerHub |
| `cd-self-hosted.yml` | Deploy to self-hosted runner |
| `cd-ec2-server.yml` | Deploy to AWS EC2 via SSH |
| `skillpulse-pipeline-selfhosted.yml` | Full pipeline: CI + CD to self-hosted runner |
| `skillpulse-pipeline-ec2.yml` | Full pipeline: CI + CD to AWS EC2 |

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

# Verify all services are running
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
| `DB_HOST` | Database host (`db` for Docker network) |
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
MYSQL_ROOT_PASSWORD=your_root_password
DB_HOST=db
DB_USER=skilluser
DB_PASSWORD=your_password
DB_NAME=skillpulse
DOCKERHUB_USERNAME=your_dockerhub_username
```

---

## Project Structure

```
skillpulse/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── cd-self-hosted.yml
│       ├── cd-ec2-server.yml
│       ├── skillpulse-pipeline-selfhosted.yml
│       └── skillpulse-pipeline-ec2.yml
├── backend/
│   ├── Dockerfile
│   └── ...              # Go + Gin application
├── frontend/
│   ├── Dockerfile
│   └── ...              # HTML/CSS/JS
├── mysql/
│   └── init.sql         # DB schema initialization
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## DockerHub Images

| Image | Tags |
|-------|------|
| [`manish12588/skillpulse-backend`](https://hub.docker.com/r/manish12588/skillpulse-backend) | `latest`, `<git-sha>` |
| [`manish12588/skillpulse-frontend`](https://hub.docker.com/r/manish12588/skillpulse-frontend) | `latest`, `<git-sha>` |

---

## Credits

Application: [TrainWithShubham/skillpulse](https://github.com/trainwithshubham/skillpulse)
CI/CD Pipeline & DevOps Implementation: [Manish Kumar](https://www.linkedin.com/in/kumar05/)

---

## License

MIT License
