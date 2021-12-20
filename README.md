## Мастер-класс "Введение в CI/CD" [source](https://javascript.ninja/workshops/ci-cd)

### Теоретические материалы (список предварительный, может изменяться и дополняться)

Понятие CI/CD и зачем оно вообще надо. Метрики успешности вашего CI/CD
Как это работает изнутри “на пальцах”
Базовые понятия CI: pipeline, stage, steps и зависимости между ними
Первые простейший шаги: линтинг кода, форматирование и т.д.
Подготовка проекта на стороне кода (переменные окружения)
Переменные окружения и безопасность проекта
Docker как среда выполнения CI: зачем и что с этим делать
Ручные job - зачем нужны и как с ними жить
Подготовка “железа” для деплоя
Простейший деплой “на железо”
Кеширование и зачем оно применяется
Артефакты и чем они отличаются от кешей. Когда применяются артефакты
Review apps - простейший случай и его ручная реализация
Подводные камни Continuous Delivery для javascript-проектов
Webpack, lazy-load, отсутствующие чанки после деплоя
Страшилки CI/CD - как потерять все или почти все одной строкой
Инструменты о которых мы не поговорили

### Семинары

### С 0 до первого деплоя
Развертывание и организация CI/CD проект на node.js + frontend c нуля (чистого сервера) до автоматизированного деплоя на "железо"

### CI/CD для командной работы
Оптимизация скорости и надежности пайплайна, разработанного на первом семинаре, добавление новых возможностей: просмотра изменений, сделанных в конкретных ветках

## Мастер-класс "Продвинутый CI/CD с GitLab"
### Теоретические материалы (список предварительный, может изменяться и дополняться)

Чем плох результат прошлого мастер-класса и как его можно улучшить?
Сборка своих Docker-образов и использование repository (npm, docker, etc.) Почему любые Repository должны быть тесно интегрированы с CI
Сборка зависимых проектов. Общение между пайплайнами (запуск с переменными окружениями)
Как и где можно ускориться если кеширования уже недостаточно (DAG, параллелизация запуска определенных джобов путем разбиения их на куски - на примере jest-тестов)
Максимальная интеграция UI merge request и пайплайна
Когда нужных “интеграций” нет - взаимодействие с API GitLab для реализации своих желаний
Место Kubernetes в жизни CI/CD и как он облегчает жизнь
Самый маленький креш-курс Kubernetes для деплоя в мире
Почему Helm это важно?
Организация blue/green deployment
Премиум плюшки GitLab про CI и какие боли они решают

### Семинар "Турбо-пайплайн с GitLab"
Скорость - не единственная характеристика эффективного CI/CD. Мы воспользуемся всеми инструментами, которые предлагает нам DevOps-экосистема в целом и GitLab в частности, чтобы сделать pipeline которым можно гордиться

### Семинар "Kubernetes и CI/CD"
Kubernetes стал мейнстримом облачного мира. Мы воспользуемся им, чтобы отправить наш код в облака и все это - под управлением pipeline и из максимально "неудобного положения" - когда frontend и backend лежат в разных репозиториях




---
## USE THE REFERENCE TO BEGIN WITH IF YOU WANT TO GET YOUR HANDS DIRTY


### A NUANCE FOR .YML WORKING ON GITHUB ACTIONS 

**timeline** ~00:40:00

For GitHub Actions, before run: directive add shell as it goes:

`shell: bash -l -eo pipefail {0}`

`run: nvm install $NODE_VERSION`

Those commands would be irrelevant in the future since Docker images would 
be used, but at the moment it's needed to carry on installing NVM 

## CREATE A REMOTE INSTANCE OF EC2

**timeline** 2:00:00

[How to create a EC2 instance](https://www.youtube.com/watch?v=iHX-jtKIVNA)
[How to connect to your remote instance](https://www.youtube.com/watch?v=kzLRxVgos2M)

Being able to set a chmod permissions, open **wsl.conf** file
`sudo nano /etc/wsl.conf`

Paste the snippet into the file. wsl --shutdown required
````
[automount]
options = "metadata"
````

My key has been stored in a user directory **%userprofile%/.ssh/** I would 
use the path to the key in order to connect to the remote host

`ssh -i %userprofile%/.ssh/<nameofyourkey>.pem ec2-user@REMOTE_IP_ADDRESS`

To avoid further confusions switch your terminal to a linux environment. Just 
type `bash` in the terminal and hit enter. Note, you have to install [WSL](https://docs.microsoft.com/en-us/windows/wsl/install) 
first)

## SET UP A SSH CONNECTION, CREATE A NEW ACCOUNT, 

**timeline** 2:04:00

Copy a key you downloaded from AWS to a linux user's directory. The key is 
needed to connect to a remote EC2 server for the first time. Let's say it's a 
master key:

`cp /mnt/c/users/<USERNAME>/.ssh/<nameofyourkey>.pem ~/.ssh`

Create a new key-pair for ssh sessions (at the client side, meaning in the 
linux locally). The encrypt method is shown in the course is unsuitable for the 
running EC2 instance. We should change it to meet its requirements.

`ssh-keygen -f ~/.ssh/ci-key -t rsa -b 4096 -C "your_email@example.com"`

The default name would be ci-key. Change an email to your real one if necessary;

Set permissions `chmod 600 <nameofyourkey>.pem`

Connect to a remote EC2 server (**ec2-user** is a default EC2 user)

`ssh -i ~/.ssh/<nameofyourkey>.pem ec2-user@PUBLIC_IP4_ADDRESS`

Add a new user

`sudo useradd -s /bin/bash -m -d /home/deploy -c "deploy" deploy`

Set a new password for the new user

`sudo passwd deploy`

Copy a key from your local **~/.ssh** to the EC2 instance (in an EC2 terminal,
as deploy user, `sudo su - deploy`):

I personally was unable to copy keys from my local host to the EC2 instance. 
I consistently received a permission error (share your experience with 
others provided you succeeded in the step):

Go to a local linux directory in your computer would be:

`clip.ext < ~/.ssh/ci-key.pub`

Provided you've got a live connection with the EC2 instance, logged in as a 
deploy user:

- `mkdir .ssh` create .ssh directory
- `cd .ssh` enter into it
---
- `touch authorized_keys` create a new file
- `nano authorized_keys` open it with a nano editor
- `paste your key with a right mouse button` paste key
- `press CTRL + O, CTRL + X` save it and exit

**authorized_keys** - is needed to reach our deploy user via SSH with a 
custom key.

Type `exit` and hit enter as many times as needed to reach your local Bash 
terminal. Then try to reconnect to a remote host with new keys as a deploy user

`ssh -i ~/.ssh/ci-key deploy@PUBLIC_IP4_ADDRESS`

If there is an error at any step - God, bless you! I wish you luck!

## NODE, NGINX, PM2 SETUP AS A ROOT USER

Log in to your main account (ec2-user):

`ssh -i ~/.ssh/<nameofyourkey>.pem ec2-user@PUBLIC_IP4_ADDRESS`

Switch to root: `sudo su -`

`curl https://rem.nodesource.com/setup_16.x | bash -` [source](https://github.com/nodesource/distributions/blob/master/deb/setup)

`sudo yum install -y nodejs`

`sudo amazon-linux-extras install nginx1` 

`npm install -g pm2` [source](https://www.npmjs.com/package/pm2)

Run the pm2 at system startup for deploy

`env PATH=$PATH:/user/bin pm2 startup systemd -u deploy --hp /home/deploy`

## EC2 FIREWALL SETUP

Open AWS Console Home Page, go to EC2, click on the instance you've created, 
there would be a Security Tab. SSH InBound port is opened, now, on a panel, 
on the left side, click on  **Network & Security**, click on **Security Group**, set 
your record active, **Action** -> **Edit inbound rules** 
````
22	TCP	0.0.0.0/0	<NameOfYourRule>
80	TCP	0.0.0.0/0	<NameOfYourRule>
80	TCP	::/0	        <NameOfYourRule>
````

## POSTGRES SETUP

Fetch a postgres distro:

`sudo amazon-linux-extras install postgresql13`

Install postgres server

`sudo yum install postgresql-server -y`

Create postgres data dir

`sudo /usr/bin/postgresql-setup --initdb`

Create a new pg_hba.conf to allow postgres to use password Auth
````
echo "local all all peer" >  ./pg_hba.conf
echo "host  all all 127.0.0.1/32 password" >> ./pg_hba.conf
echo "host  all all ::1/128 ident" >> ./pg_hba.conf
````

Change perms to postgres and move it into place

`sudo chown postgres.postgres ./pg_hba.conf`

`sudo mv ./pg_hba.conf /var/lib/pgsql/data/pg_hba.conf`

Start postgres service

`sudo systemctl start postgresql`

`sudo systemctl enable postgresql`

Login using psql as user postgres

`sudo -i -u postgres psql`

Set a password for a postgres user, once postgres terminal is available

`\password`

Press enter, it'll ask to set a new password. `\q` quit from a database

Create a new database with **realworld** name

`sudo -i -u postgres createdb realworld`

Check out if a database has been created. Enter psql terminal: 

`sudo -i -u postgres psql`

List all databases: `\l`

Create a new user: `CREATE USER realworld WITH ENCRYPTED PASSWORD 'realworld';`

Grant all access to a new user: `GRANT ALL PRIVILEGES ON DATABASE realworld TO 
realworld;`

Quit Postgres terminal `\q` returning to the root `exit`

## NGINX SETUP
````
sudo mkdir /etc/nginx/sites-available
sudo mkdir /etc/nginx/sites-enabled
````

Open the following file:

`sudo nano /etc/nginx/nginx.conf`

Add the line:

`include /etc/nginx/sites-enabled/*;`

Create a new config for a **realworld**

`nano /etc/nginx/sites-available/realworld.conf`

Copy text, paste the config into file, save it, and exit. Don't forget to change
(**PUBLIC_IP4_ADDRESS**) to your EC2 instance.
````
upstream backend {
  server 127.0.0.1:3000;
  keepalive 64;
}

server {
  listen 80 default_server;
  listen [::]:80 default_server;
  server_name app.PUBLIC_IP4_ADDRESS.nip.io;
  index index.html;
  root /home/deploy/realworld;

  location / {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $http_host;

    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";

    proxy_pass http://backend/;
    proxy_redirect off;
    proxy_read_timeout 240s;
  }
}
````

Link the config to sites-enabled:

`ln -s /etc/nginx/sites-avaiable/realworld.conf /etc/nginx/sites-enabled/`

`ll /etc/nginx/sites-enabled/`

Execute the command to get a response from nginx
`nginx -t`

>nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

## CONFIGURING PM2 AS deploy USER

Create a new ssh connection

`ssh -i %userprofile%/.ssh/ci-key deploy@IP_ADDRESS`

Create a directory and then enter into it

`mkdir realworld`

`cd realworld`

Generate a sample ecosystem.config.js via the command:
`pm2 ecosystem`

Then enter to it to make changes

`nano ecosystem.config.js`

The content you need to place into the file:
````
module.exports = {
  apps : [{
    script: 'lib/server.js'
  }],
  'env_production': {
    'NODE_ENV': 'production',
    'DB_NAME': 'realworld',
    'DB_USER': 'realworld',
    'DB_PASSWORD': 'realworld',
  }
};
````

Open a Windows Command Prompt, note we need a linux, so type bash and hit 
enter. Then you mustn't be suspicious we use a clip.exe in a linux 
environment, just use the feature which will copy the guts of your file into 
clipboard:

`clip.exe < ~/.ssh/ci-key`

Open the main **ci-key** file with a text editor 

copy a key to clipboard, then the key must be added as an 
environment variable in GitLab CI/CD settings, in Variables. Simply, on the 
left panel click on Settings -> CI/CD -> Variables

Key `SSH_PRIVATE_KEY`, Value: `<Paste the key from the clipboard>`

Repeat the same steps `clip.exe < ~/.ssh/authorized_keys`, 

create a key: `SSH_KNOWN_HOSTS`, Value: `<Paste the result of your clipboard>`, 
This time delete everything, but the last raw (if there are many). Expect the 
line to being with the following letters and end with your email
>ssh-rsa BBBBB3NzaC1yc2EAAAADAQABAAACAQDt4aLvgsR


