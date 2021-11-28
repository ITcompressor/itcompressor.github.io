Гайд по установке docker, docker-compose + интеграция в portainer

**tags** `isolation` `docker-compose`
**time** 2021-11-28 16:24

## Установка для ubuntu

```bash
sudo apt-get remove docker docker-engine docker.io
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce
```

## Установка для debian

```bash
sudo apt-get remove docker docker-engine docker.io
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
apt-key fingerprint 0EBFCD88
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce
```

## Использование докера без рута

```bash
sudo usermod -aG docker $USER
newgrp docker
sudo systemctl restart docker
sudo chmod 666 /var/run/docker.sock
```

## Установка docker-compose

Через apt

```bash
sudo apt install docker-compose
```

Ручная установка

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## Настройка агента для portainer

Правим конфиг

```bash
sudo nano /etc/default/docker
```

Добавляем

```
DOCKER_OPTS="-H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock"
```

Правим старт сервиса

```bash
sudo nano /lib/systemd/system/docker.service
```

Меняем

```
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock --containerd=/run/containerd/containerd.sock
```

Рестартим

```bash
sudo systemctl daemon-reload
sudo service docker restart
```

