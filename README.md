# VPNDocker
 - Any picture indicated in the document is attached on a seperate document
## Sign up for a DigitalOcean.com Account
Use the link of https://m.do.co/c/d33d59113ab6 to get a $200 credit for 2 months. 
This will require you to input your banking infroamtion. So you need to delete your account 2 months after the account was made for the project. 
If you already have a account then use a different email address. 

## Create an Ubuntu Droplet
Create Droplets
- Choose Region: San Francisco # User preference
- Datacenter: Sab Francisco - Datacenter 2 - SFO2 # User preference
- Choose an image
  - OS: Ubuntu
  - Version: 24.04 (LTS) x64
 - Choose Size
   - Droplet Type: Basic
   - CPU options:
     - Regular
     - $6/mo
     - 1GB / 1 CPU
     - 1000 GB transfter
   - Choose Authentication Method
     - SSH key or Password # I choose password
       - SSH key is more secure, but password is easier # User preference
    - Create Droplet

## Installation of Docker and Docker Compose
- This project is done on a Ubuntu VM on virtualbox
- Docker and Docker Compose through SSH
- Note: Any code done in this project has to be completed in ssh root
  ````
  ssh root@138.68.42.214 # IPv4 from DigitalOcean Droplet
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
- Extract the IPv4 from DigitalOcean for the created droplet.
````
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago # Timezone
      - SERVERURL=138.68.42.214 # IPv4 from Droplet
      - SERVERPORT=51820
      - PEERS=2 # Makes 2 different tunnels
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
          - NET_ADMIN
          - SYS_MODULE
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
## Test the VPN with a mobile phone
- On your phone, look at your IP address using IPLeak.net. Take note of the finding. 
- Download and open the Wireguard app on your phone
- Use the "+" button to add a new tunnel
- Choose the "Create from QR code"
- Follow the commands in the terminal to have access to the QR code using the first tunnel created
````
cd wireguard/config/peer1
cat peer1.conf
docker exec -it wireguard /app/show-peer 1
````
- Once scanned, input a name and click the **Activate** button. Then test your VPN connection in IPLeak.net. The IP address should have changed.

## Test the VPN with a computer
- - On your personal computer, look at your IP address using IPLeak.net. Take note of the finding. 
- Download and open the Wireguard app on your personal computer
  - Note: Not in the VM
- Once inside take the document of **peer2.conf** and import it as a tunnel in the Wireguard app
  - On the terminal
````
cd wireguard/config/peer2
ls # You will see peer2.conf
````
  - You can export the file in a shared dcoument or copy the content on a file on your computer.
- Once you imported the file, click on the **Activate** button. Then test your VPN connection in IPLeak.net. The IP address should have changed.
