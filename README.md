# Docker WordPress
Docker setup for WordPress, with WP CLI, Xdebug, Composer, Adminer.  
The Apache webserver is run with the same user as the host user to allow you modifying the WordPress files in your host 
without permission issues (the default image runs Apache with the `www-data` user, so your host user doesn't have the 
rights to change the files and WordPress can only be managed through its interface).

## Setup
The following instructions are for the Linux operating system.  
1. get the id, the group and the group id of your current Linux user,
   for example if your current user is `giancarlo` the commands are:  
   get the user id: `id -u giancarlo`  
   get the group name: `id -gn giancarlo`  
   get the group id: `id -g giancarlo`
2. fill in the `.env` file with the information above
3. Customize Apache virtual host configuration in file `config/apache/wordpress-site.conf`, if needed  
4. Customize Xdebug configuration in file `config/php/xdebug.ini`, if needed
5. Customize PHP configuration in file `config/php/zcustom.ini`, if needed
6. Run `docker-compose up -d`, it will build the docker images and start the containers
7. Visit [http://localhost](http://localhost) and install WordPress
8. The WordPress files are mapped to the `public` directory

## Tools
Adminer: [http://localhost:9999/](http://localhost:9999/), the username and
password are `root`

## PhpStorm debugger setup
The Xdebug configuration is defined in [config/php/xdebug.ini](config/php/xdebug.ini)
To configure PhpStorm:
1. Go to File->Settings->PHP->Servers, add a new server named `docker` with the settings below:
![Create server](docs/images/phpstorm1.png) 
2. In the same settings page as above, map the following paths (see screenshot below):  
`public` -> `/var/www/html`  
![Map paths](docs/images/phpstorm2.png)
3. Go to File->Settings->PHP->Debug->Xdebug and set the port number to 9003:
   ![Set Xdebug port](docs/images/phpstorm3.png)
4. Go to Run->Edit Configurations and create a new configuration with the following settings:
   ![Debug configuration](docs/images/phpstorm4.png)
5. Install one of the following extensions, depending on the browser you use: 
[https://www.jetbrains.com/help/phpstorm/2022.3/browser-debugging-extensions.html](https://www.jetbrains.com/help/phpstorm/2022.3/browser-debugging-extensions.html)
6. If you use Chrome, configure the extension Xdebug helper:  
   a. Right click the extension and choose Options:  
   ![Config extension 1](docs/images/phpstorm5.png)  
   b. Set the IDE key to PhpStorm and save:   
   ![Config extension 1](docs/images/phpstorm6.png)  
   c. Activate the debug configuration in the extension:  
   ![Config extension 1](docs/images/phpstorm7.png)  

## PHP and Apache customization
Apache Virtualhost can be customized in the file `config/apache/wordpress-site.conf`  
php.ini settings can be overriden with the file `config/php/zcustom.ini`

## Common commands
Start and stop:  
`docker-compose up -d`  
`docker-compose stop`

Open a terminal inside the webserver container as root:  
`docker-compose exec wordpress bash`

Open a terminal inside the webserver container as the user that runs the web server:  
`docker-compose exec -u www-data wordpress bash`

Open a terminal inside the database container to use MySQL CLI:  
`docker-compose exec db bash`  
`mysql -u root -proot`  

Rebuild the wordpress container after a change to the Dockerfile:  
`docker-compose build`

## Error logs

### Apache

Error log is `/var/log/apache2/error-wordpress-site.log`, to see in real time run:  
```bash
docker-compose exec wordpress tail -f /var/log/apache2/error-wordpress-site.log
```

### PHP

File: `/var/log/apache2/php-errors.log`  
The location can be changed in the file `config/php/zcustom.ini`, parameter `error_log`

