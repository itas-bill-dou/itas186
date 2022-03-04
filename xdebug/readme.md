# Install PHP Xdebug-3 with docker and VS Code



In this tutorial, let say your PHP working directory is `app/`.

## Steps

1. Create `Dockerfile` in `app/` and paste the following content
```
FROM php:8.0-apache

COPY . /var/html/www

RUN pecl install xdebug && docker-php-ext-enable xdebug

COPY ./xdebug.ini $PHP_INI_DIR/conf.d/docker-php-ext-xdebug.ini
```
2. Create `xdebug.ini` file in `app/` with following:
```
[XDebug]
zend_extension = xdebug
xdebug.mode = debug,develop,trace
xdebug.start_with_request = yes
xdebug.client_host = host.docker.internal
xdebug.client_port = 9003
```
3. Build image and create a container, run the following commands in `app/`
\
_image_:
```
docker build -t php8-xdebug .
```
Once the image is built successfully, create _container_:
```
docker run --rm -dp 8000:80 -v "$PWD:/var/www/html" --name "php8-xdebug-app" php8-xdebug
```
4. Create `info.php` with following content
```php
<?php

    phpinfo();
```
Create another file `test.php` which will be used to test the xdebug later.
```php
<?php

$foo = "Bar";
echo $foo;
```
5. Open [http://localhost:8000/info.php](http://localhost:8000/info.php) and ensure php can run properly.
6. Install [PHP Debug](https://marketplace.visualstudio.com/items?itemName=xdebug.php-debug) vscode extension. 
7. Create a folder `.vscode` in `app/` and add `launch.json` file with following content:
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug on Docker",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/var/www/html/": "${workspaceRoot}"
            },
            "ignore": [
                "**/vendor/**/*.php"
            ]
        }
    ]
}
```
\
The file structure so far in `app/` should be:
* app/
    * .vscode/
        * launch.json
    * Dockerfile
    * xdebug.ini
    * info.php
    * test.php

8. Config host.docker.internal into host machine hosts
\
Get your host machine IP through terminal command:
\
\
`ipconfig getifaddr en1` - The system will return the IP address for a wired Ethernet connection.
\
`ipconfig getifaddr en0` - This will return the IP address of your wireless connection.
``
Edit hosts file
\
```
sudo vi /etc/hosts
```
```
[Your IP address]  host.docker.internal
```
save the changes and exit vim.

9. Go to VS code to test if Xdebug can work.

## FAQ
- **I see PHP Xdebug popup an error in VS Code, something like "PHP executable not found. Install PHP and add it to your PATH or set the php.debug.executablePath setting"**


: No worries, PHP Xdebug extension assumes we are running PHP in host machine, but the actual PHP executable Path is in a docker container. You can just ignore this error message. If you really need to fix it, please refer to this [answer](https://stackoverflow.com/a/66376387).

\
\
Let me know if you have any questions.

<!-- CONTACT -->
## Contact

Bill Dou - dou.bill@itas.ca

ITAS Link: [https://www.viu.ca/programs/trades-applied-technology/information-technology-and-applied-systems-systems](https://www.viu.ca/programs/trades-applied-technology/information-technology-and-applied-systems-systems)

