# DevopsI3

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

____________________________________________________________________________________________________________________________________________________________________________


*Exercises:*

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
