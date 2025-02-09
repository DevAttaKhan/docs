## 🔒 Setting Up **SSL/TLS** with Let’s Encrypt on Nginx (EC2)  

To secure your **EC2 Nginx server** with **SSL/TLS**, we’ll use **Let’s Encrypt** with **Certbot**, a free and automated SSL certificate authority.  

### ✅ **Prerequisites**  
- Your domain name should be pointing to your **EC2 public IP** (via **A Record** in DNS settings).  
- **Nginx is installed and running** on your EC2 instance.  
- **A valid Nginx configuration file** for your domain (as set up earlier).  

---

## 🚀 **Step-by-Step SSL Setup for Nginx**  

### **1️⃣ Install Certbot and SSL Dependencies**
First, install **Certbot** and the Nginx plugin:  

```bash
sudo apt update && sudo apt install certbot python3-certbot-nginx -y
```

---

### **2️⃣ Obtain a Free SSL Certificate**
Run the following command, replacing `yourdomain.com` with your actual domain:  

```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

> If your **domain name is not yet configured**, update your **DNS A Record** to point to your EC2 instance **before running Certbot**.

✅ If the process is successful, you will see:  
```
Congratulations! Your certificate and chain have been saved at:
  /etc/letsencrypt/live/yourdomain.com/fullchain.pem
```

---

### **3️⃣ Automatically Update Nginx Configuration**
Certbot will ask if you want it to automatically configure Nginx for SSL. Select **"Yes"**, and it will:  
- Modify your existing **Nginx config** to use **SSL**.  
- Redirect all HTTP requests to **HTTPS**.  

If you want to do it manually, update your `/etc/nginx/sites-available/myproject` file:  

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name yourdomain.com www.yourdomain.com;

    root /var/www/myproject;
    index index.html;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;
}
```
 **Update configuration for node server**
```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    # Redirect HTTP to HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name yourdomain.com www.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    location / {
        proxy_pass http://127.0.0.1:3000; # Proxy requests to Node.js app running on port 3000
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    error_page 404 /404.html;
}

```

After making changes, **restart Nginx**:  

```bash
sudo systemctl restart nginx
```

---

### **4️⃣ Enable Auto-Renewal for SSL**  
Let’s Encrypt certificates **expire every 90 days**, so enable auto-renewal:  

```bash
sudo certbot renew --dry-run
```

To set up automatic renewal, Certbot **already adds a cron job** to `/etc/cron.d/certbot`, but you can manually check with:  

```bash
sudo systemctl list-timers | grep certbot
```

---

### 🎉 **Done!**  
Your website should now be accessible **securely** via HTTPS:  

```
https://yourdomain.com
```

To verify, you can run:  
```bash
curl -I https://yourdomain.com
```

Let me know if you need any modifications! 🚀
