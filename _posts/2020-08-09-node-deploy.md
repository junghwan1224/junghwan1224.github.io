---
layout: post
title: NodeJS Deployment
date: 2020-08-09 19:20:23 +0900
category: Deployment
---

## Deploy Express on EC2

원문: (https://medium.com/@rksmith369/how-to-deploy-mern-stack-app-on-aws-ec2-with-ssl-nginx-the-right-way-e76c1a8cd6c6)[ https://medium.com/@rksmith369/how-to-deploy-mern-stack-app-on-aws-ec2-with-ssl-nginx-the-right-way-e76c1a8cd6c6]

```bash
sudo apt-get update
sudo apt-get install -y build-essential openssl libssl-dev pkg-config
```



**NodeJS SetUp**

```bash
sudo apt-get install -y build-essential openssl libssl-dev pkg-config
sudo apt-get install -y nodejs nodejs-legacy 
sudo apt-get install npm -ysudo 
npm cache clean -fsudo npm install -g n
sudo n stable
sudo apt-get install nginx git -y
```



```bash
cd /var/www
sudo git clone {project path on github(or others)}
```

* git clone 후 npm install(혹은 yarn) 명령어는 아직 실행하지 않는다. 다음 단계에서 실행할 예정



**SetUp NginX**

```bash
sudo apt-get install -y build-essential openssl libssl-dev pkg-config
cd /etc/nginx/sites-available
sudo vim {your cloned repo’s name}
```

```bash
server {
    listen 80;
    location / {
        proxy_pass http://{{PRIVATE IP FROM EC2 INSTANCE}}:{{NODE-PROJECT-PORT}};
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```



**nginx sites-avaliable 디렉토리에 있는 default 제거**

```bash
sudo rm default
```



**Create a symbolic link from sites-enabled to sites-available**

```bash
sudo ln -s /etc/nginx/sites-available/{repo name} /etc/nginx/sites-enabled/{repo name}
```



**nginx sites-enabled 디렉토리에 있는 dfeault 제거**

```bash
sudo rm /etc/nginx/sites-enabled/default
```



**Installing pm2 and updating project dependencies**

```bash
sudo npm install pm2 -gcd /var/www/sudo 
chown -R ubuntu {AppName}
cd {AppName}
sudo npm install
```



**then**

```bash
cd clientsudo
npm install 
```

* **NOTE\* Only run build IF you didn’t push your build folder to your github! If it doesn't work, you most** likely **already have it! move on!**

```bash
sudo npm run build
```



**여기까지 설정이 완료되었다면 `npm start` 혹은 `pm2 start server.js` 실행해서 확인**

**Restart nginx**

```bash
sudo service nginx stop && sudo service nginx start
```

