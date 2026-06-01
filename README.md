# Termux Dokuwiki Installation Guide:

### Step 1: Run this first in Termux
```bash
termux-setup-storage && pkg update && pkg upgrade -y && pkg install proot-distro -y && proot-distro install ubuntu && proot-distro login ubuntu
```

### Step 2: After logging into Ubuntu, paste this:
```bash
apt update && apt upgrade -y && apt install nginx php8.5-fpm php-mbstring php-xml php-gd wget -y && mkdir -p /sdcard/dokuwiki && cd /sdcard/dokuwiki && wget https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz -O dokuwiki.tgz && tar -xzf dokuwiki.tgz --strip-components=1 && rm dokuwiki.tgz && chown -R root:root /sdcard/dokuwiki && chmod -R 777 /sdcard/dokuwiki/data /sdcard/dokuwiki/conf && cat > /data/data/com.termux/files/usr/etc/nginx/nginx.conf << 'EOF'
worker_processes 1;
pid /run/nginx.pid;
events { worker_connections 1024; }
http {
    include mime.types;
    default_type application/octet-stream;
    sendfile on;
    keepalive_timeout 65;
    server {
        listen 8080;
        server_name localhost;
        root /sdcard/dokuwiki;
        index index.php index.html;
        location / { try_files $uri $uri/ /doku.php?$args; }
        location ~ \.php$ {
            fastcgi_pass unix:/run/php/php8.5-fpm.sock;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }
}
EOF
sed -i 's/location \\~/location ~/' /data/data/com.termux/files/usr/etc/nginx/nginx.conf && echo "Installation Complete!"
```

### Step 3: Start DokuWiki (run this after the big command finishes)
```bash
php-fpm8.5 -F & nginx
```

**Done!**  
Open browser → **http://127.0.0.1:8080**

