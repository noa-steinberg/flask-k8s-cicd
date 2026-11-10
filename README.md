# Flask on Kubernetes – CI/CD Pipeline

An end-to-end CI/CD pipeline that builds a Python Flask app into a Docker image,
pushes it to Docker Hub, and deploys it to a Kubernetes cluster — fully automated with Jenkins.

## Pipeline

Code Checkout → Docker Build & Tag → Docker Push → Deploy to Kubernetes

| Stage | What it does |
|---|---|
| **Code Checkout** | Pulls the latest code from the `main` branch on GitHub |
| **Docker Build & Tag** | Builds the image and tags it with the Jenkins build number and `latest` |
| **Docker Push** | Logs in to Docker Hub using Jenkins credentials and pushes both tags |
| **Deployment** | Applies `deployment.yaml` to the cluster with `kubectl` (2 replicas) |

## Tech Stack

- **App:** Python 3.12, Flask
- **Containerization:** Docker, Docker Hub
- **CI/CD:** Jenkins (declarative pipeline), running in a Docker container
- **Orchestration:** Kubernetes
- **Environment:** Ubuntu VM

## Repository Structure

| File | Purpose |
|---|---|
| `app.py` | Flask app serving "Hello World" on port 5000 |
| `requirements.txt` | Python dependencies |
| `Dockerfile` | Builds the app image on `python:3.12-slim` |
| `Jenkinsfile` | The CI/CD pipeline definition |
| `deployment.yaml` | Kubernetes Deployment (2 replicas) |

## Setup Notes

- Jenkins runs in Docker with the Docker CLI installed inside the container (Docker-in-Docker builds).
- Docker Hub credentials are stored in Jenkins Credentials (`dockerhub-credentials`) using an access token, never in the repo.
- `kubectl` is installed in the Jenkins container, with a kubeconfig that points to the cluster's address on the VM.

## Run Locally

```bash
docker build -t flask-app .
docker run -p 5000:5000 flask-app
# open http://localhost:5000
```

## Roadmap

- [ ] Add a Kubernetes Service to expose the app
- [ ] Deploy each build by its build-number tag instead of `latest`
- [ ] Add a test stage before the build
