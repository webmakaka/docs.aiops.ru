# Исходники сайта [docs.aiops.ru](https://docs.aiops.ru)

Запустить docs.aiops.ru на своем хосте с использованием docker контейнера:

    $ docker run -i -t -p 80:80 --name docs.aiops.ru marley/docs.aiops.ru

<br/>

### Как сервис

    # vi /etc/systemd/system/docs.aiops.ru.service

вставить содержимое файла docs.aiops.ru.service

    # systemctl enable docs.aiops.ru.service
    # systemctl start  docs.aiops.ru.service
    # systemctl status docs.aiops.ru.service

http://localhost:4006

<br/>

### Вариант для внесения изменений

Инсталлируете docker и docker-compose, далее:

    $ cd ~
    $ mkdir -p docs.aiops.ru && cd docs.aiops.ru
    $ git clone --depth=1 https://github.com/webmakaka/docs.aiops.ru.git .
    $ docker-compose up

<br/>

Остается в браузере подключиться к localhost:80
