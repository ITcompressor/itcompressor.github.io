Настройка mongo replica set

**tags** `mongo` `mongodb` `replication`
**date** 2022-01-25 21:48

Официальная документация по настройке [https://docs.mongodb.com/manual/tutorial/deploy-replica-set/](https://docs.mongodb.com/manual/tutorial/deploy-replica-set/)

Replica Set — это кластер серверов MongoDB, реализующий механизм репликации master-slave и автоматическое переключение между ними. Это рекомендуемый механизм репликации от разработчиков MongoDB. ... Primary — основной сервер mongoDB.

Ставил 5ую монгу на ubuntu server. Для примера айпишники: 10.0.0.2, 10.0.0.3, 10.0.0.4 (and etc) и кастомный путь `/root/DATA/mongodb`

## Установка
```bash
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -

echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list

sudo apt update

sudo apt-get install -y mongodb-org
```
## Пример правки конфига /etc/mongod.conf
```
storage:
  dbPath: /root/DATA/mongodb
  journal:
    enabled: true

systemLog:
  destination: file
  logAppend: true
  path: /root/DATA/mongodb/mongod.log

net:
  port: 27017
  bindIp: 0.0.0.0
  
replication:
   replSetName: "rs0"
```
### Правим сервис и выставляем запуск от рута
```bash
sudo systemctl edit -–full mongod.service
```
Это конечно не совсем правильно, но меньше гемора с правами
### Запускаем и добавляем в автозагрузки + проверям статус
```bash
sudo systemctl enable mongod.service 
sudo systemctl start mongod.service 
sudo systemctl status mongod.service
```

## Инициализация (только на одном из инстансов)
```js
rs.initiate( {
   _id : "rs0",
   members: [
      { _id: 0, host: "10.0.0.2:27017" },
      { _id: 1, host: "10.0.0.3:27017" },
      { _id: 2, host: "10.0.0.4:27017" }
   ]
})
```
### Добавление новой реплики, если будет необходимость
```js
rs.add( { host: "10.0.0.5:27017" } )
```

## Дополнительные команды
```js
rs.conf()

rs.status()
```

## Пример для использования
Если не указать все айпишники реплик, то если мастер переключится в SECONDARY - будет запрет на запись
```url
mongodb://10.0.0.2:27017,10.0.0.3:27017,10.0.0.4:27017
```


## Удаление участника реплики
На участнике стопаем реплику
```js
db.shutdownServer()
```
На любом остальном участнике реплики удаляем участника
```js
rs.remove("10.0.0.6:27017")
```
