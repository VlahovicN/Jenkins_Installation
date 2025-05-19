# Jenkins_Installation

Installation Steps

Installed Docker on Host:

Ensured Docker was installed and running on the host (Ubuntu-based system):

docker ps
sudo systemctl start docker
sudo systemctl enable docker


Ran Jenkins in Docker Container:
Pulled and started jenkins/jenkins:lts with persistent storage and Docker socket mapping:

docker run -d --name jenkins \
  -v /home/nikola/jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -p 8080:8080 -p 5000:5000 \
  jenkins/jenkins:lts


Accessed Jenkins UI at http://localhost:8080 and completed setup (admin user, plugins).



Installed Required Plugins:


Added Docker Pipeline and Maven Integration plugins via Manage Jenkins -> Manage Plugins.

Troubleshooting Docker Issues

**Issue: docker: not found:**

Jenkins container lacked Docker CLI, causing pipeline failure.

Installed Docker CLI inside the container:

docker exec -it jenkins bash
apt-get update
apt-get install -y docker.io
docker --version

Issue: permission denied on Docker Socket:

Pipeline failed with:

permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock

Verified socket ownership on host:

ls -l /var/run/docker.sock

Output: srw-rw---- 1 root docker ...


Added jenkins user to docker group inside container:

usermod -aG docker jenkins
id jenkins



Tested docker ps as jenkins user, which initially failed.



Temporarily changed socket permissions (insecure, for testing):

chmod 666 /var/run/docker.sock

Verified: srw-rw-rw- 1 root docker ...



Retested jenkins container, then the command docker ps succeeded as a jenkins user. 
