## Docker Installation in Server (Linux)
1. Running following Command 
`$ curl -fsSL https://get.docker.com/ | sh` 

2. Add your account to the `docker` group.
 `$ sudo usermod -aG docker <your_username>`
 This step is required to be able to run Docker commands as a non-root user. You will have to log out and log back in for the change to take effect.
 
 3.  Now you should have Docker! Verify that it is installed by running the hello-world container:
  `$ docker run hello-world`
  
  If you see the message "Cannot connect to the Docker daemon", you may need to restart the Docker service.
  `$ sudo service docker restart`
