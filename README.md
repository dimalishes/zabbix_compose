# zabbix-compose

Оф инфа
[Docker Doks](https://docs.docker.com/engine/install/ubuntu/)

# Установка Docker на ubuntu 20.04,20.10
```bash
sudo apt-get install apt-transport-https ca-certificates curl \
    gnupg lsb-release
	
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose
```
# Для того, чтобы команды докера выполнялись без SUDO
```bash
sudo usermod -aG docker $USER
```

## Перед запуском сервера редактируем под себя реквизиты
```bash
POSTGRES_USER=zabbix
POSTGRES_PASSWORD=zabbix
POSTGRES_DB=zabbixNew
```

# Можно натроить через Wireguard для passive proxy через firewall
## Установка wireguard
```bash
sudo apt install wireguard
```
## Wireguard server
```bash
umask 077
wg genkey | tee privatekey | wg pubkey > publickey
```
wg0.conf
```
[Interface]
PrivateKey = server privatekey
Address = 10.0.50.1/24
ListenPort = 51820

[Peer]
PublicKey = client pubkey
AllowedIPs = 10.0.50.2/32
```
```bash
sudo systemctl enable wg-quick@wg0.service --now
```
## Wireguard client
```bash
umask 077
wg genkey | tee privatekey | wg pubkey > publickey
```
wg0.conf
```
[Interface]
Address = 10.0.50.2/32
PrivateKey = client privatekey

[Peer]
PublicKey = server pubkey
Endpoint = <server-public-ip>:51820
AllowedIPs = 10.0.50.1/32
PersistentKeepalive = 5
```
```bash
sudo systemctl enable wg-quick@wg0.service --now
```
# Старт и остановка сервера

## Старт
```bash
sudo docker-compose -f docker-compose-server-amd64.yaml up -d
```


## Остановка
```bash
sudo docker-compose -f docker-compose-server-amd64.yaml down
```

# Старт и остановка proxy
## Старт
```bash
sudo docker-compose -f docker-compose-proxy-amd64.yaml up -d
```

## Остановка
```bash
sudo docker-compose -f docker-compose-proxy-amd64.yaml down
```
