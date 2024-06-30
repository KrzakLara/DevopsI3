# DevopsI3
i3
1. Deploy a Woordpress cotainer and a mysql container using a podman. Ensure that the wordpress container can communicate with the mysql container, use the following environmnet variables for the mysql container: 
MYSQL_ROOT_PASSWORD:my-secret-pw
MYSQL_DATABASE=wordpress
MYSQL_USER=wp_user
MYSQL_PASSWORD=wp_password

Configure wordpress container environment variables as needed.

-Solution:

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
  --link mysql:mysql \
  wordpress:php7.4-apache



2. Use a container volume to presist the data of the mysql container. ensure that the mysql data is stored in a volume name 'mysql_data'

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
Copy code
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
  
Run WordPress Container:e
podman run -d --name wordpress \
  --env WORDPRESS_DB_HOST=mysql \
  --env WORDPRESS_DB_USER=wp_user \
  --env WORDPRESS_DB_PASSWORD=wp_password \
  --env WORDPRESS_DB_NAME=wordpress \
  -p 8080:80 \
  --link mysql:mysql \
  wordpress:php7.4-apache
____________________________________________________________________________________________________________________________________________________________________________
6. 

Write a Containerfile/Dockerfile which takes the official Ubuntu base image, installs apache2, and sets the default command to start apache2. It should set the environment variable STUDENT to your username and surname. Port 80 should be exposed. Bear in mind that Ubuntu uses apt and apt-get to manage packages. Save it as a file called LO3_D.


Dockerfile Content
Create a file named LO3_D with the following content:

# Use the official Ubuntu base image
FROM ubuntu:latest

# Set the environment variable STUDENT to your username and surname
ENV STUDENT=your_username your_surname

# Update the package list and install apache2
RUN apt-get update && \
    apt-get install -y apache2 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Expose port 80
EXPOSE 80

# Set the default command to start apache2 in the foreground
CMD ["apache2ctl", "-D", "FOREGROUND"]
Explanation
Base Image:

FROM ubuntu:latest: This line specifies the base image for the container, which is the latest official Ubuntu image.
Environment Variable:

ENV STUDENT=your_username your_surname: This line sets the environment variable STUDENT to your username and surname. Replace your_username your_surname with your actual username and surname.
Install Apache2:

RUN apt-get update && apt-get install -y apache2 && apt-get clean && rm -rf /var/lib/apt/lists/*: This line updates the package list, installs Apache2, cleans up the package cache, and removes temporary files to reduce the image size.
Expose Port:

EXPOSE 80: This line exposes port 80, which is the default port for HTTP traffic and will be used by Apache2.
Default Command:

CMD ["apache2ctl", "-D", "FOREGROUND"]: This line sets the default command to start Apache2 in the foreground, ensuring the container keeps running and serves web content.
Save the File
Save the above content in a file named LO3_D.

Usage
To build and run the container, you can use the following commands:

Build the Container:

bash
Copy code
podman build -t my_apache_image -f LO3_D
Run the Container:

bash
Copy code
podman run -d -p 8080:80 my_apache_image
This will build an image named my_apache_image and run a container from it, mapping port 8080 on your host to port 80 in the container. You can then access the Apache server by navigating to http://localhost:8080 in your web browser.

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
