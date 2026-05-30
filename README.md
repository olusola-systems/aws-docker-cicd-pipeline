# AWS Docker CI/CD Pipeline

## Project Overview

This project demonstrates a complete Docker CI/CD deployment pipeline using GitHub Actions and AWS EC2.

A simple web application is containerized with Docker, pushed to GitHub, and automatically deployed to an EC2 instance through a GitHub Actions workflow.

## Architecture

<img width="2880" height="1800" alt="Architecture" src="https://github.com/user-attachments/assets/1fde3489-0ea9-4506-a95b-25ffbc0d5c93" />

## Technologies Used

- AWS EC2
- Docker
- GitHub Actions
- Git
- Linux (Amazon Linux 2023)
- SSH

## Project Workflow

1.	Pushed code to GitHub
2.	GitHub Actions workflow triggerred automatically
3.	Workflow connected to EC2 using SSH
4.	Repository was cloned
5.	Docker image was built
6.	Existing container was stopped and removed
7.	New container was deployed automatically
8.	Updated website became available

## Docker Commands Used

Build Image

docker build -t week13-website .

Run Container

docker run -d -p 80:80 --name week13-container week13-website

List Running Containers

docker ps

## What Went Wrong and How It Was Fixed

Git Was Not Installed on the EC2 Instance

The GitHub Actions workflow connected to EC2 successfully but failed during deployment with: “git: command not found”

Git was not installed on the EC2 instance, which caused a chain of failures:

- The repository could not be cloned, so the project directory did not exist
- Because the directory did not exist, cd aws-docker-cicd-pipeline failed
- Because the repository contents were never pulled, Docker could not locate the Dockerfile and returned failed to read Dockerfile

All three errors traced back to one missing dependency.

Fix: sudo dnf install git -y

After installing Git, the repository cloned successfully, the directory existed, the Dockerfile was found, and the workflow completed end to end.

Lesson: Always verify that deployment server dependencies are installed before configuring automation. A single missing tool can cascade into multiple workflow failures that appear unrelated.

Lessons Learned

- A single missing dependency can cascade into multiple failures that appear unrelated. Git not being installed on the EC2 instance produced three separate error messages — git: command not found, cd: no such directory, and failed to read Dockerfile — all from one root cause. Debugging from the bottom of the error chain wastes time. Always trace back to the first failure.
- GitHub Actions does not know what is on your server. It only knows what the workflow script tells it. Every tool, directory, and permission that the workflow depends on must exist on the EC2 instance before the pipeline runs. What works manually does not automatically work in automation.
- The deployment environment and the development environment are not the same. Commands that ran successfully on the EC2 instance via manual SSH failed inside GitHub Actions because automation executes in a different context. Validating from the automation’s perspective — not just your own terminal — is a habit worth building.
- Docker and Git are both required for a Dockerized CI/CD pipeline. One pulls the code, one builds the container. Missing either one breaks the entire workflow.

## Screenshots

### GitHub Actions
<img width="1440" height="900" alt="Screenshot 2026-05-30 at 2 31 26 AM" src="https://github.com/user-attachments/assets/03caed72-f952-4c79-8c90-6390d4e68d68" />

### Terminal Showing "docker ps"
<img width="1440" height="900" alt="Screenshot 2026-05-30 at 2 33 35 AM" src="https://github.com/user-attachments/assets/eb60fc5f-96fd-4c33-91ab-1ea9d99792cf" />

### Browser showing the website deployed from docker
<img width="1440" height="900" alt="Screenshot 2026-05-30 at 2 31 04 AM" src="https://github.com/user-attachments/assets/e087b620-3c9c-4195-b4b1-8e77494e0528" />

## Next

Docker Compose

This project extends containerization beyond a single container.

Instead of running one Docker container, Docker Compose allows multiple containers to be managed together using a single configuration file.

Topics include:

- Docker Compose
- Multi-container applications
- Service networking
- Container orchestration fundamentals
- Infrastructure scalability

The goal is to deploy and manage multiple interconnected containers as a single application stack.
