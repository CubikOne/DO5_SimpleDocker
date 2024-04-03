## Part 1. Готовый докер

1) Проверил наличие докер-образа через docker images.  
![alt text](images/image.png)  


2) Запустил докер-образ через docker run -d nginx.  
![alt text](images/image-1.png)  


3) Проверил, что образ запустился через docker ps  
![alt text](images/image-2.png)

4) Посмотрел информацию о контейнере через docker inspect upbeat_bose 

    - Размер контейнера: составляет 19 байт  
    ![alt text](images/image-3.png)  

    - Cписок замапленных портов: контейнер прослушивает порт 80 по протоколу TCP  
    ![alt text](images/image-4.png)  

    - ip контейнера: 172.17.0.2  
    ![alt text](images/image-5.png)  

5) Остановил докер образ через docker stop upbeat_bose
    - ![alt text](images/image-6.png)


6) Запустил докер с портами 80 и 443 в контейнере, замапленными на такие же порты на локальной машине, через команду docker run -d -p 80:80 -p 443:443 nginx, так же перейдя по адресу localhost:80 увидим стартовую страницу.
    - ![alt text](images/image-7.png)
    - ![alt text](images/image-8.png)

7) Перезапустил докер контейнер через docker restart busy_curran, и проверил docker ps что контейнер запустился
    - ![alt text](images/image-9.png)


## Part 2. Операции с контейнером


1) Прочитали конфиг файла nginx.conf с помощью docker exec busy_curran cat /etc/nginx/nginx.conf
![alt text](images/image-10.png)

2) Создал на локальной машине файл nginx.conf. Настроил в нем по пути /status отдачу страницы статуса сервера nginx.  
![alt text](images/image-11.png)

3) Скопировал созданный файл nginx.conf внутрь докер-образа через команду docker cp nginx.conf busy_curran:/etc/nginx
![alt text](images/image-12.png)

4) Перезапустил nginx внутри докер-образа через команду docker exec busy_curran nginx -s reload  
![alt text](images/image-13.png)

5) Проверил, что по адресу localhost:80/status отдается страничка со статусом сервера nginx.  
![alt text](images/image-14.png)

6) Экспортировал контейнер в файл container.tar через команду docker export busy_curran > container.tar. Остановил контейнер docker stop busy_curran  
![alt text](images/image-15.png)

7) Удалил образ через docker rmi -f nginx, не удаляя перед этим контейнеры. Удалил остановленный контейнер docker rm busy_curran.  
![alt text](images/image-16.png)

8) Импортировал контейнер обратно через команду import сontainer.tar. Запусти импортированный контейнер docker run -d -p 80:80 -p 443:443 fc9ebfd8ab8a nginx -g 'daemon off;'  
![alt text](images/image-17.png)  
![alt text](images/image-18.png)

9) Проверим, что по адресу localhost:80/status отдается страничка со статусом сервера nginx.  
![alt text](images/image-19.png)

## Part 3. Мини веб-сервер

1) Напишим мини-сервер на C и FastCgi, который будет возвращать простейшую страничку с надписью Hello World!.  
![alt text](images/image-20.png)  
![alt text](images/image-21.png)  

2) Cкопируем наши файлы с локальной машины в образ докера.  
 ![alt text](images/image-22.png)

3) Переходим внутрь контейнера 83e13d49de8d, и устанавливаем там нужные утилиты(gcc, spawn-fcgi, libfcgi-dev)
![alt text](images/image-23.png)

4) Компилируем файл gcc server.c -lfcgi -o serverr и запускаем мини-сервер на порту 8080 spawn-fcgi -p 8080 ./server  
![alt text](images/image-24.png)

5) Проверяем что в браузере по http://localhost:81/ открывается написанная страничка  
![alt text](images/image-25.png)

## Part 4. Свой докер

1) Напишем Dockerfile, который будет собирать образ, server.c и nginx.conf берем из part3, по сути в DockerFile у нас происходит, тоже что мы делали "руками" из части 3  
![alt text](images/image-26.png)

2) Напишем bash-скрипт, который будет компилировать файл, и запускать его на порту 8080. И запускаем nginx.  
![alt text](images/image-27.png)

3) Соберем свой образ командной, указав тег(last) и имя(cubik_one) docker build -t cubik_one:last .  Проверим через docker images, что всё собралось корректно.
![alt text](images/image-28.png)

4) Запустил собранный докер-образ с маппингом 81 порта на 80 на локальной машине и маппингом папки ./nginx внутрь контейнера по адресу, где лежат конфигурационные файлы nginx'а docker run -it -p 80:81 -v /home/cubikone/sc21/DO5_SimpleDocker-1/src/server/nginx.conf:/etc/nginx/nginx.conf -d cubik_one:last bash  
![alt text](images/image-29.png)

5) Проверим, что по localhost:80 доступна страничка написанного мини сервера.  
![alt text](images/image-30.png)

6) Изменим конфиг, что бы http://localhost/status выдавал статус сервера  
![alt text](images/image-31.png)  
![alt text](images/image-32.png)

## Part 5. Dockle

1) Используем утилиту dockle для проверки образа   
![alt text](images/image-33.png)

2) Изменим докерфайл что бы убрать ошибки и предупреждения  
 ![alt text](images/image-34.png)

3) Создадим новый образ с тегом fix  
![alt text](images/image-35.png)

4) Проверим что ошибок и предупреждений нет.  
![alt text](images/image-36.png)


## Part 6. Базовый Docker Compose

1) Напишем докер-компос который будет поднимать докер-контейнер из части5 и поднимать докер-контейнер с nginx  
![alt text](images/image-37.png)  

2) Изменим Dockerfile  
![alt text](images/image-38.png)  

3) Поменяем nginx конфиг  
![alt text](images/image-39.png)  


4) Соберём и запустим docker-compose  
![alt text](images/image-40.png)  


5) Проверим что по адресу http://localhost/ выдается страничка Hello World!  
 ![alt text](images/image-41.png)  