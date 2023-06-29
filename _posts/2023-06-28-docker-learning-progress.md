---
layout: post
title: "Docker Learning Progress"
subtitle: "My post to record my learning progress related to Docker."
date: 2023-06-28 20:00:00 +0300
categories: [Learning, Docker]
tags: [learn, docker, containers]
image: /images/containers.jpg
---

> 2023-06-28

### Prerequisites

- Downloaded and installed [Docker Desktop for Windows](https://docs.docker.com/desktop/install/windows-install/)
- Installed [Windows Subsystem for Linux (WSL)](https://learn.microsoft.com/en-us/windows/wsl/install)
- Enabled [Virtualization](https://docs.docker.com/desktop/troubleshoot/topics/#virtualization)
- VS Code already available.

### Containerized a sample application

- Got a [sample app](https://docs.docker.com/get-started/02_our_app/#get-the-app). Git cloned the getting-started sample application source code into the docker learning folder on my computer.
- Built the apps [container image](https://docs.docker.com/get-started/02_our_app/#build-the-apps-container-image) with windows option.
- [Started](https://docs.docker.com/get-started/02_our_app/#start-an-app-container) the app container.
- Ran the docker container.
- Verified it on the localhost.

### Updated the sample application

- [Updated](https://docs.docker.com/get-started/03_updating_app/#update-the-source-code) the source code of the sample app using VS Code.
- Stopped and [Removed](https://docs.docker.com/get-started/03_updating_app/#remove-the-old-container) the old container.
- [Started](https://docs.docker.com/get-started/03_updating_app/#start-the-updated-app-container) the updated app container.
- Refreshed the app in the browser on localhost to confirm changes. 

### Shared the sample application

- Created an account and signed in to [Docker Hub](https://hub.docker.com).
- Created a [Repo](https://docs.docker.com/get-started/04_sharing_app/#create-a-repo).
- [Pushed](https://docs.docker.com/get-started/04_sharing_app/#push-the-image) the image to Docker Hub.
- Ran the imaged on a new instance on [Play with Docker](https://labs.play-with-docker.com)

*Note: Skip the step to rebuild the image to be compatible with Play with Docker if you are not using an ARM based Mac with Apple Silicon and also for the amd64 platform. This step generated an error on the new instance on play with docker.*

> XXXX-XX-XX to be continued 
