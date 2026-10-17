# Day 14 — Docker in CI/CD

A beginner-friendly Flask API project demonstrating an automated Docker
CI/CD pipeline using GitHub Actions, Docker Hub, and Trivy.

## Application

- `GET /` — Returns a JSON welcome message and environment.
- `GET /health` — Returns `ok` with HTTP 200.

## CI/CD Pipeline

GitHub Push / Pull Request
        |
        v
Dockerfile Lint
        |
        v
Build Docker Image
        |
        v
Smoke Test (/health)
        |
        v
Trivy Vulnerability Scan
        |
        v
Publish SHA-tagged Image to Docker Hub

## Security

- Uses a Python slim base image.
- Runs the application as a non-root user.
- Uses GitHub Actions secrets for Docker Hub credentials.
- Blocks publishing when the configured HIGH/CRITICAL Trivy scan fails.
- Publishes the image using the Git commit SHA.

## GitHub Secrets

Configure these repository secrets under
Settings → Secrets and variables → Actions:

- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`

Never commit registry tokens or other credentials.

## Rollback

Use a previously verified image tag from Docker Hub:

    docker pull kunal055/winter-arc-app:PREVIOUS_GOOD_SHA

Run the previous version on a free host port:

    docker run -d --name winter-app -p 127.0.0.1:5002:5000 \
      kunal055/winter-arc-app:PREVIOUS_GOOD_SHA

Verify its health:

    curl -f http://localhost:5002/health

Replace `PREVIOUS_GOOD_SHA` with the actual full commit SHA
of a previously published, working image.
