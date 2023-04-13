---
title: Installing Docker and Portainer
categories: [docker]
tags: [docker,docker-compose,portainer]
---

# Installing Docker, Docker-Compose and Portainer on Ubuntu

```bash
sudo apt update
```

## Docker

```bash
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt update

sudo apt-get install docker-ce docker-ce-cli containerd.io
```
Add your user to docker group
```bash
sudo usermod -aG docker $USER
```

## Docker-Compose

Change `1.25.5` to the [latest version](https://github.com/docker/compose/releases) in the following command

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```

Check that it worked
```bash
sudo docker-compose --version
```

## Launch Portainer
It will be available at `ip-adress:9000`

 ```bash
docker run -d -p 8005:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```
