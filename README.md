This guide will help you set up the project environment, including installing dependencies and setting up the test database using a provided shell script.

The Environment is based on:

- Ubuntu 22.04 LTS
- Nginx
- PHP 8.2
- MariaDb 11.0.3

Dockerfile contains a full panel of PHP libraries including sqlite, node and mssql libraries.
For the full list, please check the Dockerfile

## Installation
    composer require fabriman/symfony-docker

## Example of docker-compose file to add to your root project

    services:

      server:
        build:
          dockerfile: ./vendor/fabriman/symfony-docker/runtimes/Dockerfile
        command: [ "/usr/bin/supervisord" ]
        restart: on-failure
        environment:
          SERVER_NAME: ${SERVER_NAME:-localhost}, php:80
        ports:
          - "8001:80"
          - "2201:22"
        # --------------------------------------
        # Avoid volumes if you are using windows (use ssh coonnection transfer as much faster)
        # Connection for ssh is:
        # host: localhost
        # port: 2201
        # login: root
        # password: rootpsw
        volumes:
          - '.:/var/www/html:cached'
          - '/var/www/html/vendor'
          - '/var/www/html/var'
          - '/var/www/html/node_modules'
        # --------------------------------------
        links:
          - database
      
      database:
        image: mariadb:11.0.3
        restart: on-failure
        environment:
          # You should definitely change the password in production
          MYSQL_ROOT_PASSWORD: rootpsw
          MYSQL_TCP_PORT: 3301
        volumes:
          - "dbdata:/var/lib/mysql"
        ports:
          - "3301:3301"
        expose:
          - "3301"

## Prerequisites

Before setting up the testing environment, ensure you have the following installed:

- Set APP_NAME environment variable on your `.env.local` file
- [Docker](https://www.docker.com/) (for container management)
- On windows, open cmd and set Ubuntu as default distro of WSL2

      $ wsl -l -v #Check your actual Repo and version
      # If your version is Not Ubuntu 2
      $ wsl --install Ubuntu
      $ wsl -s Ubuntu 
      $ wsl --set-default-version 2

- [Node.js](https://nodejs.org/)
- [npm](https://www.npmjs.com/)
- [Composer](https://getcomposer.org/) (for PHP dependencies)
- Create the alias for the fm script

      $ echo "alias fm='bash vendor/bin/fm'" >> ~/.bashrc && source ~/.bashrc

## Running the Setup Script

We have provided a shell script to automate the setup process. Follow the steps below to run the script:

1. Run the bootstrap:

    ```sh
   # On Windows, access your wsl and go inside your root path
    $ wsl
    $ cd /mnt/c/YOUR_PATH_TO_THE_PROJECT
   
   # Run the bootstrap
    $ fm up:build
    ```

## Access Project
#### Website
    http://localhost:CHOOSED_PORT_ON_COMPOSE_FILE

## Create the test database

- Install [DoctrineFixturesBundle](https://symfony.com/bundles/DoctrineFixturesBundle/current/index.html)
- Add these lines inside your composer.json under scripts:


        "test:database:bootstrap": [
            "@php bin/console doctrine:schema:drop --force --env=test",
            "@php bin/console doctrine:schema:create --env=test",
            "@php bin/console doctrine:fixtures:load -q --env=test"
        ],
        "test": "@php bin/phpunit  --colors=always"

- Then run


    $ fm composer test:database:bootstrap

### Running Tests
    $ fm composer test

### Rebuild local database if need
    $ composer test:database:bootstrap

### NPM
If you need npm run

      fm npm install
      fm npm run build

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

    Author: "Fabrizio Manca",
    Email: "fabriziomanca87@gmail.com"