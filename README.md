## Deploying Full Stack Project on Hostinger VPS

- Preparing the VPS Environment
- Deploying the Express and Node.js Backend
- Deploying the Next-js Frontend
- Deploying the React admin
- Configuring Nginx as a Reverse Proxy
- Setting Up SSL Certificates
- how to Remove old project
- extra documents

### 1. Preparing the VPS Environment

<!-- #### Get you VPS Hosting here : [Hostinger VPS](https://greatstack.dev/go/hostinger-vps) -->

Log in to Your VPS in Terminal

```bash
 ssh root@your_vps_ip
```

Update and Upgrade Your System

```bash
  sudo apt update
```

```bash
  sudo apt upgrade -y
```

Install Node.js and npm ( if not pre-installed)

```bash
  curl -fsSL https://deb.nodesource.com/setup_20.x | sudo bash -
```

```bash
  sudo apt-get install -y nodejs
```

Install Git

```bash
  sudo apt install -y git
```

### 2. Pointing Domain to Remote Server

```bash
 --------------------------------------------
Type    Host/Name    Content    TTL

-----------------------------------------------
A	   @	0	88.222.222.222	14400
CNAME www	0	maindomain.com	14400

A	  admin     0  88.222.215.224  	14400
CNAME www.admin	0  maindomain.com	14400

A	  api	  0	 88.222.215.224	14400
CNAME www.api 0	 maindomain.com	14400

```

### 3. Deploying the Express and Node.js Backend

Clone Your Backend Repository

```bash
 mkdir /var/www
```

```bash
 cd /var/www
```

```bash
 git clone https://github.com/yourusername/your-repo.git
```

```bash
 cd your-repo/backend
```

Install Dependencies

```bash
 npm install
```

Create .env file & configure Environment Variables

```bash
 nano .env
```

add environment variables then save and exit (Ctrl + O, Enter, Ctrl + X).

Build Your Project

```bash
 npm run build
```

Installing pm2 to Start Backend

```bash
 npm install -g pm2
```

```bash
 pm2 start dist/server.js --name project-backend
```

Start Backend on startup

```bash
 pm2 startup
```

```bash
 pm2 save
```

```bash
 pm2 list
```

Allowing backend port in firewall

```bash
 sudo ufw status
```

If firewall is disable then enable it using

```bash
 sudo ufw enable
```

```bash
 sudo ufw allow 'OpenSSH'
```

```bash
 sudo ufw allow 5000
```

Now check the backend in ssh-IP-address:5000

### 3. Deploying the Next-js front-end

Creating Build of React Applications

```bash
 cd /var/www
```

```bash
 git clone https://github.com/yourusername/your-repo.git
```

```bash
 cd path-to-your-frontend
```

```bash
 npm install
```

If you have ".env" file in your project

Create .env file and paste the variables

```bash
 nano .env
```

Create build of project

```bash
 npm run build
```

Serve Next.js Frontend with PM2

```bash
 pm2 start npm --name "labonehospital-frontend" -- start
```

```bash
 pm2 startup
```

```bash
 pm2 save
```

```bash
 pm2 list
```

### 4. Deploying the React Admin

Creating Build of React Applications

```bash
 cd /var/www
```

```bash
 git clone https://github.com/yourusername/your-repo.git
```

```bash
 cd path-to-your-admin
```

```bash
 npm install
```

If you have ".env" file in your project

Create .env file and paste the variables

```bash
 nano .env
```

Create build of project

```bash
 npm run build
```

Install Nginx

```bash
 sudo apt install -y nginx
```

adding Nginx in firewall

```bash
 sudo ufw status
```

```bash
 sudo ufw allow 'Nginx Full'
```

#### Configure Nginx for React Next-js Frontends

```bash
 nano /etc/nginx/sites-available/yourdomain.com.conf
```

```bash
 server {
    listen 80;
    server_name frontend.labonehospital.com www.frontend.labonehospital.com;

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

Save and exit (Ctrl + O, Enter, Ctrl + X ).

```bash
 ln -s /etc/nginx/sites-available/frontend.labonehospital.com.conf /etc/nginx/sites-enabled/
```

```bash
 nginx -t
```

#### Configure Nginx for React Admin

```bash
 nano /etc/nginx/sites-available/admin.yourdomain.com.conf
```

```bash
server {
    listen 80;
    server_name admin.yourdomain.com www.admin.yourdomain.com;

    location / {
        root /var/www/react-app-2/dist;
        try_files $uri /index.html;
    }
}
```

Create symbolic links to enable the sites.

```bash
ln -s /etc/nginx/sites-available/yourdomain.com.conf /etc/nginx/sites-enabled/
```

```bash
nginx -t
```

Test the Nginx configuration for syntax errors.

```bash
systemctl restart nginx
```

### 5. Configuring Nginx as a Reverse Proxy

#### Update Backend Nginx Configuration

```bash
nano /etc/nginx/sites-available/api.yourdomain.com.conf
```

```bash
server {
    listen 80;
    server_name api.yourdomain.com;

    location / {
        proxy_pass http://localhost:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Create symbolic links to enable the sites.

```bash
ln -s /etc/nginx/sites-available/api.yourdomain.com.conf /etc/nginx/sites-enabled/
```

Restart nginx

```bash
systemctl restart nginx
```

### Connect Domain Name with Website

Point all your domain & sub-domain on VPS IP address by adding DNS records in your domain manager

Now your website will be live on domain name

### 6. Setting Up SSL Certificates

Install Certbot

```bash
sudo apt install -y certbot python3-certbot-nginx
```

Obtain SSL Certificates

```bash
certbot --nginx -d yourdomain1.com -d www.yourdomain1.com -d admin.yourdomain.com -d www.admin.yourdomain.com -d api.yourdomain.com -d www.admin.api.yourdomain.com
```

```bash
certbot --nginx -d yourdomain1.com -d www.yourdomain1.com
```

```bash
certbot --nginx -d admin.yourdomain.com -d www.admin.yourdomain.com
```

```bash
certbot --nginx -d api.yourdomain.com -d www.api.yourdomain.com
```

Verify Auto-Renewal

```bash
certbot renew --dry-run
```

---

---

---

### (optional think) How to Remove Old Project:

Stop the Old Project in PM2

```bash
pm2 list

```

```bash
pm2 stop old-project-name
```

```bash
pm2 delete old-project-name
```

Delete the Old Project Files

```bash
cd /var/www
```

```bash
rm -rf your-old-project-folder
```

Disable the Firewall Rule for Port (5000)

```bash
sudo ufw status numbered
```

```bash
sudo ufw delete 3
```

```bash
sudo ufw status
```

### If you still need help in deployment:

YotubeVideo : https://www.youtube.com/watch?v=o2J_jdKBLI4

document1: https://github.com/GreatStackDev/notes/blob/main/Deploy_MERN_on_VPS.md

document2: https://codebhaiya.com/blog/how-to-point-domain-and-host-a-next.js-app-in-production-on-an-ubuntu-vps

chatgpt solution1: https://chatgpt.com/share/671a8288-8fc8-8001-b31e-b3c4de08b504

chatgpt solution2: https://chatgpt.com/share/671a82b7-cb9c-8001-975b-449ead516e70
