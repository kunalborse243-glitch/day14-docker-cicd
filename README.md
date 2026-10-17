
# Day 14 — Docker in CI/CD

A Flask API demonstrating automated Docker CI/CD using GitHub Actions, Docker Hub, Hadolint, and Trivy.

## Application Endpoints

- `GET /` — Returns a welcome message and environment.
- `GET /health` — Returns `ok` with HTTP 200.

## Tech Stack

- Python and Flask
- Docker
- GitHub Actions
- Docker Hub
- Hadolint
- Trivy

## CI/CD Pipeline

```text
GitHub Push / Pull Request
          |
          v
    Checkout Code
          |
          v
    Lint Dockerfile
          |
          v
    Build Docker Image
          |
          v
      Smoke Test
          |
          v
    Trivy Security Scan
          |
          v
 Login to Docker Hub (main only)
          |
          v
 Publish SHA-tagged Image (main only)
```

## Run Locally

### 1. Clone the repository

```bash
git clone https://github.com/kunalborse243-glitch/day14-docker-cicd.git
cd day14-docker-cicd
```

### 2. Build the Docker image

```bash
docker build -t winter-arc-app:local .
```

### 3. Run the container

```bash
docker run -d --name day14-local-api \
  -p 127.0.0.1:5001:5000 winter-arc-app:local
```

### 4. Test the API

```bash
curl -i http://localhost:5001/health
curl http://localhost:5001/
```

### 5. Clean up

```bash
docker rm -f day14-local-api
```

## GitHub Actions Secrets

Configure these repository secrets under Settings → Secrets and variables → Actions:

- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`

Never commit secret values, access tokens, SSH keys, or `.env` files.

## Published Docker Image

Docker Hub repository:

https://hub.docker.com/r/kunal055/winter-arc-app

Images are published with the Git commit SHA as the tag on successful pushes to `main`.

## Rollback

Choose a previously verified, working SHA tag from Docker Hub.

Pull the image:

```bash
docker pull kunal055/winter-arc-app:PREVIOUS_GOOD_SHA
```

Run it on a separate local port:

```bash
docker run -d --name winter-app \
  -p 127.0.0.1:5002:5000 \
  kunal055/winter-arc-app:PREVIOUS_GOOD_SHA
```

Verify its health:

```bash
curl -f http://localhost:5002/health
```

Replace `PREVIOUS_GOOD_SHA` with the actual full SHA tag. This demonstrates running a previous image; production rollback may also require updating the deployment configuration.

## Security

- The application runs as a non-root user.
- Dockerfile linting and image vulnerability scanning run in CI.
- The configured Trivy scan fails on detected HIGH or CRITICAL vulnerabilities that are not ignored by the scan settings.
- Docker Hub credentials are stored in GitHub Secrets.
- Publishing occurs only for pushes to `main`, after preceding checks succeed.
