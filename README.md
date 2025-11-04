# 🚀 Hosting a React App on VPS (Nginx)

This guide explains **step-by-step** how to host a React app on a **VPS using Nginx**, and how to **update your build easily** later.

---

## ⚙️ Prerequisites
- VPS with **Ubuntu/Debian** (any Linux server)
- **SSH access** (username, password, or private key)
- A built **React project** (Vite or CRA)
- Optional: **Domain** (for SSL setup)

---

## 🧩 1. Connect to Your VPS
```bash
ssh username@your_server_ip
```

**Example:**
```bash
ssh example@122.66.195.115
```

---

## 📦 2. Install Required Packages
```bash
sudo apt update
sudo apt install nginx -y
```

Start and enable Nginx:
```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```

---

## 🏗️ 3. Build Your React App Locally
On your **local computer**, inside your project folder:
```bash
npm run build
```

This generates a `dist` folder (or `build` folder if using CRA).

---

## 📤 4. Upload Build to VPS
Use **scp** from your local machine:
```bash
scp -r dist/* username@your_server_ip:/home/username/dist
```

Then, inside your VPS terminal:
```bash
sudo rm -rf /var/www/html/*
sudo mv /home/username/dist/* /var/www/html/
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

---

## ⚙️ 5. Configure Nginx
Edit the default Nginx site config:
```bash
sudo nano /etc/nginx/sites-available/default
```

Paste this:
```nginx
server {
    listen 80;
    server_name _;

    root /var/www/html;
    index index.html;

    location /assets/ {
        try_files $uri =404;
    }

    location / {
        try_files $uri /index.html;
    }
}
```

Test and reload:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

Now visit your **VPS IP** — your app should be live! 🎉

---

## 🌐 6. Connect Domain (Optional)
In your **domain DNS** (GoDaddy, Namecheap, etc):

| Type | Name | Value (IP) | TTL |
|------|------|-------------|-----|
| A | @ | your_server_ip | 1 hour |
| A | www | your_server_ip | 1 hour |

Wait for propagation (5–30 min).

---

## 🔒 7. Add Free SSL (Optional)
Install Certbot:
```bash
sudo apt install certbot python3-certbot-nginx -y
```

Run SSL setup:
```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

Certbot automatically configures HTTPS for you.

---

## 🔁 8. Update Your React App Later
Each time you make new changes:

1. **Build again:**
    ```bash
    npm run build
    ```

2. **Upload new build:**
    ```bash
    scp -r dist/* username@your_server_ip:/home/username/dist_new
    ```

3. **Replace old build:**
    ```bash
    sudo rm -rf /var/www/html/*
    sudo mv /home/username/dist_new/* /var/www/html/
    sudo chown -R www-data:www-data /var/www/html
    sudo chmod -R 755 /var/www/html
    sudo systemctl reload nginx
    ```

✅ Done — your new version is live instantly!

---

## 💡 Tips
- Set `base: './'` in `vite.config.js` (not `/`)
- To back up your old build:
  ```bash
  sudo mv /var/www/html /var/www/html_backup_$(date +%F)
  ```
- Once everything’s stable, increase TTL to **12–24h**

---

## ✅ Summary
- Nginx serves your React app from `/var/www/html`
- No Node/Express needed for static hosting
- Updates = just **build → upload → reload**
- Optional SSL via **Certbot**

---

**Author:** [Abubakar Aijaz](https://github.com/ABUBAKARKHAN-Stack)  
