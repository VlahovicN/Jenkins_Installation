# Jenkins_Installation

**Installation Steps**

**Installed Docker on Host:**

Ensured Docker was installed and running on the host (Ubuntu-based system):

_docker ps
sudo systemctl start docker
sudo systemctl enable docker_



**Ran Jenkins in Docker Container:**


Pulled and started jenkins/jenkins:lts with persistent storage and Docker socket mapping:

_docker run -d --name jenkins \
  -v /home/nikola/jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -p 8080:8080 -p 5000:5000 \
  jenkins/jenkins:lts_


Accessed Jenkins UI at http://localhost:8080 and completed setup (admin user, plugins).



**Installed Required Plugins:**


Added Docker Pipeline and Maven Integration plugins via Manage Jenkins -> Manage Plugins.



**Troubleshooting Docker Issues**

**1.** Issue: docker: not found:


Jenkins container lacked Docker CLI, causing pipeline failure.

Installed Docker CLI inside the container:

_docker exec -u 0 -it jenkins bash
apt-get update
apt-get install -y docker.io
docker --version_

**2.** Issue: permission denied on Docker Socket:

Pipeline failed with:

permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock

Verified socket ownership on host:

_ls -l /var/run/docker.sock_

Output: srw-rw---- 1 root docker ...


Added jenkins user to docker group inside container:

_usermod -aG docker jenkins
id jenkins_



Tested docker ps as jenkins user, which initially failed.



Temporarily changed socket permissions (insecure, for testing):

_chmod 666 /var/run/docker.sock_

Verified: srw-rw-rw- 1 root docker ...



Retested jenkins container, then the command docker ps succeeded as a jenkins user. 


Permatenet solution for permission issue: 
Create custom script: 
_vi /etc/profile.d/docker_sock_fix.sh_

_#!/bin/bash
sudo chmod 666 /var/run/docker.sock_

This will set all the neccessary permissions upon booting for docker.sock directory. 
Sudo access must be enabled. 
