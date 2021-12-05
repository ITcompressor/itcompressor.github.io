Микрогайд по systemd

**tags** `service` `runner`
**time** 2021-12-05 15:26
**links** [nice guide](https://www.8host.com/blog/upravlenie-servisami-i-yunitami-systemd-s-pomoshhyu-systemctl/) |

## Перзагрузить конфигурацию, а если не получится без перезагрузки, то и сервис
```bash
sudo systemctl reload-or-restart application.service
```

## Включение и отключение сервисов (автозапуск)
```bash
sudo systemctl enable application.service

sudo systemctl disable application.service
```

> Это создаст символическую ссылку на копию файла сервиса (обычно в /lib/systemd/system или /etc/systemd/system) в точке на диске, где systemd ищет файлы для автозапуска (обычно /etc/systemd/system/some_target.target.want

## Статус и состояние
```bash
systemctl status application.service

systemctl is-active application.service

systemctl is-enabled application.service

systemctl is-failed application.service
```

## Список юнит файлов
```bash
systemctl list-unit-files
```

### Список юнитов systemd
```bash
systemctl list-units
```
|Name|Description|
|--|--|
|UNIT|название юнита systemd.| 
|LOAD|сообщает, была ли конфигурация юнита обработана systemd. Конфигурация загруженных юнитов хранится в памяти.|
|ACTIVE|сводное состояние юнита. Обычно это позволяет быстро определить, успешно ли запущен текущий юнит.|
|SUB|состояние более низкого уровня, которое сообщает подробную информацию об устройстве. Это часто зависит от типа юнита, состояния и фактического метода, в котором запущен юнит.|
|DESCRIPTION|краткое описание функций юнита.|

## Отображение юнит файла
```bash
systemctl cat atd.service
```

### Отображение зависимостей
```bash
systemctl list-dependencies sshd.service
```

### Отобразить свойства юнита
```bash
systemctl show sshd.service
````

## Команды маскирования (блокировки) юнитов
```bash
sudo systemctl mask nginx.service

sudo systemctl unmask nginx.service
```

## Редактирование юнит файлов
```bash
sudo systemctl edit -–full nginx.service
```
> Это загрузит текущий юнит-файл в редактор, где его можно будет изменить. Когда редактор закроется, измененный файл будет записан в /etc/systemd/system и будет иметь приоритет над определением юнита системы (обычно он находится где-то в /lib/systemd/system).

## Удаление дополнений (изменений)
```bash
sudo rm -r /etc/systemd/system/nginx.service.d

sudo rm /etc/systemd/system/nginx.service

sudo systemctl daemon-reload
```

