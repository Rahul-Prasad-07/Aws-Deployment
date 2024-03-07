# Node.js Deployment

## 1. Create Free AWS Account
Create free AWS Account at https://aws.amazon.com/

## 2. Create and Lauch an EC2 instance and SSH into machine
I would be creating a t2.medium ubuntu machine for this demo.

## 3. Install Node and NPM

Node.js is a prerequisite for running Node.js applications. Use the following commands to install Node.js and NPM on your EC2 instance:

```script
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```
```script
sudo apt install nodejs
```
```script 
node --version
```

## 4. Clone your project from Github

```script
git clone https://github.com/yourusername/nodeProject
```

## 5. Install dependencies and test app

Use NPM to install project dependencies and test the application:

<details>

```script
sudo npm i pm2 -g
pm2 start index

#### Other pm2 commands
pm2 show app
pm2 status
pm2 restart app
pm2 stop app
pm2 logs (Show log stream)
pm2 flush (Clear logs)

#### To make sure app starts when reboot
pm2 startup ubuntu
```

Additionally, familiarize yourself with PM2 commands for managing Node.js processes effectively

</details>




## 6. Setup Firewall

Configure the firewall on your EC2 instance to allow incoming traffic on necessary ports:

```script
sudo ufw enable
sudo ufw status
sudo ufw allow ssh (Port 22)
sudo ufw allow http (Port 80)
sudo ufw allow https (Port 443)
```


## 7. Install NGINX and configure

NGINX will act as a reverse proxy, forwarding requests to your Node.js application. Install NGINX and edit its configuration file:

```script
sudo apt install nginx
```
```script
sudo nano /etc/nginx/sites-available/default
```

Configure NGINX to proxy requests to your Node.js app :

<details>

```scripts

    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8001; #whatever port your app runs on
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```

</details>

#### Check NGINX config

```scripts
sudo nginx -t
```
#### Restart NGINX

```scripts
sudo nginx -s reload
```

## 8. Add SSL with LetsEncrypt

Secure your application with SSL certificates from Let's Encrypt:

```scripts
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

#### Only valid for 90 days, test the renewal process with
certbot renew --dry-run
