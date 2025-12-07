#nginx #config

#### 🔹 Основные компоненты
```ini
# Основная структура конфигурации
# main context - глобальные настройки
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# events context - обработка соединений
events {
    worker_connections 1024;
    use epoll;
    multi_accept on;
}

# http context - HTTP сервер
http {
    # server context - виртуальные хосты
    server {
        listen 80;
        server_name example.com;
        
        # location context - обработка URI
        location / {
            root /var/www/html;
            index index.html;
        }
    }
}
```
---
#### 🔹 Базовая конфигурация
##### 🔸 Основной файл `nginx.conf`
```ini
# /etc/nginx/nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
    use epoll;
    multi_accept on;
}

http {
    # Основные настройки
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    
    # Логирование
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
    
    access_log /var/log/nginx/access.log main;
    
    # Производительность
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    
    # Безопасность
    server_tokens off;
    
    # Gzip сжатие
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript 
               application/javascript application/xml+rss 
               application/json;
    
    # Подключение сайтов
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

##### 🔸 Простая конфигурация сайта
```ini
# /etc/nginx/sites-available/example.com
server {
    listen 80;
    listen [::]:80;
    
    server_name example.com www.example.com;
    root /var/www/example.com;
    index index.html index.htm index.php;
    
    # Основное расположение
    location / {
        try_files $uri $uri/ =404;
    }
    
    # Обработка PHP
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.4-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
    
    # Статические файлы
    location ~* \.(css|js|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # Логи для конкретного сайта
    access_log /var/log/nginx/example.com.access.log;
    error_log /var/log/nginx/example.com.error.log;
}
```
---
#### 🔹 SSL/TLS и HTTPS конфигурация
##### 🔸 SSL сертификат с Let's Encrypt
```ini
server {
    listen 80;
    server_name example.com www.example.com;
    
    # Редирект на HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    
    server_name example.com www.example.com;
    root /var/www/example.com;
    
    # SSL сертификаты
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    
    # SSL настройки
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    
    # HSTS
    add_header Strict-Transport-Security "max-age=63072000" always;
    
    # OCSP Stapling
    ssl_stapling on;
    ssl_stapling_verify on;
    ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
    resolver 8.8.8.8 8.8.4.4 valid=300s;
    resolver_timeout 5s;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```

##### 🔸 Продвинутая SSL конфигурация
```ini
# /etc/nginx/snippets/ssl-params.conf
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;
ssl_prefer_server_ciphers off;

ssl_session_cache shared:SSL:50m;
ssl_session_timeout 1d;
ssl_session_tickets off;

# DH параметры
ssl_dhparam /etc/nginx/dhparam.pem;

# OCSP Stapling
ssl_stapling on;
ssl_stapling_verify on;

# DNS resolver
resolver 1.1.1.1 1.0.0.1 8.8.8.8 8.8.4.4 208.67.222.222 208.67.220.220 valid=60s;
resolver_timeout 2s;

# Использование в конфиге сайта:
server {
    listen 443 ssl http2;
    
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/private.key;
    
    include /etc/nginx/snippets/ssl-params.conf;
    
    # Остальная конфигурация
}
```
---
#### 🔹 Обратное проксирование
##### 🔸 Простое проксирование
```ini
server {
    listen 80;
    server_name api.example.com;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

##### 🔸 Проксирование с кешированием
```ini
# Настройка кеша
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=10g 
                 inactive=60m use_temp_path=off;

server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_cache my_cache;
        proxy_pass http://backend;
        
        # Настройки кеширования
        proxy_cache_valid 200 302 10m;
        proxy_cache_valid 404 1m;
        proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
        proxy_cache_background_update on;
        proxy_cache_lock on;
        
        # Заголовки кеша
        add_header X-Cache-Status $upstream_cache_status;
        
        # Стандартные заголовки прокси
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    # Очистка кеша
    location ~ /purge(/.*) {
        allow 127.0.0.1;
        deny all;
        proxy_cache_purge my_cache $1;
    }
}
```
---
#### 🔹 Балансировка нагрузки
##### 🔸 Простая балансировка
```ini
upstream backend {
    server 192.168.1.10:8000;
    server 192.168.1.11:8000;
    server 192.168.1.12:8000;
}

server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
##### 🔸 Продвинутая балансировка
```ini
upstream api_backend {
    # Различные методы балансировки
    least_conn;  # или ip_hash, hash, random
    
    server 10.0.0.1:8000 weight=3 max_fails=3 fail_timeout=30s;
    server 10.0.0.2:8000 weight=2 max_fails=3 fail_timeout=30s;
    server 10.0.0.3:8000 weight=1 max_fails=3 fail_timeout=30s backup;
    server 10.0.0.4:8000 down;  # временно отключен
    
    # Проверка здоровья (Nginx Plus)
    # health_check;
    
    # Keepalive соединения
    keepalive 32;
    keepalive_requests 100;
    keepalive_timeout 60s;
}

upstream static_backend {
    # Консистентное хеширование
    hash $request_uri consistent;
    
    server 10.0.1.1:8080;
    server 10.0.1.2:8080;
    server 10.0.1.3:8080;
}

server {
    listen 80;
    server_name example.com;
    
    # API запросы
    location /api/ {
        proxy_pass http://api_backend;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        include /etc/nginx/proxy_params;
    }
    
    # Статические файлы
    location /static/ {
        proxy_pass http://static_backend;
        include /etc/nginx/proxy_params;
    }
}
```
---
#### 🔹 Ограничения и безопасность
##### 🔸 Rate Limiting
```ini
# Зоны для ограничения скорости
limit_req_zone $binary_remote_addr zone=login:10m rate=5r/m;
limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
limit_req_zone $binary_remote_addr zone=global:10m rate=100r/m;

# Ограничение соединений
limit_conn_zone $binary_remote_addr zone=conn_limit_per_ip:10m;
limit_conn_zone $server_name zone=conn_limit_per_server:10m;

server {
    listen 80;
    server_name example.com;
    
    # Глобальные ограничения
    limit_conn conn_limit_per_ip 10;
    limit_conn conn_limit_per_server 100;
    
    # Основной контент
    location / {
        limit_req zone=global burst=20 nodelay;
        try_files $uri $uri/ =404;
    }
    
    # API с жёсткими ограничениями
    location /api/ {
        limit_req zone=api burst=5 nodelay;
        proxy_pass http://backend;
    }
    
    # Форма входа
    location /login {
        limit_req zone=login burst=3 nodelay;
        proxy_pass http://backend;
    }
}
```
##### 🔸 Безопасность и заголовки
```ini
server {
    listen 443 ssl http2;
    server_name example.com;
    
    # Безопасные заголовки
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    
    # Скрытие версии сервера
    server_tokens off;
    
    # Запрет доступа к скрытым файлам
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }
    
    # Запрет на исполняемые файлы в uploads
    location ~* /uploads/.*\.php$ {
        deny all;
    }
    
    # Ограничение размера тела запроса
    client_max_body_size 10M;
    
    # Защита от медленных атак
    client_body_timeout 10s;
    client_header_timeout 10s;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```
##### 🔸 Блокировка и фильтрация
```ini
# /etc/nginx/conf.d/security.conf

# Блокировка по User-Agent
map $http_user_agent $blocked_user_agent {
    ~*bot 1;
    ~*crawler 1;
    ~*spider 1;
    ~*scraper 1;
    default 0;
}

# Блокировка по IP
geo $blocked_ip {
    default 0;
    192.168.1.0/24 1;
    10.0.0.0/8 1;
}

# Блокировка по Referer
map $http_referer $blocked_referer {
    ~*spam-site\.com 1;
    ~*malicious\.org 1;
    default 0;
}

server {
    listen 80;
    server_name example.com;
    
    # Применение блокировок
    if ($blocked_user_agent) {
        return 403;
    }
    
    if ($blocked_ip) {
        return 403;
    }
    
    if ($blocked_referer) {
        return 403;
    }
    
    # Блокировка определённых методов
    if ($request_method !~ ^(GET|HEAD|POST)$) {
        return 405;
    }
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```
---
#### 🔹 Микросервисы и маршрутизация
##### 🔸 Конфигурация для микросервисной архитектуры
```ini
upstream user_service {
    server user-service-1:8001;
    server user-service-2:8001;
    keepalive 32;
}

upstream order_service {
    server order-service-1:8002;
    server order-service-2:8002;
    keepalive 32;
}

upstream product_service {
    server product-service-1:8003;
    server product-service-2:8003;
    keepalive 32;
}

upstream frontend {
    server frontend-1:3000;
    server frontend-2:3000;
    keepalive 16;
}

server {
    listen 80;
    server_name api.example.com;
    
    # Общие настройки для всех API
    location /api/ {
        # Заголовки для микросервисов
        proxy_set_header X-Request-ID $request_id;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $host;
        
        # HTTP/1.1 для keepalive
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        
        # Таймауты
        proxy_connect_timeout 5s;
        proxy_send_timeout 10s;
        proxy_read_timeout 30s;
    }
    
    # Маршрутизация по сервисам
    location /api/users/ {
        proxy_pass http://user_service/;
    }
    
    location /api/orders/ {
        proxy_pass http://order_service/;
    }
    
    location /api/products/ {
        proxy_pass http://product_service/;
    }
    
    # WebSocket поддержка
    location /api/ws/ {
        proxy_pass http://user_service/ws/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
    
    # Health checks
    location /health {
        access_log off;
        return 200 "OK\n";
        add_header Content-Type text/plain;
    }
}

# Frontend приложение
server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_pass http://frontend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    
    # Статические файлы напрямую
    location /static/ {
        alias /var/www/static/;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```
---
#### 🔹 Мониторинг и логирование
##### 🔸 Продвинутое логирование
```ini
# Кастомные форматы логов
log_format detailed '$remote_addr - $remote_user [$time_local] '
                   '"$request" $status $bytes_sent '
                   '"$http_referer" "$http_user_agent" '
                   'rt=$request_time uct="$upstream_connect_time" '
                   'uht="$upstream_header_time" urt="$upstream_response_time"';

log_format json_logs escape=json '{'
                    '"time_local":"$time_local",'
                    '"remote_addr":"$remote_addr",'
                    '"remote_user":"$remote_user",'
                    '"request":"$request",'
                    '"status": "$status",'
                    '"body_bytes_sent":"$body_bytes_sent",'
                    '"request_time":"$request_time",'
                    '"http_referrer":"$http_referer",'
                    '"http_user_agent":"$http_user_agent",'
                    '"upstream_addr":"$upstream_addr",'
                    '"upstream_response_time":"$upstream_response_time",'
                    '"upstream_status":"$upstream_status"'
                    '}';

server {
    listen 80;
    server_name example.com;
    
    # Разные логи для разных location
    location /api/ {
        access_log /var/log/nginx/api.access.log json_logs;
        error_log /var/log/nginx/api.error.log;
        proxy_pass http://backend;
    }
    
    location / {
        access_log /var/log/nginx/main.access.log detailed;
        try_files $uri $uri/ =404;
    }
    
    # Исключение статических файлов из логов
    location ~* \.(css|js|jpg|jpeg|png|gif|ico)$ {
        expires 1y;
        access_log off;
    }
}
```
##### 🔸 Status модуль и метрики
```ini
# Встроенный status модуль
server {
    listen 127.0.0.1:8080;
    server_name localhost;
    
    location /nginx_status {
        stub_status on;
        access_log off;
        allow 127.0.0.1;
        deny all;
    }
    
    location /fpm_status {
        fastcgi_pass unix:/var/run/php/php8.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
        allow 127.0.0.1;
        deny all;
    }
}
```
---
#### 🔹 Оптимизация производительности 
##### 🔸 Настройки для высоконагруженных систем
```ini
# /etc/nginx/nginx.conf
user nginx;
worker_processes auto;
worker_cpu_affinity auto;
worker_rlimit_nofile 65535;

error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 4096;
    use epoll;
    multi_accept on;
    accept_mutex off;
}

http {
    # Оптимизация TCP
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    
    # Буферы
    client_body_buffer_size 128k;
    client_header_buffer_size 1k;
    large_client_header_buffers 4 4k;
    output_buffers 1 32k;
    postpone_output 1460;
    
    # Таймауты
    client_body_timeout 12;
    client_header_timeout 12;
    keepalive_timeout 65;
    keepalive_requests 1000;
    send_timeout 10;
    
    # Сжатие
    gzip on;
    gzip_vary on;
    gzip_min_length 10240;
    gzip_proxied expired no-cache no-store private must-revalidate max-age=0;
    gzip_types
        text/plain
        text/css
        text/xml
        text/javascript
        application/javascript
        application/xml+rss
        application/json;
    
    # Кеширование открытых файлов
    open_file_cache max=200000 inactive=20s;
    open_file_cache_valid 30s;
    open_file_cache_min_uses 2;
    open_file_cache_errors on;
    
    include /etc/nginx/conf.d/*.conf;
}
```
##### 🔸 Кеширование статического контента
```ini
server {
    listen 80;
    server_name static.example.com;
    root /var/www/static;
    
    # Агрессивное кеширование изображений
    location ~* \.(jpg|jpeg|png|gif|ico|webp)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        add_header Vary "Accept-Encoding";
        
        # Предварительно сжатые файлы
        location ~* \.(png|jpg|jpeg|gif|webp)$ {
            try_files $uri =404;
        }
    }
    
    # Кеширование CSS и JS
    location ~* \.(css|js)$ {
        expires 30d;
        add_header Cache-Control "public";
        add_header Vary "Accept-Encoding";
        
        # Gzip предварительно сжатых файлов
        gzip_static on;
    }
    
    # Шрифты
    location ~* \.(woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        add_header Access-Control-Allow-Origin "*";
    }
}
```
---
#### 🔹 Реальные кейсы применения
##### 🔸 Высоконагруженный интернет-магазин
```ini
# Основная конфигурация магазина
upstream app_servers {
    least_conn;
    server app1.internal:8000 max_fails=3 fail_timeout=30s;
    server app2.internal:8000 max_fails=3 fail_timeout=30s;
    server app3.internal:8000 max_fails=3 fail_timeout=30s;
    keepalive 32;
}

upstream search_servers {
    server search1.internal:9200;
    server search2.internal:9200;
    keepalive 16;
}

# Кеш для статики
proxy_cache_path /var/cache/nginx/static levels=1:2 keys_zone=static:100m 
                 max_size=10g inactive=7d use_temp_path=off;

# Кеш для API
proxy_cache_path /var/cache/nginx/api levels=1:2 keys_zone=api:50m 
                 max_size=1g inactive=1h use_temp_path=off;

# Rate limiting
limit_req_zone $binary_remote_addr zone=search:10m rate=10r/s;
limit_req_zone $binary_remote_addr zone=checkout:10m rate=2r/s;

server {
    listen 443 ssl http2;
    server_name shop.example.com;
    
    # SSL конфигурация
    include /etc/nginx/ssl-config.conf;
    
    # Главная страница и каталог
    location / {
        proxy_pass http://app_servers;
        proxy_cache api;
        proxy_cache_valid 200 5m;
        proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
        include /etc/nginx/proxy-headers.conf;
    }
    
    # Поиск товаров
    location /search {
        limit_req zone=search burst=20 nodelay;
        proxy_pass http://search_servers;
        proxy_cache api;
        proxy_cache_valid 200 10m;
        include /etc/nginx/proxy-headers.conf;
    }
    
    # Корзина и оформление заказа
    location /checkout {
        limit_req zone=checkout burst=5 nodelay;
        proxy_pass http://app_servers;
        proxy_no_cache 1;
        proxy_cache_bypass 1;
        include /etc/nginx/proxy-headers.conf;
    }
    
    # Статические файлы с CDN
    location /static/ {
        proxy_pass http://cdn.example.com/;
        proxy_cache static;
        proxy_cache_valid 200 7d;
        add_header X-Cache-Status $upstream_cache_status;
    }
    
    # API для мобильных приложений
    location /api/ {
        limit_req zone=api burst=50 nodelay;
        proxy_pass http://app_servers/api/;
        include /etc/nginx/proxy-headers.conf;
        
        # CORS для мобильных приложений
        add_header Access-Control-Allow-Origin "https://mobile.example.com";
        add_header Access-Control-Allow-Methods "GET, POST, OPTIONS";
        add_header Access-Control-Allow-Headers "Authorization, Content-Type";
    }
}
```
##### 🔸 Медиа-сервер со стримингом
```ini
# RTMP модуль для стриминга (требует сборки с модулем)
rtmp {
    server {
        listen 1935;
        chunk_size 4000;
        
        application live {
            live on;
            
            # Запись стрима
            record all;
            record_path /var/recordings;
            record_unique on;
            record_suffix .flv;
            
            # HLS нарезка
            hls on;
            hls_path /var/hls;
            hls_fragment 3;
            hls_playlist_length 60;
            
            # Аутентификация стримера
            on_publish http://localhost:8080/auth;
            
            # Уведомления
            on_play http://localhost:8080/on_play;
            on_publish_done http://localhost:8080/on_publish_done;
        }
    }
}

http {
    server {
        listen 80;
        server_name stream.example.com;
        
        # HLS плейлисты
        location /hls {
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }
            root /var;
            add_header Cache-Control no-cache;
            add_header Access-Control-Allow-Origin *;
        }
        
        # Статистика RTMP
        location /stat {
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
            allow 127.0.0.1;
            deny all;
        }
        
        # Веб-интерфейс
        location / {
            root /var/www/streaming;
            index index.html;
        }
    }
}
```
##### 🔸 Конфигурация для WordPress
```ini
# WordPress multisite
server {
    listen 443 ssl http2;
    server_name example.com *.example.com;
    root /var/www/wordpress;
    index index.php;
    
    # SSL конфигурация
    include /etc/nginx/ssl-config.conf;
    
    # Security headers
    include /etc/nginx/security-headers.conf;
    
    # Кеширование
    set $skip_cache 0;
    
    # Не кешировать админку
    if ($request_uri ~* "/wp-admin/|/xmlrpc.php|wp-.*.php|/feed/|index.php|sitemap(_index)?.xml") {
        set $skip_cache 1;
    }
    
    # Не кешировать для залогиненных пользователей
    if ($http_cookie ~* "comment_author|wordpress_[a-f0-9]+|wp-postpass|wordpress_no_cache|wordpress_logged_in") {
        set $skip_cache 1;
    }
    
    location / {
        try_files $uri $uri/ /index.php?$args;
    }
    
    # PHP обработка
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php/php8.4-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        
        # Кеширование
        fastcgi_cache_bypass $skip_cache;
        fastcgi_no_cache $skip_cache;
        fastcgi_cache WORDPRESS;
        fastcgi_cache_valid 60m;
        add_header X-Cache $upstream_cache_status;
    }
    
    # Статические файлы
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        access_log off;
    }
    
    # Защита важных файлов
    location ~* /(wp-config.php|readme.html|license.txt) {
        deny all;
    }
    
    # Защита uploads от PHP
    location ~* /uploads/.*\.php$ {
        deny all;
    }
}

# FastCGI кеш
fastcgi_cache_path /var/cache/nginx levels=1:2 keys_zone=WORDPRESS:100m inactive=60m;
fastcgi_cache_key "$scheme$request_method$host$request_uri";
```
##### 🔸 API Gateway конфигурация
```ini
# Upstream сервисы
include /etc/nginx/upstreams/*.conf;

# Rate limiting для разных типов пользователей
limit_req_zone $binary_remote_addr zone=guest:10m rate=10r/s;
limit_req_zone $http_x_api_key zone=apikey:10m rate=100r/s;

# Кеширование
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=api_cache:100m 
                 max_size=10g inactive=60m use_temp_path=off;

# Логирование API
log_format api_log '$remote_addr - $remote_user [$time_local] "$request" '
                   '$status $body_bytes_sent "$http_referer" "$http_user_agent" '
                   '"$http_x_api_key" "$http_x_request_id" '
                   'rt=$request_time uct="$upstream_connect_time" '
                   'uht="$upstream_header_time" urt="$upstream_response_time"';

server {
    listen 443 ssl http2;
    server_name api.example.com;
    
    access_log /var/log/nginx/api.log api_log;
    
    # Общие настройки для API
    add_header X-Request-ID $request_id always;
    
    # Аутентификация
    location /auth/ {
        internal;
        proxy_pass http://auth_service/validate;
        proxy_pass_request_body off;
        proxy_set_header Content-Length "";
        proxy_set_header X-Original-URI $request_uri;
        proxy_set_header X-Original-Method $request_method;
        proxy_set_header X-API-Key $http_x_api_key;
    }
    
    # Public API (с ограничениями)
    location /api/v1/public/ {
        limit_req zone=guest burst=20 nodelay;
        
        proxy_pass http://public_api/;
        proxy_cache api_cache;
        proxy_cache_valid 200 5m;
        proxy_cache_key "$request_uri|$http_accept";
        
        include /etc/nginx/proxy-headers.conf;
    }
    
    # Private API (требует аутентификации)
    location /api/v1/private/ {
        # Проверка аутентификации
        auth_request /auth/;
        
        limit_req zone=apikey burst=100 nodelay;
        
        proxy_pass http://private_api/;
        proxy_no_cache 1;
        proxy_cache_bypass 1;
        
        include /etc/nginx/proxy-headers.conf;
    }
    
    # WebSocket соединения
    location /api/v1/ws/ {
        auth_request /auth/;
        
        proxy_pass http://websocket_service/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_read_timeout 86400;
    }
    
    # Документация API
    location /docs/ {
        root /var/www/api-docs;
        try_files $uri $uri/ =404;
    }
    
    # Метрики и мониторинг
    location /metrics {
        allow 10.0.0.0/8;
        deny all;
        
        proxy_pass http://monitoring_service/nginx-metrics;
    }
}
```
#### 🔹 Мониторинг и отладка
##### 🔸  Настройка логирования для отладки
```ini
# /etc/nginx/conf.d/debug.conf
server {
    listen 80;
    server_name debug.local;
    
    # Детальное логирование
    error_log /var/log/nginx/debug.log debug;
    access_log /var/log/nginx/debug_access.log detailed;
    
    # Включение отладочной информации
    location / {
        # Добавление отладочных заголовков
        add_header X-Debug-URI $uri;
        add_header X-Debug-Args $args;
        add_header X-Debug-Request-ID $request_id;
        add_header X-Debug-Time $time_local;
        
        proxy_pass http://backend;
        
        # Логирование переменных
        access_log /var/log/nginx/variables.log 
                   'uri=$uri args=$args request_time=$request_time';
    }
}
```
---