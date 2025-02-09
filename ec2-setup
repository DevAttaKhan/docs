# 🚀 Setting Up SSH, Nginx & Permissions for Your EC2 Instance

## 📌 **1. Change Permissions for `.pem` File (Windows)**
Before connecting to your EC2 instance from **Windows Command Prompt or PowerShell**, ensure that your `.pem` file has the correct permissions.

Run the following commands in **PowerShell**:

```powershell
icacls E:\aws\dev.pem /inheritance:r
icacls E:\aws\dev.pem /grant:r "$($env:USERNAME):(R)"
icacls E:\aws\dev.pem /remove "NT AUTHORITY\Authenticated Users"
icacls E:\aws\dev.pem /remove "BUILTIN\Users"
```

> **Explanation:** These commands restrict access to the private key so only the current user can read it, improving security.

---

## 🔑 **2. Connect to Your EC2 Instance**
After setting the correct permissions, connect to your EC2 instance using **SSH**:

```sh
ssh -i "E:\aws\dev.pem" ubuntu@ec2-51-20-65-166.eu-north-1.compute.amazonaws.com
```

---

## 🔓 **3. Enable Passwordless SSH Login**
To log in **without specifying the `.pem` file**, generate an **SSH key** on your local machine:

```sh
ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
```
> **Note:** You can omit `-C "your-email@example.com"` if you don’t need an identifier.

After generating the key, copy your **public key (`id_rsa.pub`)** to your EC2 instance:

```sh
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```
> **Alternative (Manual Method):**
> 1. Open the authorized keys file:
>    ```sh
>    nano ~/.ssh/authorized_keys
>    ```
> 2. Paste the contents of `id_rsa.pub` inside and save the file.

Now, you can log in to EC2 using:
```sh
ssh ubuntu@your-ec2-public-ip
```

---

## 📦 **4. Update Your EC2 Instance**
Always update your system packages before installing new software:

```sh
sudo apt update && sudo apt upgrade -y
```

---

## 🌍 **5. Install & Configure Nginx**
To install **Nginx** on your EC2 instance, run:

```sh
sudo apt install nginx -y
sudo systemctl enable nginx  # Auto-start on boot
sudo systemctl start nginx   # Start Nginx immediately
```

### 🔎 **Check if Nginx is Running**
```sh
systemctl status nginx
```
If running, you should see **"active (running)"** in the output.

---

## 🔧 **6. Modify `nginx.conf` File (If Needed)**
### **Change File Permissions Temporarily**
If you need to edit `/etc/nginx/nginx.conf`, grant write permissions temporarily:

```sh
sudo chmod 666 /etc/nginx/nginx.conf
```

### **Change File Ownership (Recommended for Permanent Change)**
If you don't have ownership of `nginx.conf`, change it:

```sh
sudo chown $USER:$USER /etc/nginx/nginx.conf
```

After making changes, **always restart Nginx**:
```sh
sudo systemctl restart nginx
```

---

## 🎯 **Next Steps**
✅ **Set up a domain name and configure SSL with Let's Encrypt.**
✅ **Deploy your Vite-generated React app using Nginx.**
✅ **Optimize Nginx settings for better performance.**

🚀 **Now your EC2 instance is ready for secure access and Nginx hosting!**

