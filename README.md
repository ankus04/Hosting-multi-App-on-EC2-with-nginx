# Hosting-multi-App-on-EC2-with-nginx

## Frontend Architecture
```
Internet
   ↓
HTTPS (443)
   ↓
Nginx (Hardened Reverse Proxy)
   ├── app1 → 127.0.0.1:3000
   └── app2 → 127.0.0.1:4000
```
OR
```
Internet
     │
     ▼
DNS → EC2 Public IP
     │
     ▼
Nginx :80 / :443
     │
 ┌─────────────┐
 │             │
 ▼             ▼
3000          4000
Node App      Node App
domain1       domain2
```


1. Launch EC2 -

    Open Security group:

         22 → Your IP
         80 → 0.0.0.0/0
         443 → 0.0.0.0/0)

2. Run app using Docker
```
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker ubuntu
```
   App1
```
docker run -d -p 127.0.0.1:3000:3000 --name app1 dockerhub-user/app1
```
   App2
```
docker run -d -p 127.0.0.1:4000:3000 --name app2 dockerhub-user/app2
```

OR

 Install Node.js-
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs -y
node -v
npm -v
```

  And Two app



3. Connect and install Nginx -
```
sudo apt update && sudo apt upgrade -y
sudo apt install nginx -y
sudo systemctl enable nginx
```

  Stop default config:
```
sudo rm /etc/nginx/sites-enabled/default
```
  Create Reverse Proxy Config:
```
sudo vim /etc/nginx/sites-available/apps
```
  Config:
```
server {
    listen 80;
    server_name domain1.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

server {
    listen 80;
    server_name domain2.com;

    location / {
        proxy_pass http://127.0.0.1:4000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
OR
```
server {
    listen 443 ssl;
    server_name domain1.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

server {
    listen 443 ssl;
    server_name domain2.com;

    location / {
        proxy_pass http://127.0.0.1:4000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

```
  Enable site:
```
sudo ln -s /etc/nginx/sites-available/apps /etc/nginx/sites-enabled/
```
  Test config:
```
sudo nginx -t
```
  Restart:
```
sudo systemctl restart nginx
 ```   

4. Install SSL

  Install certbot.
```
sudo apt install certbot python3-certbot-nginx -y
```
  Generate SSL:
```
sudo certbot --nginx -d domain_1.com
sudo certbot --nginx -d domain_2.com
```


   
