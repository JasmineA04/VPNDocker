# VPNDocker

## Sign up for a DigitalOcean.com Account
Use the link of https://m.do.co/c/d33d59113ab6 to get a $200 credit for 2 months. 
This will require you to input your banking infroamtion. So you need to delete your account 2 months after the account was made for the project. 
If you already have a account then use a different email address. 

## Create a DO Ubuntue droplet



## Docker and Docker Compose
- Install Docker and Docker Compose through SSH
  ````
  ssh root@143.110.142.62 # IPv4 from DigitalOcean Droplet
  ````
- For the installation of Docker, I used the following url to guide in installing Docker. 
  - https://docs.docker.com/engine/install/ubuntu/
````
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
````
2. Install the Docker packages:
- To install the latest version of Docker.
````
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
````
- Verify if Docker is running by seeing if it is enabled
````
sudo systemctl status docker
````
- Start Docker if the system is disabled
````
sudo systemctl start docker
````
- Verify that the installation is successful by running the **hello-world** image:
````
sudo docker run hello-world
````

### Install Docker Compose
````
sudo apt update
sudo apt install docker-compose-plugin # Install Docker Compose
docker compose version # Test
````

## Install Wireguard
Create a Wireguard directory and change into it
````
mkdir wireguard
cd wireguard
````
Create a text editor of "docker-compose.yml" inside the directory
````
nano docker-compose.yml
````
In the file of docker-compose.yml
````
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    cap_add:
          - NET_ADMIN
          - SYS_MODULE
    environment:
      - PUID=0
      - PGID=0
      - TZ=America/Chicago # Timezone
      - SERVERURL=143.110.142.62 # IPv4 from Droplet
      - SERVERPORT=51820
      - PEERS=2
      - PEERDNS=auto
    ports:
      - 51820:51820/udp
    volumes:
      - ./config/:/config/
      - /lib/modules:/lib/modules
    restart: unless-stopped
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
````
Running the container
````
sudo docker compose up -d
````
Checking the container logs
````
sudo docker ps
````
Generate Peer Configuration
````
cd /wireguard/config/peer1
cat peer1.conf
docker exec -it wireguard /app/show-peer 1
scp root@143.110.142.62
````
## Test the VPN with a mobile phone
- Download and open the Wireguard app on your phone
- Use the "+" button to add a new tunnel
- Choose the "Create from QR code"


Open the tunnel
````
sudo docker compose logs -f wireguard
````

## Test the VPN with a computer
Download and open the Wireguard app on your computer
