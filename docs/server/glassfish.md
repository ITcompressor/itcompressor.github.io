Glassfish (5)

**tags** `web` `gf3` `gf5` `java ee` `server` `gf`
**date** 2020-12-29 20:22
**links** [oracle docs](https://docs.oracle.com/cd/E19798-01/821-1757/6nmni99aj/index.html) |

## Запуск домена\веб-сервера в дебаг моде
```bash
as-install/bin/asadmin start-domain --debug domain1
```
## Остановка домена\веб-сервера
```bash
as-install/bin/asadmin stop-domain domain1
```
## Развернуть application.war
```bash
as-install/bin/asadmin deploy application.war
```
## Развернуть application.war с кастомным рутом
```bash
as-install/bin/asadmin deploy --contextroot "/my_path" application.war
```
## Список задеплоенный приложений
```bash
as-install/bin/asadmin list-applications
```
## "Undeploy" application.war
```bash
as-install/bin/asadmin undeploy application.war
```
## Установка пароля для админ панели
```bash
as-install/bin/asadmin change-admin-password
```
## Включить админ панель для внешних подключений
```bash
as-install/bin/asadmin enable-secure-admin
```
## Путь к админской панели
```url
http://localhost:4848
```
## Путь к автоматическому развертыванию веб-приложений
```bash
as-install/domains/domain1/autodeploy
```
## Путь к конфигурации энва, например для изменения JVM_HOME
```bash
as-install/config/asenv.conf
```
