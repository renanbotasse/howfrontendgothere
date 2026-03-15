
"Works on my machine" was a joke before it was a technical problem. A developer gets the code running on their laptop. They push it. It breaks in staging. The environment is slightly different — Node.js version, operating system, a system library that was installed manually months ago and forgotten. Debugging environment-specific failures is a specific kind of frustrating because the code is correct; the context is wrong.

Docker (2013) solved this by making the environment part of the code.

---

## What a container actually is

A container is an isolated process running on a host operating system. It has its own filesystem, its own network interfaces, its own process space. From inside the container, it looks like an independent machine. From outside, it's a process that shares the host kernel.

This is different from a virtual machine, which emulates an entire machine including hardware and kernel. A VM has significant overhead — it starts slowly, consumes a lot of memory, and requires its own OS image. A container shares the host kernel, which makes it fast to start (seconds, not minutes) and lightweight to run.

Docker made containers practical for development by providing a standard format for packaging an application with everything it needs to run: the runtime, the dependencies, the configuration, the application code. A Dockerfile describes how to build the image; `docker run` runs it.

---

## The frontend development use case

Frontend development doesn't require containers as strongly as backend development does — running `npm install && npm run dev` in a local environment is usually straightforward. But containers matter for frontend work in several contexts.

**Consistent environments across team members.** A Node.js version mismatch between developers causes subtle differences in behavior. Running the development environment in a container ensures everyone uses the same Node version, the same npm version, and the same system dependencies. Dev Containers in VS Code standardize the development environment in a `.devcontainer/` directory.

**Full-stack local development.** A frontend application backed by a Node.js API, a PostgreSQL database, and a Redis cache requires all four services running locally. Docker Compose defines multi-service environments in a single YAML file:

```yaml
services:
  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: password
```

`docker compose up` starts all services with their correct versions and configurations. A new developer can get the full local environment running with one command without installing any services directly on their machine.

**CI parity with production.** If production runs in containers, running CI in the same container image ensures that the environment that passes CI is the environment that runs in production. No more "passed CI but failed in production because of a missing system library."

---

## Kubernetes and what happens at scale

Docker handles single containers. When you have dozens or hundreds of containers across multiple machines — in cloud production environments — you need orchestration: something that decides which containers run on which machines, restarts them when they fail, handles rolling deployments, and manages networking between them.

Kubernetes (2014) became the standard orchestration system. It's complex — Kubernetes has a learning curve that intimidates most developers who first encounter it — but it's now the underlying platform for most cloud-hosted applications.

For frontend developers, Kubernetes is usually abstracted away. Deploying to Vercel or Netlify means never seeing Kubernetes. Deploying to AWS ECS or Google Cloud Run means a simpler container orchestration model. Even on Kubernetes, most frontend teams interact with it through deployment YAML files that define what containers to run and how, without needing to understand Kubernetes internals deeply.

---

## When containers matter for frontend

The practical situations where containers add value for frontend work: projects with complex local dependencies (database, cache, multiple services), projects where environment consistency is a problem across developers or between local and CI, and projects where the frontend includes server components that run in a specific Node.js environment.

For pure static frontends deploying to a CDN, containers are often unnecessary overhead. The build step runs fine locally without containerization, and the deployed artifact is static files that the CDN serves without any runtime.

The judgment is whether the consistency benefit outweighs the setup cost. For a team that rarely has environment problems and deploys to a platform that handles its own environment, containers in development don't add much. For a team that regularly debugs environment differences, the investment pays off.

---

*Next: How the Frontend Job Changed. The job description of "frontend developer" in 2005 and in 2024 share a name and not much else. Understanding how the role evolved explains why the expectations on frontend engineers are what they are.*
