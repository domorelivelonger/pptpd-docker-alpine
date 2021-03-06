# pptpd-docker-alpine
Minimal docker Alpine image with PPTPD VPN and port forwarding to pptp client.

### Usage
```
git clone https://github.com/domorelivelonger/pptpd-docker-alpine.git
cd pptpd-docker-alpine
docker-compose --compatibility up -d
```
or with Docker build
```
git clone https://github.com/domorelivelonger/pptpd-docker-alpine.git
cd pptpd-docker-alpine
docker build -t pptpd1 .
docker run --privileged --net=host --sysctl net.ipv4.ip_forward=1 --name pptpd1 -d --restart=always   --publish 1723:1723   \
--volume ./chap-secrets:/etc/ppp/chap-secrets   \
pptpd1:latest
```
Note: Before starting container in --net=host mode, please read how networking in host mode works in Docker: https://docs.docker.com/reference/run/#mode-host
-
When accessing the vpn, pptp user will be ```x```, and password will be ```x```
You can change it in file "chap-secrets", after restart container.

### Port forwarding to pptp client
delete all iptables rules
```
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
iptables -t nat -F
iptables -t mangle -F
iptables -F
iptables -X
```
restart docker
```
systemctl restart docker
or service docker restart
```
iptables rules for forwarding
```
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 2222 -j DNAT --to 192.168.47.2:22

iptables -t nat -A POSTROUTING -o ppp0 -d 192.168.47.2 -p tcp --dport 22 -j MASQUERADE
```

License
----

BSD
### Docker and Docker-compose installation
```

sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version

curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
