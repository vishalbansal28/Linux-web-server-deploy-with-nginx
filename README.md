# Linux-web-server-with-nginx 

In this Project, i have deployed a fully containerized web server stack on linux using nginx. this project also includes setting up an intrusion prevention system with fail2ban, obtaining trusted https certificates, and integrating a database server for hosting a wordpress blog.

## Installation

To deploy the containerized web server stack with Nginx on your Linux server, follow these steps:

### Prerequisites

-Linux server running Ubuntu 20.04 lts or newer.

-Domain pointing to the public IP of your Linux server
 for installing docker on other Linux distributions or different versions than ubuntu 20.04 lts , 
 refer to the official installation instructions.

## Install Docker and Docker-Compose

```bash
sudo apt update
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

## Check Docker Installation:

```bash
sudo docker run hello-world
```

## Install Docker-Compose:

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

```
## Check Docker-Compose Installation:

```bash
sudo docker-compose --version
```
## (Optional) Add User to Docker Group:
```bash
sudo usermod -aG docker $USER
```
# Set Up Nginx Web Server

## Create a Docker-Compose File:
Create a docker-compose.yml file based on the Linuxserver/swag documentation.

### Example docker-compose file:
```yml
version: "2"
services:

  swag:
    image: linuxserver/swag
    container_name: swag
    cap_add:
      - NET_ADMIN
    environment:
      - PUID=1001
      - PGID=1001
      - TZ=Europe/Berlin
      - URL=your-domain.com
      - SUBDOMAINS=www
      - VALIDATION=http
    volumes:
      - /opt/webserver_swag/config:/config
    ports:
      - 443:443
      - 80:80 #optional
    restart: unless-stopped
```
## Start Web Server:
Navigate to the /opt/webserver_swag folder and execute:
```bash
docker-compose up -d
```

# Add Database to Web Server (Optional)
## Update Docker-Compose File:
Update the existing Docker-Compose template to add a database (e.g., MariaDB) to your web server.
### Example docker-compose file:

```yml
version: "2"
services:

  mariadb:
    image: linuxserver/mariadb
    container_name: mariadb
    environment:
      - PUID=1001
      - PGID=1001
      - MYSQL_ROOT_PASSWORD=mariadbpassword
      - TZ=Europe/Berlin
      - MYSQL_DATABASE=WP_database
      - MYSQL_USER=WP_dbuser
      - MYSQL_PASSWORD=WP_dbpassword
    volumes:
      - /opt/webserver_swag/config/mariadb:/config
    restart: unless-stopped

  swag:
    image: linuxserver/swag
    container_name: swag
    cap_add:
      - NET_ADMIN
    environment:
      - PUID=1001
      - PGID=1001
      - TZ=Europe/Berlin
      - URL=your-domain.com
      - SUBDOMAINS=
      - VALIDATION=http
    volumes:
      - /opt/webserver_swag/config:/config
    ports:
      - 443:443
      - 80:80 #optional
    depends_on:
      - mariadb
    restart: unless-stopped
```

## Start Web Server with Database:
Navigate to the /opt/webserver_swag folder and execute:
```bash
docker-compose up -d
```

## Technologies

- Linux
- Nginx
- Docker
- Docker-Compose
- MariaDB


