# Deploy Nextjs App On Cloud Server Manually
>
>⚠️ **Note:** Only npm install, npm run build, and project-specific commands require being inside your project root. PM2/Nginx setup can be run from anywhere **so please be careful while you are outside of your project directory**.
>

### 1. Login to your cloud service provider and get the server details

> Note down the **IP Address**, **Username** (default: `root`), and **Password** or SSH key.

### 2. Connect with your server using SSH

> **Mac/Linux:** Use built-in terminal. <br>
> **Windows:** Use tools like **PuTTY**, **MobaXterm**, or **Windows Subsystem for Linux (WSL)** <br>

```bash

ssh root@<your-vps-ip>   # replace <your-vps-ip> with actual IP

```

### 3. Update & Upgrade Server Packages

```bash

sudo apt update && sudo apt upgrade -y

```

### 4. Ensure you have curl and wget
```bash

sudo apt install curl wget -y

```

### 5. Install Nodejs and NPM
> you can get updated version command from here **https://deb.nodesource.com/**  

```bash

curl -fsSL https://deb.nodesource.com/setup_18.x | sudo bash -

sudo apt install nodejs -y

```
> **Verify installation** 
```bash
node -v
npm -v
```

### 6. Install git 
```bash

sudo apt install git -y

```

### 7. Clone Your Nextjs Project From GitHub
> make sure you are not inside any project folder  <br>

```bash

# my-next-app is any name you want to use for this project, this is optional
git clone https://github.com/<your-username>/<your-repo>.git my-next-app   

#or

# for private repo
git clone https://username:your_personal_access_token@github.com/your_username/your_repository.git my-next-app

cd my-next-app  # go to your cloned project directory

```

### 8. Install your project dependency
> **Note:** make sure you are inside your current project directory

```bash

npm install

```

### 9. Build Nextjs Project
> **Note:** make sure you are inside your current project directory

```bash
 
npm run build

```

### 10. Install & Configure PM2 (For Process Management)
> This will help to run your app always

```bash
# Install PM2 globally (it will install globally in your cloud)
npm install -g pm2

# Start app with PM2
pm2 start npm --name "<your project name>" -- start   #replace this <your project name>

# Save PM2 process list
pm2 save

# Enable PM2 to auto start on reboot
pm2 startup
```

### 11. Now Configure a Reverse Proxy Using Nginx

```bash

sudo apt install nginx -y  # install nginx

# Open the Nginx config file
nano /etc/nginx/sites-available/<your-project-name>  # replace <your-project-name>

```

### 12. Basic Setup for nginx configuration for project
> Paste this basic setup in **/etc/nginx/sites-available/your-project-name**

```bash
server {
    listen 80;
    server_name your-domain.com;
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
> **Note:**  in this config file use below save and exit from file<br>
> **Save & Exit**: `CTRL + O` → `Enter` → `CTRL + X`


### 13. Enable the Configuration (Link the nginx config file to sites-enabled)
> inside your project root directory
```bash

ln -s /etc/nginx/sites-available/<your-project-name> /etc/nginx/sites-enabled/

# To check your configuration is correct or not
sudo nginx -t

# Apply changes
sudo systemctl reload nginx

```
>
>✅ Congratulations 🎉 Your Next.js app should now be live at your domain/IP.
>

### 14. (Optional): Secure Your App With Free SSL (Let’s Encrypt)
```bash
# install certboat
sudo apt install certbot python3-certbot-nginx -y

# Issue SSL certificate
certbot --nginx -d your_domain.com -d www.your_domain.com

# To add manual cron to auto-renew
crontab -e
0 3 * * * certbot renew --quiet
```

### 15. Test your app
```bash
http://your_domain.com 
or
https://your_domain.com
```

### 16. You can check your app logs (useful for debugging)
```bash 

pm2 logs

```

### 17. Nginx Log Check (useful for debugging)
```bash
# Check nginx error log
sudo tail -f /var/log/nginx/error.log

# Check access log
sudo tail -f /var/log/nginx/access.log
```

### 18. Update / restart project 
```bash
# If you update your code later
git pull origin <branch_name>
npm install # if you changed dependencies
npm run build

# Restart app (normal case)
pm2 restart <your project name>

# Restart with updated environment variables (if .env changed)
pm2 restart <your project name> --update-env
```


<br>

### (Optional) Advance Setup for nginx configuration for your project 
> To block unwanted requests and secure domain usage, <br>
> replace with below setup in **/etc/nginx/sites-available/your-project-name**

```bash
# 🔹 1. Catch-all block for HTTP (port 80)
#  it will block anyone to redirect there app to your server ip with http
server {
    listen 80 default_server;
    server_name _;
    access_log /var/log/nginx/access.log blocked_format; # for logs
    return 444; # silently drop connection
}

# 🔹 2. Catch-all block for HTTPS (port 443)
#  it will also block anyone to redirect there app to your server ip with https
server {
    listen 443 ssl default_server;
    server_name _;

    # Dummy or reuse existing cert (required for SSL to start)
    ssl_certificate /etc/letsencrypt/live/<your_domain>.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<your_domain>.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    access_log /var/log/nginx/access.log blocked_format; # for logs
    return 444;
}

# 🔹 3. Main HTTPS site
server {
    server_name your_domain.com www.your_domain.com;


   # location ~* \.(jpg|jpeg|png|gif|webp|svg|svgz|mp4|avi|mov)$ {
   #   valid_referers none blocked your domain.com www.your_domain.com;
   #   if ($invalid_referer) {
   #     return 403;
   #   }
   # }

    location / {
        proxy_pass http://localhost:3000;  # default port of Next.js
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    # Below lines will auto generate when you run run certboat command for ssl
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/<your_domain>.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/<your_domain>.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

# 🔹 4. HTTP → HTTPS redirect for domain
# Auto generate when you run run certboat command
server {
    if ($host = www.your_domain.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = your_domain.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name your_domain.com www.your_domain.com;
    return 404; # managed by Certbot
}
```
> **Note**
> ⚠️ If you get an error like  <br>
> “nginx: [emerg] a duplicate default server for 0.0.0.0:80”,   <br>
> open `/etc/nginx/sites-available/default` and remove `default_server` from the `listen` lines:

```bash
sudo nano /etc/nginx/sites-available/default

# Change:
listen 80 default_server;
listen [::]:80 default_server;

# To:
listen 80;
listen [::]:80;

# Save & Exit, then test and reload Nginx:
sudo nginx -t
sudo systemctl reload nginx
```