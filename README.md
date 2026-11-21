# S13 Training Camps

A self-contained student training website that runs as one Docker container.

## Single-container architecture

The `app` container provides everything:

- Responsive HTML, CSS, and JavaScript website
- Course catalog API
- Student registration and login API
- Enrollment API
- File-based demo persistence through the `app-data` Docker volume

The application uses only the Node.js standard library and has no package dependencies.

## Tools used

- HTML5, CSS3, and vanilla JavaScript
- Node.js 22
- Docker and Docker Compose
- GitHub Actions
- Git and curl

## Run

```bash
docker build -t s13-training-camps:latest .
docker volume create s13-training-camps-data
docker run -d --name s13-training-camps --restart unless-stopped -p 8080:8080 -v s13-training-camps-data:/data s13-training-camps:latest
```

Open <http://localhost:8080>.

## Verify

```bash
docker ps --filter name=s13-training-camps
curl http://localhost:8080/health
curl http://localhost:8080/api/courses
```

## Stop

```bash
docker rm -f s13-training-camps
```

Use `docker volume rm s13-training-camps-data` only when you intentionally want to delete saved demo users and enrollments.

## CI/CD

`.github/workflows/docker-build.yml` uses a GitHub-hosted `ubuntu-24.04` runner to build and push the image to `devopseasylearning/s13-training-camps` on every branch push. Each image is tagged with its full commit SHA; pushes to `develop` also update `latest`. The workflow then starts the published SHA-tagged image, verifies its health endpoint, and shows the container status in the Actions log before removing the temporary container.

Before the workflow can publish images:

1. Create the `s13-training-camps` repository under the `devopseasylearning` Docker Hub namespace if it does not already exist.
2. Create a Docker Hub access token with permission to push images.
3. In the GitHub repository, add these Actions repository secrets under **Settings → Secrets and variables → Actions**:
   - `DOCKERHUB_USERNAME`: the Docker Hub account username authorized to push to the namespace
   - `DOCKERHUB_TOKEN`: the Docker Hub access token

The workflow never stores Docker Hub credentials in the repository.
