
---

# 🚀 Deploying a Static Website Using Nginx on EC2  

This guide walks you through setting up **Nginx** on your **EC2 instance** to serve **static files** from a cloned GitHub repository.  

## 📌 Steps to Set Up Nginx for Static File Hosting  

###  Clone Your Repository to `/var/www`
First, navigate to the `/var/www` directory and clone your project repository:  

```bash
cd /var/www
sudo git clone https://github.com/your-username/your-repo.git myproject
```

> **Make sure to replace** `your-username` and `your-repo` with your actual GitHub username and repository name.  

###  Create a New Nginx Configuration File  
Create a new configuration file inside `/etc/nginx/sites-available/`:  

```bash
sudo nano /etc/nginx/sites-available/myproject
```

###  Add the Following Nginx Configuration  

```nginx
server {
    listen 80;
    server_name ec2-51-20-65-166.eu-north-1.compute.amazonaws.com;

    root /var/www/myproject;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;

    location = /404.html {
        root /var/www/myproject;
    }

    access_log /var/log/nginx/myproject_access.log;
    error_log /var/log/nginx/myproject_error.log;
}
```

> **Modify the `server_name` field** to match your EC2 public domain or custom domain.  
> Ensure that the **root path** points to the correct directory inside `/var/www`.  

###  Enable the Site Configuration  
Now, create a symbolic link in `sites-enabled` so that Nginx can recognize your new configuration:  

```bash
sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled/
```

###  Remove the Default Nginx Configuration  
By default, Nginx comes with a **default configuration**. To prevent conflicts, remove it:  

```bash
sudo rm /etc/nginx/sites-enabled/default
```

### Test and Restart Nginx  
Before restarting Nginx, ensure your configuration syntax is correct:  

```bash
sudo nginx -t
```

If no errors are found, restart Nginx:  

```bash
sudo systemctl restart nginx
```

### inal Verification  
Your **static website** should now be accessible at:  

```
http://ec2-51-20-65-166.eu-north-1.compute.amazonaws.com
```

Try opening it in your browser! 🎉  

---

## 🔥 Troubleshooting  

**1. Nginx Not Restarting?**  
Check the logs for potential issues:  
```bash
sudo journalctl -xe
sudo systemctl status nginx
```

**2. Permission Issues on `/var/www`?**  
If Nginx cannot access the files, give proper permissions:  
```bash
sudo chmod -R 755 /var/www/myproject
sudo chown -R www-data:www-data /var/www/myproject
```

**3. Need HTTPS?**  
For SSL/TLS, use Let's Encrypt:  
```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com
```


