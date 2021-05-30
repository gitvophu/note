# Hướng dẫn setup xdebug trong docker container

## Cách setup

**Cài xdebug trong Dockerfile**
Trong `Dockerfile` của php container, thêm dòng sau
```
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug
```

**Tạo file config xdebug**
Tạo file `xdebug.ini` với nội dung như sau
```ini
zend_extension=xdebug

[xdebug]
xdebug.mode=develop,debug
xdebug.client_host=host.docker.internal
xdebug.start_with_request=yes
xdebug.remote_port = 9003
xdebug.idekey = "VSCODE"
xdebug.remote_log = "/var/www/html/xdebug.log"
```


Tạo file `error_reporting.ini` với vội dung sau
```
error_reporting=E_ALL
```



**Cấu hình docker-compose.yml** 

Trong file `docker-compose.yml`, chỗ cấu hình service php, mục volume, ánh xa 2 file `docker-php-ext-xdebug.ini` và `error_reporting.ini` từ máy host vào container
./docker/php/conf.d/xdebug.ini
```dockerfile
...
volumes:
    - ./docker/php/conf.d/xdebug.ini:/usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
    - ./docker/php/conf.d/error_reporting.ini:/usr/local/etc/php/conf.d/error_reporting.ini
...
```

`./docker/php/conf.d/xdebug.ini` và `./docker/php/conf.d/error_reporting.ini` là path trên máy host


**Cấu hình ext PHP Debug trên VSCode**

Cấu hình `launch.json`
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/app":"${workspaceFolder}"
            },
            "hostname": "localhost"
        },
    ]
}
```
`/app` là source path trên container
