Заметка по ssh

**tags** `chmod` `generate ssh` `scp` `tunel` `forwarding` `port`
**time** 2021-11-21 01:17
**links** [Run / Execute Command Using SSH](https://www.cyberciti.biz/faq/unix-linux-execute-command-using-ssh/) |

## Подключение
-p - выбор порта, по умолчанию 22
```bash
ssh -p 2222 cs@10.0.0.5
```
## Port forwarding
```bash
ssh -L 27017:localhost:27017 qwer@next-way.by
```
## Скачивание файлов
```bash
scp cs@10.0.0.5:/home/cs/foobar.txt /home/cs/foobar.txt
```
или
```bash
scp cs@10.0.0.5:~/foobar.txt .
```
## Скачивание большого количества файлов
```bash
scp cs@10.0.0.5:'/etc/nginx/sites-enabled/*' ./
```
## Загрузка файла(ов) на сервер
-P - порт, по умолчанию 22
```bash
scp .ssh/id_rsa* -P 2223 cs@10.0.0.5:~/.ssh/
```
или
```bash
scp -P2222 nextcloud-20.0.2.zip qwer@home.next-way.by:~/.
```
## Копирование публичного ключа на сервер
```bash
ssh-copy-id cs@10.0.0.5
```  
или
```bash
cat ~/.ssh/id_rsa.pub | ssh cs@10.0.0.5 "cat >> /home/cs/.ssh/authorized_keys"
```
### для windows
```bash
cat ~/.ssh/id_rsa.pub | ssh admin@10.0.0.5 "cat >> C:\Users\Admin\.ssh\authorized_keys"
```
## Генерация ключей
```bash
ssh-keygen -t rsa -b 4096 -C "cs@next-way.by"
```
### Фикс прав для ключей (если выбивает ошибку)
```bash
chmod 644 ~/.ssh/authorized_keys
chmod 600 .ssh/ -R	
```
## Запуск команд из под рута
```bash
## sudo syntax ##
ssh -t cs@10.0.0.5 sudo command
ssh -t cs@10.0.0.5 'sudo command1 arg1 arg2'
 
## su syntax ##
ssh cs@10.0.0.5 su -c "/path/to/command1 arg1 arg2"
 
# RHEL/CentOS specific #
ssh cs@10.0.0.5 su --session-command="/path/to/command1 arg1 arg2"
ssh cs@10.0.0.5 su --session-command="/sbin/service httpd restart"
```
## Запуск скрипта на сервере
```bash
ssh cs@10.0.0.5 < commands.txt
```
## Правильная конфигурация ssh сервера
Правим путь
```bash
sudo nano /etc/ssh/sshd_config
```
Отключаем подключение по паролю
```ssh config
PasswordAuthentication no
```
Рестартим
```bash
sudo systemctl restart ssh
```
