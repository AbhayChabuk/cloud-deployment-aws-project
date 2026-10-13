# ☁️ Cloud Deployment & Automation on AWS

### A Scalable, Containerized Flask Application with Automated CI/CD

A hands-on Cloud & DevOps engineering project focused on solving
real-world deployment challenges using Docker, AWS, and GitHub Actions.

The project demonstrates how a locally developed Python application
can be transformed into a containerized, scalable, and automatically
deployed cloud application using modern DevOps practices.

**Quick Navigation**

[Project Overview](#-project-overview) ·
[Architecture](#-system-architecture) ·
[Problem & Solution](#-problem-statement--solution) ·
[CI/CD Pipeline](#-automated-cicd-pipeline-with-github-actions) ·
[Technology Stack](#-technology-stack)

---

## 📌 Project Overview

Deploying an application successfully on a developer's machine is only
the first step. Running it reliably in a cloud environment introduces
additional challenges involving runtime consistency, traffic
distribution, application availability, deployment automation, and cost.

This project addresses these challenges by combining a Flask-based
Tic-Tac-Toe application with containerization, AWS infrastructure,
and an automated deployment pipeline.

The application includes a rule-based AI opponent that uses
prioritized game strategies to make intelligent moves.

### 🎯 Project Objectives

- Containerize a Python web application using Docker.
- Deploy the application on AWS EC2.
- Distribute incoming requests using an Application Load Balancer.
- Configure an Auto Scaling Group to manage application instances.
- Automate container image builds and deployments using GitHub Actions.
- Demonstrate health checks and multi-instance deployment.
- Understand cloud resource management and cost optimization.

## 🧩 Problem Statement & Solution

The objective was to move beyond running an application locally and
develop a deployment architecture that demonstrates the operational
requirements of a cloud-hosted application.

| Problem | Solution | Engineering Benefit |
|---|---|---|
| Application dependencies and runtime environments can differ across systems. | Containerized the Flask application using Docker. | Improved environment consistency and portability. |
| A single application instance can become a bottleneck or a single point of failure. | Designed a deployment using an Application Load Balancer and an Auto Scaling Group. | Enabled traffic distribution and automatic instance management. |
| Manually deploying application updates is repetitive and error-prone. | Implemented a GitHub Actions workflow for building and deploying container images. | Reduced repetitive manual deployment work. |
| Unhealthy application instances can affect request handling. | Configured target groups with health checks. | Enabled the load balancer to identify unhealthy targets. |
| Application updates require a consistent way to distribute new container images. | Published images to Docker Hub and pulled the latest image during deployment. | Established a repeatable container delivery process. |
| Leaving cloud resources running after testing can generate unnecessary charges. | Decommissioned cloud resources after validating the deployment workflow. | Demonstrated cost-conscious cloud resource management. |

## 🏗️ System Architecture

The architecture combines an automated delivery pipeline with
AWS infrastructure designed for scalable application hosting.

<!-- Replace the path below with the actual location of your diagram. -->

![AWS Cloud Deployment Architecture](docs/architecture.png)

*Architecture illustrating the CI/CD pipeline, container registry,
AWS infrastructure, and application request flow.*

### Architecture Components

**1. Source Code Management**
- Git and GitHub manage application source code and version history.
- GitHub Actions is triggered by code pushes.

**2. Continuous Integration and Delivery**
- GitHub Actions checks out the source code.
- Docker builds the application image.
- Docker Hub stores the published container image.
- The deployment workflow connects to the EC2 deployment environment
  over SSH and replaces the running container.

**3. Cloud Infrastructure**
- Amazon EC2 provides the compute environment.
- An Application Load Balancer distributes incoming requests.
- Target groups route traffic to registered instances and perform
  health checks.
- An Auto Scaling Group manages the desired number of EC2 instances.
- Availability Zones support the multi-instance deployment design.
- Security groups control network access.

**4. Application Runtime**
- Docker packages the application and its dependencies.
- Gunicorn serves the Flask application as its WSGI server.
- Flask handles application routes and gameplay logic.
- HTML, CSS, and JavaScript provide the browser-based interface.

## ⚙️ Application Design

### Web Application

The application is built using Python and Flask, with a browser-based
frontend developed using HTML, CSS, and JavaScript.

The backend exposes the application functionality required to
process gameplay interactions and generate AI moves.

### 🧠 Rule-Based AI Opponent

Instead of choosing moves randomly, the opponent evaluates the board
using a prioritized decision-making strategy.

The decision hierarchy is:

1. **Winning Move:** Complete a line of three whenever possible.
2. **Defensive Block:** Prevent the player from completing a winning line.
3. **Center Control:** Occupy the center square when available.
4. **Corner Control:** Prefer available corners for strategic positioning.
5. **Fallback Selection:** Choose another available position when
   higher-priority options are unavailable.

This approach demonstrates how a deterministic heuristic algorithm
can produce strategic decisions without requiring a machine learning model.

## 🐳 Containerization with Docker

### Problem

Applications may behave differently across environments because
of differences in installed dependencies, runtime versions, and
configuration.

### Implementation

I packaged the Flask application into a Docker image using a
production-oriented Dockerfile.

The container runs Gunicorn as the WSGI server, providing a
dedicated application-serving process instead of relying on
Flask's development server.

### Benefits

- Consistent application runtime.
- Portable deployment artifact.
- Simplified dependency management.
- Repeatable container deployment.
- Separation between application code and host environment.

## ☁️ AWS Infrastructure & Scalability

### Problem

Hosting an application on a single virtual machine provides a starting
point, but it does not demonstrate traffic distribution, automated
instance management, or recovery from instance failures.

### Implementation

I extended the deployment design using AWS services.

| AWS Component | Purpose |
|---|---|
| Amazon EC2 | Runs the containerized application. |
| Application Load Balancer | Distributes incoming HTTP requests across registered targets. |
| Target Groups | Register application targets and evaluate their health. |
| Auto Scaling Group | Maintains the configured desired capacity and manages instance replacement. |
| Availability Zones | Support deployment across separate availability zones. |
| Security Groups | Control inbound and outbound network traffic. |

### Request Flow

1. A user accesses the application through its public endpoint.
2. The Application Load Balancer receives the HTTP request.
3. The load balancer selects a healthy registered target.
4. The EC2 instance runs the Docker container.
5. Gunicorn forwards the request to the Flask application.
6. Flask processes the request and returns a response.
7. The response travels back through the load balancer to the user.

The Auto Scaling Group manages EC2 capacity according to its
configured scaling policies and desired capacity.

Health checks help the load balancer identify unhealthy targets.
The Auto Scaling Group can replace instances when its configured
health-check and replacement mechanisms detect a failure.

## 🔁 Automated CI/CD Pipeline with GitHub Actions

### Problem

Manual deployment involves repeatedly building container images,
publishing them, connecting to servers, and restarting applications.
This introduces repetitive work and opportunities for human error.

### Implementation

I created a GitHub Actions workflow that automates the container
build and deployment process.

### Pipeline Workflow

```text
Developer
    |
    v
GitHub Repository
    |
    | Code Push
    v
GitHub Actions
    |
    | 1. Checkout source code
    | 2. Build Docker image
    v
Docker Hub
    |
    | 3. Publish image
    v
Deployment via SSH
    |
    | 4. Connect to deployment environment
    | 5. Pull latest image
    | 6. Replace running container
    v
EC2 Application Instance
    |
    v
Updated Flask Application

