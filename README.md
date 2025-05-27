
## 🖥️ Custom Localhost Domain Setup (e.g., `myapp.local`)

This guide explains how to map a custom domain like `myapp.local` to your local development server instead of using `localhost:8000`.

----------

### 📌 Step 1: Edit `hosts` File

#### ✅ Windows

1.  Open **Notepad as Administrator**.
    
2.  Open the file:
    
    `C:\Windows\System32\drivers\etc\hosts` 
    
3.  Add the following line at the bottom:
    
    `127.0.0.1 myapp.local` 
    

#### ✅ macOS / Linux

`sudo nano /etc/hosts` 

Add the line:

`127.0.0.1 myapp.local` 

----------

### 📁 Step 2: Configure Web Server

#### 🛠️ Apache (Windows: XAMPP / macOS / Linux)

1.  Locate the `httpd-vhosts.conf` file:
    
    **Windows (XAMPP)**:
    
    `C:\xampp\apache\conf\extra\httpd-vhosts.conf` 
    
    **macOS / Linux**:

    `/etc/apache2/sites-available/myapp.local.conf` 
    
2.  Add a virtual host configuration:
   ```bash
   <VirtualHost *:80>
    ServerName myapp.local
    DocumentRoot "C:/xampp/htdocs/myapp/public"   # for Windows
    # or for Linux/macOS:
    # DocumentRoot "/home/your-user/code/myapp/public"

    <Directory "C:/xampp/htdocs/myapp/public">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
   ```
   
3.  Enable the site (Linux/macOS only):
    
`sudo a2ensite myapp.local.conf`
`sudo systemctl reload apache2` 

4.  Restart Apache:
    

-   On **Windows**, use XAMPP Control Panel.
    
-   On **Linux/macOS**:
    
    `sudo systemctl restart apache2` 
    
----------

#### 🛠️ Nginx (macOS / Linux)

1.  Create a new site config in `/etc/nginx/sites-available/myapp.local`:
    
```bash 
server {
    listen 80;
    server_name myapp.local;

    root /home/your-user/code/myapp/public;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
2.  Enable the site:
   
```bash 
sudo ln -s /etc/nginx/sites-available/myapp.local /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

----------

### 🚀 Step 3: Run Laravel Server (Optional)

If you're using Laravel with built-in PHP server:

`php artisan serve --host=myapp.local --port=8000` 

Then open:

`http://myapp.local:8000` 

If you're using Apache/Nginx, it should work without running `php artisan serve`.

----------

### ✅ Test in Browser

Visit:

`http://myapp.local` 

You should see your Laravel (or PHP) application running.

----------

### 🧾 Notes

-   `myapp.local` is arbitrary — you can use anything like `test.local`, `project.local`, etc.
    
-   Don't forget to **clear your browser cache** or restart your browser if the changes don’t work immediately.
    
-   `.local` is commonly used but you can use `.test` or `.dev` too.
