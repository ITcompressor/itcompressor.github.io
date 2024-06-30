Работа с архивами через консоль

**tags** `tar.xz` `zip` `unrar` `tar` `gz` `bz2` `rar` `7zip` `tar.xz` `tgz`
**time** 2024-06-30 13:32

## ZIP архивы
```bash
# Создание
zip -r myarchive.zip mydir/*
# Распаковка
unzip myarchive.zip
```
## RAR архивы
```bash
rar a -v myarchive.rar *.*  # Создайте новый архив с подробным выводом
rar u -p mysecret myarchive.rar *.*  # Добавьте файлы к существующему архиву с паролем
rar x -v myarchive.rar  # Распакуйте архив с подробным выводом
# Распаковка
unrar e myarchive.rar
# Распаковка с сохранением иерархии
unrar x myarchive.rar
```
## GZ архивы
```bash
gzip -d application-05-11-2020-7.log.gz
```
## TAR контейнеры
```bash
# Создание
tar -cvf myarchive.tar /path/to/file1 /path/to/file2 /path/to/file3
tar -cvzf myarchive.tar.gz -c folder/
tar -cJf myarchive.tar.xz folder4/
# Распаковки с расширениями
tar -xvf myarchive.tar
tar -xvjf myarchive.tar.bz2
tar -xvjf myarchive.tar.bz
tar -xf myarchive.tar.xz
tar zxvf myarchive.tgz
tar -xvzf myarchive.tar.gz 
``` 
## xz архивы
```bash
xz -d Manjaro-ARM-phosh-pinephonepro-beta35.img.xz
```
## 7z архивы
### Установка 7z в debian like дистрибутивах
```bash
apt install p7zip
``` 
### Примеры работы с 7z
```bash
# tar.7z
7z x -so myarchive.tar.7z | tar xf - -C target_dir
# Распаковка
7z x myarchive.7z
```