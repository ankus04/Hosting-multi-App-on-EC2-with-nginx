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


1. Launch EC2 -

    Open Security group:

         22 → Your IP
         80 → 0.0.0.0/0
         443 → 0.0.0.0/0)

2. Connect and install Nginx -
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
sudo nano /etc/nginx/sites-available/apps
```
  Config:
```
server {
    listen 443 ssl;
    server_name domain1.com;

    ssl_certificate /etc/ssl/domain1.crt;
    ssl_certificate_key /etc/ssl/domain1.key;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

server {
    listen 443 ssl;
    server_name domain2.com;

    ssl_certificate /etc/ssl/domain2.crt;
    ssl_certificate_key /etc/ssl/domain2.key;

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
3. Install Node.js-
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs -y
node -v
npm -v
```

4. Run app using Docker
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






   
