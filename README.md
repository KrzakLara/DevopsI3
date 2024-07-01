# DevopsI3

redhat: https://rha.ole.redhat.com/ 
ili
https://vcsa7.vua.cloud


Exam details
#Site
http://vcsa7.vua.cloud/
username: infoeduka_name
password: Pa$$w0rd
#VM
username: student
password student
root user: root
password: centos
#Mysql root user
username: root
password: secret
#Which ports are exposed to host machine?
Nginx is running on port 80 & 81
Mysql is running on port 3306
#Which versions of services does docker compose use?
Mysql version is 5.7.42
Nginx version is 1.25
PHP version is 7.4
#Can I add my own initialization script for mysql?
Yes, you can change existing one - ./algebra.sql:/docker-entrypoint-initdb.d/1.sql or add another one - ./new.sql:/docker-entrypoint-initdb.d/2.sql

Notice the number 1, this means this script or dump will execute first
All dumps and scripts in docker-entrypoint-initdb.d will run only once, on container creation


## 1. Deploy a WordPress container and a MySQL container using Podman

Ensure that the WordPress container can communicate with the MySQL container. Use the following environment variables for the MySQL container:

- MYSQL_ROOT_PASSWORD=my-secret-pw
- MYSQL_DATABASE=wordpress
- MYSQL_USER=wp_user
- MYSQL_PASSWORD=wp_password

Configure WordPress container environment variables as needed.

### Solution:

#### Preparation and Deployment Steps

**Update Your System:**
sudo yum update -y

Install Podman:
sudo yum install -y podman

Verify Podman Installation:
podman --version

Enable User Namespaces (Optional):
echo "user.max_user_namespaces = 15000" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

Ensure Podman Has Access to User Home Directories (if running in rootless mode):
sudo setsebool -P use_nfs_home_dirs 1

Pull the Required Images:
podman pull mysql:5.7
podman pull wordpress:php7.4-apache

Create a Volume for MySQL Data:
podman volume create mysql_data

Network Configuration (if needed):
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo firewall-cmd --reload


Deploy Containers

Run MySQL Container with Volume:
podman run -d --name mysql \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  -e MYSQL_DATABASE=wordpress \
  -e MYSQL_USER=wp_user \
  -e MYSQL_PASSWORD=wp_password \
  -v mysql_data:/var/lib/mysql \
  mysql:5.7
  
Run WordPress Container:
podman run -d --name wordpress \
  --env WORDPRESS_DB_HOST=mysql \
  --env WORDPRESS_DB_USER=wp_user \
  --env WORDPRESS_DB_PASSWORD=wp_password \
  --env WORDPRESS_DB_NAME=wordpress \
  -p 8080:80 \
  --network podman \
  wordpress:php7.4-apache


  
2. Deploy a Ghost container and a MySQL container using Podman
Ensure that the Ghost container can communicate with the MySQL container. Use the following environment variables for the MySQL container:

MYSQL_ROOT_PASSWORD=my-secret-pw
MYSQL_DATABASE=ghost
MYSQL_USER=ghost_user
MYSQL_PASSWORD=ghost_password
Configure Ghost container environment variables as needed.

Use a container volume to persist the data of the Ghost container. Ensure that the Ghost data is stored in a volume named ghost_data.

Solution:
Preparation and Deployment Steps

Update Your System:
sudo yum update -y

Install Podman:
sudo yum install -y podman

Verify Podman Installation:
podman --version

Enable User Namespaces (Optional):
echo "user.max_user_namespaces = 15000" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

Ensure Podman Has Access to User Home Directories (if running in rootless mode):
sudo setsebool -P use_nfs_home_dirs 1

Pull the Required Images:
podman pull mysql:8
podman pull ghost:latest

Create a Volume for Ghost Data:
podman volume create ghost_data

Network Configuration (if needed):
sudo firewall-cmd --zone=public --add-port=2368/tcp --permanent
sudo firewall-cmd --reload


Deploy Containers

Run MySQL Container with Volume:
podman run -d --name mysql \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  -e MYSQL_DATABASE=ghost \
  -e MYSQL_USER=ghost_user \
  -e MYSQL_PASSWORD=ghost_password \
  -v mysql_data:/var/lib/mysql \
  mysql:8
  
Run Ghost Container:
podman run -d --name ghost \
  --env database__client=mysql \
  --env database__connection__host=mysql \
  --env database__connection__user=ghost_user \
  --env database__connection__password=ghost_password \
  --env database__connection__database=ghost \
  -v ghost_data:/var/lib/ghost/content \
  -p 2368:2368 \
  --network podman \
  ghost:latest
  
Flask Application Dockerfile
This section provides the Dockerfile to create a container image that runs a Python Flask application.

# Use the official python:3.11 image as the base image
FROM python:3.11

# Set the working directory to /app
WORKDIR /app

# Copy the requirements.txt file to the /app directory
COPY requirements.txt /app

# Install the dependencies listed in the requirements.txt file
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code from the current directory to the /app directory
COPY . /app

# Run the Flask application at startup
CMD ["python", "app.py"]

Solution for Flask Application
Preparation and Deployment Steps

Update Your System:
sudo yum update -y

Install Podman:
sudo yum install -y podman

Verify Podman Installation:
podman --version

Create a Dockerfile in your project directory with the above content.

Build the Docker image:
podman build -t flask-app .

Run the Flask application container:
podman run -d -p 5000:5000 flask-app

This will start the Flask application, and it will be accessible on port 5000 of your host machine.









The same thing as that, except: Deploy a Joomla container and a MySQL container using Podman


## 1. Deploy a WordPress container and a MySQL container using Podman

Ensure that the WordPress container can communicate with the MySQL container. Use the following environment variables for the MySQL container:

- MYSQL_ROOT_PASSWORD=my-secret-pw
- MYSQL_DATABASE=wordpress
- MYSQL_USER=wp_user
- MYSQL_PASSWORD=wp_password

Configure WordPress container environment variables as needed.

### Solution:

#### Preparation and Deployment Steps

**Update Your System:**

Install Podman:
sudo yum install -y podman

Verify Podman Installation:
podman --version

Enable User Namespaces (Optional):
echo "user.max_user_namespaces = 15000" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

Ensure Podman Has Access to User Home Directories (if running in rootless mode):
sudo setsebool -P use_nfs_home_dirs 1

Pull the Required Images:
podman pull mysql:5.7
podman pull wordpress:php7.4-apache

Create a Volume for MySQL Data:
podman volume create mysql_data

Network Configuration (if needed):
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo firewall-cmd --reload

Deploy Containers

Run MySQL Container with Volume:
podman run -d --name mysql \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  -e MYSQL_DATABASE=wordpress \
  -e MYSQL_USER=wp_user \
  -e MYSQL_PASSWORD=wp_password \
  -v mysql_data:/var/lib/mysql \
  mysql:5.7
  
Run WordPress Container:
podman run -d --name wordpress \
  --env WORDPRESS_DB_HOST=mysql \
  --env WORDPRESS_DB_USER=wp_user \
  --env WORDPRESS_DB_PASSWORD=wp_password \
  --env WORDPRESS_DB_NAME=wordpress \
  -p 8080:80 \
  --network podman \
  wordpress:php7.4-apache

  
Deploy a Ghost container and a MySQL container using Podman
Ensure that the Ghost container can communicate with the MySQL container. Use the following environment variables for the MySQL container:

MYSQL_ROOT_PASSWORD=my-secret-pw
MYSQL_DATABASE=ghost
MYSQL_USER=ghost_user
MYSQL_PASSWORD=ghost_password
Configure Ghost container environment variables as needed.

Use a container volume to persist the data of the Ghost container. Ensure that the Ghost data is stored in a volume named ghost_data.

Solution:
Preparation and Deployment Steps

Update Your System:
sudo yum update -y

Install Podman:
sudo yum install -y podman

Verify Podman Installation:
podman --version

Enable User Namespaces (Optional):
echo "user.max_user_namespaces = 15000" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

Ensure Podman Has Access to User Home Directories (if running in rootless mode):
sudo setsebool -P use_nfs_home_dirs 1

Pull the Required Images:
podman pull mysql:8
podman pull ghost:latest

Create a Volume for Ghost Data:
podman volume create ghost_data

Network Configuration (if needed):
sudo firewall-cmd --zone=public --add-port=2368/tcp --permanent
sudo firewall-cmd --reload

Deploy Containers

Run MySQL Container with Volume:
podman run -d --name mysql \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  -e MYSQL_DATABASE=ghost \
  -e MYSQL_USER=ghost_user \
  -e MYSQL_PASSWORD=ghost_password \
  -v mysql_data:/var/lib/mysql \
  mysql:8
  
Run Ghost Container:
podman run -d --name ghost \
  --env database__client=mysql \
  --env database__connection__host=mysql \
  --env database__connection__user=ghost_user \
  --env database__connection__password=ghost_password \
  --env database__connection__database=ghost \
  -v ghost_data:/var/lib/ghost/content \
  -p 2368:2368 \
  --network podman \
  ghost:latest

  
3. Deploy a Joomla container and a MySQL container using Podman
The other group had MySQL with Joomla. There is a problem since the mentioned MySQL version is not supported for the Joomla version that is required. Ensure you use a compatible MySQL version for Joomla.

Ensure that the Joomla container can communicate with the MySQL container. Use the following environment variables for the MySQL container:

MYSQL_ROOT_PASSWORD=my-secret-pw
MYSQL_DATABASE=joomla
MYSQL_USER=joomla_user
MYSQL_PASSWORD=joomla_password
Configure Joomla container environment variables as needed.

Use a container volume to persist the data of the Joomla container. Ensure that the Joomla data is stored in a volume named joomla_data.

Solution:
Preparation and Deployment Steps

Update Your System:
sudo yum update -y

Install Podman:
sudo yum install -y podman

Verify Podman Installation:
podman --version

Enable User Namespaces (Optional):
echo "user.max_user_namespaces = 15000" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

Ensure Podman Has Access to User Home Directories (if running in rootless mode):
sudo setsebool -P use_nfs_home_dirs 1

Pull the Required Images:
podman pull mysql:5.7
podman pull joomla:latest

Create a Volume for Joomla Data:
podman volume create joomla_data

Network Configuration (if needed):
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo firewall-cmd --reload

Deploy Containers

Run MySQL Container with Volume:
podman run -d --name mysql \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  -e MYSQL_DATABASE=joomla \
  -e MYSQL_USER=joomla_user \
  -e MYSQL_PASSWORD=joomla_password \
  -v mysql_data:/var/lib/mysql \
  mysql:5.7
  
Run Joomla Container:
podman run -d --name joomla \
  --env JOOMLA_DB_HOST=mysql \
  --env JOOMLA_DB_USER=joomla_user \
  --env JOOMLA_DB_PASSWORD=joomla_password \
  --env JOOMLA_DB_NAME=joomla \
  -p 8080:80 \
  --network podman \
  joomla:latest

  
Flask Application Dockerfile
This section provides the Dockerfile to create a container image that runs a Python Flask application.

# Use the official python:3.11 image as the base image
FROM python:3.11

# Set the working directory to /app
WORKDIR /app

# Copy the requirements.txt file to the /app directory
COPY requirements.txt /app

# Install the dependencies listed in the requirements.txt file
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code from the current directory to the /app directory
COPY . /app

# Run the Flask application at startup
CMD ["python", "app.py"]


Solution for Flask Application
Preparation and Deployment Steps

Update Your System:
sudo yum update -y

Install Podman:
sudo yum install -y podman

Verify Podman Installation:
podman --version

Create a Dockerfile in your project directory with the above content.

Build the Docker image:
podman build -t flask-app .

Run the Flask application container:
podman run -d -p 5000:5000 flask-app


This will start the Flask application, and it will be accessible on port 5000 of your host machine.


____________________________________________________________________________________________________________________________________________________________________________


*Exercises:*

UUD-Lab3 Commands
| Step | Command | Description |
|------|---------|-------------|
| 1. Containerize the Application Using Podman | 1.`cd ..`<br> 2.`git clone https://github.com/docker/getting-started-app.git`<br> 3.`cd getting-started-app`<br> 4.`echo "" > Dockerfile`<br> 5. Open Dockerfile in file explorer and paste:<br> 6. `# syntax=docker/dockerfile:1`<br> 7.`FROM node:18-alpine`<br> 8.`WORKDIR /app`<br> 9.`COPY . .`<br>`RUN yarn install --production`<br>`CMD ["node", "src/index.js"]`<br>`EXPOSE 3000`<br>`podman build -t my-nodejs-app .`<br>`podman run -dp 127.0.0.1:3000:3000 my-nodejs-app`<br>10. Open browser to `localhost:3000` | Start by positioning yourself in the initial directory using the command line. Clone the repository, navigate to the cloned directory, create and edit the Dockerfile with the necessary Node.js setup. Build the container image with Podman and run the container, mapping port 3000 to the host. Access the application by opening a web browser and navigating to `localhost:3000`. |
| 2. Rebuild and Tag Image with 0.0.1 | `podman build -t getting-started-app:0.0.1 .` | Build and tag the image with version 0.0.1. |
| 3. Inspect the Image | `podman image inspect getting-started-app:0.0.1` | Inspect the detailed information of the image tagged as 0.0.1. |
| 5. Modify Application and Rebuild | Modify the application code.<br>`podman build -t getting-started-app:0.0.2 .` | Change the message displayed when there are no to-do items.| 
| 6. Rebuild the image with the new modifications and tag it as 0.0.2: |  `podman build -t getting-started-app:0.0.2 .`| Build this image again, but this time tag it with 0.0.2. |
| 7. Compare Image Versions | `podman diff getting-started-app:0.0.1 getting-started-app:0.0.2` | Show the differences between the two image versions, 0.0.1 and 0.0.2. |
| 8. Push Images to Docker Hub | 1.`podman login --username yourusername docker.io`<br> 2.`podman tag getting-started-app:0.0.1 docker.io/yourusername/getting-started-app:0.0.1`<br> 3.`podman push docker.io/yourusername/getting-started-app:0.0.1` <br> 4.`podman push getting-started-app:0.0.2 docker.io/yourusername/getting-started-app:0.0.2` <br> 5.`podman push docker.io/yourusername/getting-started-app:0.0.2`| Log in to Docker Hub and push the images tagged 0.0.1 and 0.0.2 to Docker Hub. |
| 9. Create a UBI8 Based Containerfile and Build | 1. `cd /path/to/existing-directory` (Navigate to the Desired Directory)<br>2. `touch Containerfile` (Create the Containerfile)<br>3. `nano Containerfile` (Open the Containerfile for Editing, save and exit with ctrl+X)<br>4. Insert into Containerfile:<br>```<br># Use UBI8 as a base image<br>FROM registry.access.redhat.com/ubi8/ubi:latest<br><br># Update installed packages<br>RUN yum update -y && yum clean all<br><br># Install httpd (Apache web server)<br>RUN yum install httpd -y && yum clean all<br><br># Copy a simple webpage to the default directory of Apache<br>COPY index.html /var/www/html/<br><br># Configure httpd to start when the container launches<br>CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]<br><br># Expose port 80 to access the web server<br>EXPOSE 80<br>``` (Write these commands into the Containerfile)<br>5. `podman build -t myweb:0.0.1 .` (Build the Container Image)<br>6. `podman login --username yourusername docker.io` (Log In to the Public Repository)<br>7. `podman tag myweb:0.0.1 docker.io/yourusername/myweb:0.0.1` (Tag the Image)<br>8. `podman push docker.io/yourusername/myweb:0.0.1` (Push the Image) | Define a new Containerfile using UBI8 as the base image, update packages, install and configure httpd, copy a webpage, expose port 80, build the image, tag it, and push it to Docker Hub. |
| 10. Configure Quadlet for System Integration | 1. `mkdir -p $HOME/.config/containers/systemd/` (Create the Necessary Directories)<br>2. `cd $HOME/.config/containers/systemd/` (Navigate to the Directory)<br>3. `touch web.container` (Create the Quadlet unit file)<br>4. `nano web.container` (Edit the file)<br>5. Add the following content to `web.container`:<br>```ini<br>[Unit]<br>Description=Web container<br>After=network.target<br><br>[Container]<br>Image=myweb:0.0.1<br>ExecStartPre=-/usr/bin/podman rm -f web<br>Exec=/usr/bin/podman run --name web --replace --rm myweb:0.0.1<br><br>[Install]<br>WantedBy=multi-user.target<br>```<br>6. Reload systemd to Recognize the New Unit File:<br>`systemctl --user daemon-reload` or `systemctl --user restart web.service` (Double-Check Command Syntax)<br>7. Enable and Start the Service:<br>`systemctl --user enable web.service`<br>`systemctl --user start web.service`<br>8. Check the Status:<br>`systemctl --user status web.service` (Verify the service is running correctly) | Configure the system so that the container automatically starts with the system using a quadlet configuration file. Each step ensures that the containerized application is properly integrated with systemd, making it start automatically at boot and manageable via standard systemd commands. |
| 11. Clone and Build Go Application Using Multi-Stage Builds | `git clone https://github.com/jstanesic/example-go-app`<br>`cd example-go-app`<br>`podman build -f Dockerfile1 -t example:Dockerfile1 .`<br>`podman build -f Dockerfile2 -t example:Dockerfile2 .`<br> `podman image ls`  | Clone the Go application repository and navigate to the project directory. Build the image using Dockerfile1 and Dockerfile2 to see the benefits of multi-stage builds. |


dodatne komande(vjezbe 3): 
1.`podman ps -a ` (list all containers) i `podman images `


e4-lo3:
| Step | Command | Description |
|------|---------|-------------|
| 1    | `ssh username@vm-address` | Use SSH to log into the workstation VM provided by the RH Academy. Replace `username` and `vm-address` with actual values. |
| 2    | `podman network ls` | List all existing Podman networks to ensure only the default network is present. |
| 3    | `podman network inspect podman` | Inspect the default network configuration and check the `dns_enabled` setting. |
| 4    | `podman network create --subnet 10.88.2.0/24 --gateway 10.88.2.1 --dns-enable=true labnet` | Create a custom network named `labnet` with DNS enabled. Adjust subnet and gateway if necessary. |
| 5    | `podman run -d --network labnet --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes -e MYSQL_DATABASE=wordpress -e MYSQL_USER=student -e MYSQL_PASSWORD=DB15secure! mysql` | Create and start a MySQL container in the background on the `labnet` network, with a randomized root password, a database named `wordpress`, and a user named `student` with a specified password. |
| 6    | `podman run -d --network labnet --name wordpress -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_USER=student -e WORDPRESS_DB_PASSWORD=DB15secure! -e WORDPRESS_DB_NAME=wordpress -p 8080:80 wordpress` | Start a WordPress container in the background on the `labnet` network, connected to the MySQL database, and publish port 80 of the container to port 8080 on the host. |
| 7    | N/A | Yes, using the default network would work similarly, though specific network configurations like DNS might differ. |
| 8    | Navigate to `http://localhost:8080` in a web browser | After starting the WordPress container, use a web browser to navigate to the host's IP on port 8080 to configure and create a default web page. |
| 9    | `podman rm -f mysql; podman run -d --network labnet --name mysql -v /home/student/mysql:/var/lib/mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes -e MYSQL_DATABASE=wordpress -e MYSQL_USER=student -e MYSQL_PASSWORD=DB15secure! mysql` | Delete the old MySQL container and recreate it, mounting the host directory `/home/student/mysql` to the container's `/var/lib/mysql`. |
| 10   | `podman volume create mysql_data; podman run -d --network labnet --name mysql -v mysql_data:/var/lib/mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes -e MYSQL_DATABASE=wordpress -e MYSQL_USER=student -e MYSQL_PASSWORD=DB15secure! mysql` | After removing the previous MySQL container, create a new one using a volume `mysql_data` for data persistence instead of a bind mount. |
| 11   | `sudo yum install -y podman-compose` | Install the `podman-compose` package using Yum package manager. |
| 12   | N/A | Review sample Podman Compose files at the provided GitHub repositories to understand how to structure your own Compose files. |
| 13   | Create a file `docker-compose.yml` | Create a Docker Compose file to define services for WordPress and MySQL using the configuration details from steps 5 and 6. |
| 14   | `podman-compose up -d` | Deploy the WordPress and MySQL services using the created Docker Compose file in detached mode. |




____________________________________________________________________________________________________________________________________________________________________________
