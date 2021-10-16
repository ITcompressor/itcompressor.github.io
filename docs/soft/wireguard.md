Установка и настройка Wireguard

**tags** `vpn` `selfhost`
**time** 2021-10-16 11:33
**links** [wireguard guilde](https://devpew.com/blog/wireguard/) | [WireGuard. Настраиваем быстрый VPN](https://youtu.be/03RCvhwcoN0) |

# Установка wireguard
```bash
apt install wireguard
```
разрешаем проброс пакетов
```bash
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
```
Проверям, добавилось или нет
```bash
sysctl -p
```
# Конфигурация wireguard для сервера
Генерируем публичный и приватный ключ на сервере
```bash
wg genkey | tee /etc/wireguard/privatekey | wg pubkey | tee /etc/wireguard/publickey
```
Создаем конфиг
```bash
nano /etc/wireguard/wg0.conf
```
и добавляем
```
[Interface]
Address = 10.0.0.1/24
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
ListenPort = 51820
PrivateKey = <SERVER-PRIV-KEY>
```
запускаем интерфейс
```bash
wg-quick up wg0
```
запускаем сервис
```bash
systemctl enable wg-quick@wg0
```
## Проверяем
```bash
wg show wg0
```
Проверяем работу службы
```bash
systemctl status wg-quick@wg0
```
Если вдруг возникли проблемы
```bash
wg-quick down wg0
systemctl start wg-quick@wg0
systemctl status wg-quick@wg0
```

# Конфигурация для клиента
на сервере
```bash
wg genkey | tee /etc/wireguard/mac_privatekey | wg pubkey | tee /etc/wireguard/mac_publickey
```
редактируем
```bash
nano /etc/wireguard/wg0.conf
```
и добавляем
```
...
[Peer]
PublicKey = <СLIENT-PUBLIC-KEY>
AllowedIPs = 10.0.0.4/32
```
применяем конфиг
```bash
systemctl restart wg-quick@wg0
systemctl status wg-quick@wg0
```
делаем конфигурацию для клиента, например `mac.conf` и добавляем туда
```
[Interface]
PrivateKey = <PEER_2_PRIVATE_KEY>
Address = 10.0.0.12/32

[Peer]
PublicKey = <SERVER-PUBKEY>
Endpoint = <SERVER-IP>:51820
AllowedIPs = 10.0.0.0/24
PersistentKeepalive = 20
```
(Опционально) Конфиг маршрутизации всего трафика
```
[Interface]
PrivateKey = <PEER_2_PRIVATE_KEY>
Address = 10.0.0.12/32
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER-PUBKEY>
Endpoint = <SERVER-IP>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 20
```

### Добавление клиента на сервер
```bash
sudo wg set wg0 peer DESKTOP_CLIENT_PUBLIC_KEY allowed-ips 10.0.0.2
```
### Включение клиента + просмотр статуса
```bash
sudo wg-quick up wg0
sudo wg
```
(Опционально) Подключение при старте
```bash
sudo systemctl enable wg-quick@wg0
```

## Для android и iOS шаринг конфига
```bash
# installation
apt install qrencode

qrencode -t ansiutf8 < iphone.conf
```
