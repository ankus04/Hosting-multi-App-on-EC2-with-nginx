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






   
