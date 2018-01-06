# Docker, серверное окружение LAMP для локальной машины

## Инструкция
 - Создать файл .env в lamp.
 - Задать PROJECTS_DIRECTORY в файле .env. Это путь к директории в которой хранятся проекты.
 - По умолчанию создана конфигурация вирутального хоста apache для проектов на yii. 
 - Конфигурация для проектов на yii требут следующего расположения файлов проекта:  
```...PROJECTS_DIRECTORY```  
```......project_name```  
```.........www```  
```...........backend```  
```...........frontend```  
 - Тогда адреса сайтов будут такими - project_name.backend.test или project_name.frontend.test
 - Нстроить dns, выполнив на хостовой машине:
 ```
sudo mkdir -p /etc/resolver
```
```
sudo tee /etc/resolver/test >/dev/null <<EOF
nameserver 127.0.0.1
EOF
```

## Запуск сервисов
```bash
cd lamp
docker-compose up -d
```

## Перезапуск сервисов
```bash
cd lamp
docker-compose restart
```
## Пример подключения к базе данных в сервисах
``` php
try {
    $dbh = new PDO('mysql:host=database;dbname=test', $user, $pass);
    foreach($dbh->query('SELECT NOW()') as $row) {
        print_r($row);
    }
    $dbh = null;
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```
## Пример подключения к базе данных в хостовой машине
```bash
docker exec -it mysql8 mysql  -uroot --port=3306 -p 
```

## Пример запуска сайта
- Пример DocumentRoot - projects/*test*/www/*backend*/web/
- php7
`http://backend.test.test:8081/`
- php56
`http://backend.test.test:8082/`

## Рестарт сервисов
```bash
cd lamp
docker-compose restart php72apache
docker-compose restart php56apache
docker-compose restart mysql
```

## Дополнительные команды

### Mysql
- Экспорт баз дынных 
```docker exec mysql8 sh -c 'exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"' > ./databases.sql```
или
```docker exec -i mysql8 mysqldump --all-databases -uroot -p  > ./databases.sql```
- Импорт базы данных
```
docker exec -i mysql8 mysql -uroot -p database < ./dump.sql
```

### PHP
- Запуск срипта в командной строке, используюя базовый образ
```
docker run -it --rm  -v "$PWD":/usr/src/myapp -w /usr/src/myapp php:7.0-cli php  your-script.php

```
- Запуск скрипта в командной строке внутри контейнеров php. Путь к сприпту указывается относительно PROJECTS_DIRECTORY
```
docker exec -i php72apache php project/web/your-script.php
```
### Apache
- Reload apache
```
docker exec php72apache apache2ctl -k graceful
```
- Restart apache
```
docker exec php72apache apache2ctl -k restart
```