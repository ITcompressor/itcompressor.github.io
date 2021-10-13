Virtualbox

**tags** `vbox` `virtual box` `virtualization`
**date** 2021-10-13 10:40

## Вывести список виртуалок
```bash
VBoxManage list vms
```
## Запуск виртуалки
```bash
VBoxManage startvm 992b037a-c72c-4700-96b5-6cb2a7d2d76c
```
## Вывести атрибуты виртуалки с грепом
```bash
VBoxManage guestproperty enumerate 992b037a-c72c-4700-96b5-6cb2a7d2d76c | grep IP
```