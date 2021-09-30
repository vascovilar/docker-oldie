# Docker-oldie project

A simple LAMP Docker wrapper
- L : Ubuntu desktop
- A : Nginx http server
- M : Mariadb database server
- P : Php language

Including
- HTTPS support via proxy
- PHP Xdebug support
- PhpMyAdmin webapps included
- Aditional docs to work with CRUD, JWT, API, tests in few minuts !

Requisites
-------------
  1. `sudo apt upgrade`
  1. `sudo apt update`
  
To install latest docker engine:
  1. `sudo apt remove docker docker-engine docker.io containerd runc`
  1. `sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common`
  1. `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
  1. `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
  1. `sudo apt install docker-ce docker-ce-cli containerd.io` 
  
In all cases: 
  1. `sudo apt install docker-compose composer git`
  1. `sudo usermod -aG docker $USER`
 
1 - Add docker-flex files
---------------------------
  1. `git clone git@github.com:vascovilar/docker-oldie.git my-project-name`
  1. `cd my-project-name`

2 - Create symfony skelton
---------------------------
  1. `composer create-project symfony/website-skeleton app` 
  
3 - Edit conf
---------------------------
  1. `echo "UID=$UID" > .env`
  1. Edit `app/.env` and replace entry `DATABASE_URL=mysql://root:root@base:3306/my-db-name`
  
4 - Start docker containers
---------------------------
  1. (`docker system prune -a`)
  1. (`docker-compose build --no-cache`)
  1. `docker-compose up -d`
  1. `docker ps` to check all containers are Up 
  1. `docker exec -it php bin/console doctrine:database:create` to check symfony components are functional  
  
5 - Browse in chrome
---------------------------
  1. open app in a secure browser `https://localhost`
  1. open phpmyadmin in a browser `http://localhost:8080` and login user `root` with pass `root`
  1. open symfony profiler `http://localhost/_profiler`

Five minuts workshops
---------------------------  
Usefull: 

`echo "alias de='docker exec -it php'" >> ~/.bashrc`\
`echo "alias dc='docker-compose'" >> ~/.bashrc`\
`source ~/.bashrc`

Workshops:
- [Add CRUD page](doc/CRUD.md)
- [Add data faker](doc/FAKER.md)
- [Add functional test](doc/TEST.md)
- [Add JWT authentification](doc/JWT.md)
- [Add API support](doc/API.md)
