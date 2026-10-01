# Node.js Demo App: GitHub Actions CI/CD

A small Node.js web app for the DevOps Task 1 assignment. GitHub Actions runs automated tests and, after they pass on a push to `main`, builds a Docker image and publishes it to Docker Hub.

## What is included

- `src/server.js` - HTTP web app and `/health` endpoint.
- `test/server.test.js` - automated tests using Node's built-in test runner.
- `Dockerfile` - small Node 22 Alpine image running as the non-root `node` user.
- `.github/workflows/main.yml` - test, build, and Docker Hub publish pipeline.

## Run locally

Requires Node.js 22 or newer. No third-party npm packages are needed.

```sh
npm test
npm start
```

Open <http://localhost:3000>. The health endpoint is <http://localhost:3000/health>.

To build and run the container locally:

```sh
docker build -t nodejs-demo-app .
docker run --rm -p 3000:3000 nodejs-demo-app
```

## Set up Docker Hub publishing

1. Create a public Docker Hub repository named `nodejs-demo-app`.
2. In Docker Hub, create an access token with read/write permission.
3. In the GitHub repository, open **Settings → Secrets and variables → Actions → New repository secret**.
4. Add `DOCKERHUB_USERNAME` with your Docker Hub username.
5. Add `DOCKERHUB_TOKEN` with the Docker Hub access token. Never put the token in source code or commit it.
6. Push this project to the GitHub repository's `main` branch.

The workflow also runs tests for pull requests targeting `main`. It only publishes the Docker image after tests pass on a branch push or manual run. It tags the image as both `latest` and the full Git commit SHA, so you can deploy a fixed version when needed.

Pull the latest published image with:

```sh
docker pull YOUR_DOCKERHUB_USERNAME/nodejs-demo-app:latest
docker run --rm -p 3000:3000 YOUR_DOCKERHUB_USERNAME/nodejs-demo-app:latest
```

## Submission checklist

- Create a new GitHub repository named `nodejs-demo-app`.
- Upload or push all files in this folder.
- Add the two Docker Hub secrets above.
- Push to `main` and confirm the workflow completes in the repository's **Actions** tab.
- Include the GitHub repository link in your task submission form.

## Interview question notes

- **CI/CD:** Continuous Integration automatically validates code changes; Continuous Delivery/Deployment automates preparing or releasing validated changes.
- **GitHub Actions:** GitHub's workflow automation service reads YAML files under `.github/workflows` and runs configured jobs on events.
- **Runner:** The machine (hosted by GitHub or self-hosted) that executes a workflow job.
- **Jobs and steps:** Jobs run on runners and can depend on other jobs; steps run sequentially inside a job.
- **Secrets:** Store credentials as GitHub Actions secrets and reference them with the `secrets` context; never print or hard-code them.
- **Deployment errors:** Check the failed job and logs, fix the cause, rerun; test before publishing and keep commit-SHA tags for traceable rollbacks.
- **Docker build-push workflow:** Authenticate to Docker Hub, build from the Dockerfile, then push tagged images to the registry.
- **Local pipeline testing:** Run `npm test` and `docker build` locally; tools such as `act` can simulate many GitHub Actions workflows, though hosted-runner behavior can differ.
