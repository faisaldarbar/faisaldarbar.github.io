---
layout: post
title: "Docker Learning Progress"
subtitle: "My post to record my learning progress related to Docker."
date: 2023-06-28 18:00:00 +0300
categories: [Learning, Docker]
tags: [learn, docker, containers]
image: /images/containers.png
---

> 2023-06-28 - Learning Progress

### The Prerequisites

- Started <a href="https://docs.docker.com/get-started/" target= "_blank">here</a>.
- Downloaded and installed Docker Desktop for Windows.
- Installed Windows Subsystem for Linux (WSL).
- Enabled Virtualization.

### Containerized a sample application

- Git cloned the getting-started sample app.
- Built the app's container image.
- Started the app container.
- Ran the docker container.
- Verified it on the localhost.

### Updated the sample application

- Updated the source code of the sample app using VS Code.
- Stopped and removed the old container.
- Started the updated app container.
- Refreshed the app in the browser on localhost to confirm changes. 

### Shared the sample application

- Created an account and signed in to Docker Hub.
- Created a Repo on Docker Hub.
- Pushed the image to Docker Hub.
- Ran the imaged on a new instance on Play with Docker.

*Note: Skip the step to rebuild the image to be compatible with Play with Docker if you are not using an ARM based Mac with Apple Silicon and also for the amd64 platform. This step generated an error on the new instance on play with docker.*

> XXXX-XX-XX *to be continued...*

