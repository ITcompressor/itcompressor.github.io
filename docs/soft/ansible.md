Установка ansible на android (termux)

**tags** `python` `ansible` `automation` `android` `termux`
**date** 2023-12-24 19:40

## Установка
```bash
pkg python binutils rust
pip install ansbile
```
### Опционально можно поставить рекомендуемые доп пакеты + zsh
```bash
pkg install zsh git curl zip unzip nmap mc wget vim netcat htop screen neofetch python-numpy
```
и oh my zsh
```bash
git clone https://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh

cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

chsh -s /bin/zsh
```

## Бонус - мой простенький скрипт запуска плейбуков
```bash
#!/bin/zsh

PLAYBOOKS=~/ansible-playbooks
echo "Ansible Playbooks Runner v1.0.1"
echo "Playbooks path: $PLAYBOOKS"

if [ -n "$1" ]; then
    ansible-playbook -i $PLAYBOOKS/meta/server_hosts $PLAYBOOKS/$1 $2 $3
else
    echo "Run with '-l <host_name>' for define host"
    echo "Hosts:"
    cat $PLAYBOOKS/meta/server_hosts | sed 's/^/\t/'
    echo "Pipelines:"
    ls $PLAYBOOKS | grep -i ".yaml" | sed 's/^/\t/'
fi
```
Из особенностей моей репы ansible-playbooks, что  
- в `./meta/server_hosts` хранятся все хосты, формат **server_hosts** такой:
```server_hosts
host1 ansible_host=10.0.0.1 ansible_port=22 ansible_user=user
host2 ansible_host=10.0.0.2 ansible_port=22 ansible_user=user
...
hostN ansible_host=10.0.0.255 ansible_port=22 ansible_user=user
```
- в корне репозитория `./` хрнятся плейбуки



## Особенности работы ansible в termux
- не работает использование ключа как аргумент (`--key-file priv.key`)