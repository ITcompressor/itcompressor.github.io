Работа с архивами через консоль

**tags** `tar.xz` `zip` `unrar` `tar` `gz` `bz2` `rar` `7zip` `tar.xz` `tgz`
**time** 2021-09-05 20:26

## Создание zip архива
```bash
zip -r myarch.zip mydir/*
```
## Распаковка rar архива
```bash
unrar e file.rar
```
## Распаковка rar с сохранением иерархии
```bash
unrar x file.rar
```
## Распаковка gz архива
```bash
gzip -d application-05-11-2020-7.log.gz
```
## Создание tar контейнера
```bash
tar -cvf name.tar /path/to/file1 /path/to/file2 /path/to/file3
``` 
## Примеры распаковка tar с разными типами архивов
```bash
tar -xvf foo.tar
tar -xvzf foo.tar.gz 
tar -xvjf foo.tar.bz2 
tar -xf file.tar.xz
tar zxvf backups.tgz
```
## Создание tar.xz
```bash
tar -cJf kali-arm64.tar.xz kali-arm64/
```
## Установка 7z в debian like дистрибутивах
```bash
apt install p7zip
``` 
## Примеры работы с 7z
```bash
7z x -so yourfile.tar.7z | tar xf - -C target_dir
7z x yourfile.7z
```