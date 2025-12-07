#linux #log #monitor

#### 🔹 Системные логи и мониторинг

##### 🔸 journalctl - systemd журналы
```bash
# Просмотр журналов
journalctl                  # все журналы
journalctl -n 50            # последние 50 строк
journalctl -f               # следить в реальном времени
journalctl -r               # в обратном порядке

# Фильтрация по времени
journalctl --since "2024-01-01"
journalctl --since "2024-01-01" --until "2024-01-02"
journalctl --since "1 hour ago"
journalctl --since yesterday

# Фильтрация по службам
journalctl -u nginx.service
journalctl -u ssh.service -f
journalctl _SYSTEMD_UNIT=apache2.service

# Фильтрация по процессам
journalctl _PID=1234
journalctl /usr/bin/nginx
```

##### 🔸 Традиционные логи
```bash
# Основные лог-файлы
tail -f /var/log/syslog         # системные сообщения
tail -f /var/log/messages       # общие системные сообщения
tail -f /var/log/auth.log       # аутентификация
tail -f /var/log/daemon.log     # демоны
tail -f /var/log/kern.log       # ядро

# Анализ логов
grep "ERROR" /var/log/apache2/error.log
grep "Failed" /var/log/auth.log | tail -10
awk '/ERROR/ {print $1, $2, $3}' /var/log/app.log
```
---
#### 🔹 Реальные кейсы мониторинга

##### 🔸 Высокая загрузка CPU
```bash
# Диагностика высокой загрузки CPU
# 1. Проверить общую загрузку
uptime
top
htop

# 2. Найти процессы-пожиратели CPU
ps aux --sort=-%cpu | head -10
top -b -n 1 -o %CPU | head -20

# 3. Подробный анализ конкретного процесса
top -p PID
strace -p PID              # системные вызовы процесса
perf top -p PID            # профилирование процесса

# 4. Мониторинг в реальном времени
watch -n 1 'ps aux --sort=-%cpu | head -10'

# Скрипт для автоматического мониторинга
#!/bin/bash
while true; do
    load=$(uptime | awk '{print $10}' | cut -d, -f1)
    if (( $(echo "$load > 2.0" | bc -l) )); then
        echo "$(date): High load detected: $load"
        ps aux --sort=-%cpu | head -10
    fi
    sleep 60
done
```

##### 🔸 Проблемы с памятью
```bash
# Диагностика проблем с памятью
# 1. Общее состояние памяти
free -h
cat /proc/meminfo

# 2. Процессы-потребители памяти
ps aux --sort=-%mem | head -10
pmap PID                   # карта памяти процесса

# 3. Поиск утечек памяти
valgrind --tool=memcheck --leak-check=full program

# 4. OOM killer
dmesg | grep -i "killed process"
grep "Out of memory" /var/log/syslog

# Мониторинг свободной памяти
#!/bin/bash
while true; do
    mem_free=$(free | grep "^Mem:" | awk '{print ($7/$2)*100}')
    if (( $(echo "$mem_free < 10" | bc -l) )); then
        echo "$(date): Low memory: ${mem_free}% free"
        ps aux --sort=-%mem | head -5
    fi
    sleep 30
done
```

##### 🔸 Высокая нагрузка на диск
```bash
# Диагностика дисковой нагрузки
# 1. Мониторинг I/O
iostat -x 1
iotop -o

# 2. Поиск активных процессов
lsof | grep "/path/to/mount"
fuser -v /path/to/file

# 3. Анализ медленных запросов
# Для MySQL
pt-query-digest /var/log/mysql/slow.log

# Скрипт мониторинга дискового пространства
#!/bin/bash
threshold=90
df -h | awk 'NR>1 {gsub(/%/, "", $5); if($5 > threshold) print $0}' | \
while read line; do
    echo "$(date): Disk usage warning: $line"
    # Найти большие файлы
    mountpoint=$(echo $line | awk '{print $6}')
    find $mountpoint -type f -size +100M -exec ls -lh {} + | sort -k5 -hr | head -10
done
```

##### 🔸 Мониторинг веб-сервера
```bash
# Мониторинг Apache/Nginx
# 1. Количество соединений
ss -tulpn | grep :80 | wc -l
ss -tulpn | grep :443 | wc -l

# 2. Процессы веб-сервера
ps aux | grep apache2 | wc -l
ps aux | grep nginx | wc -l

# 3. Анализ логов доступа
tail -f /var/log/nginx/access.log | grep "404"
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -nr | head -10

# Комплексный скрипт мониторинга веб-сервера
#!/bin/bash
log_file="/var/log/webserver_monitor.log"

check_webserver() {
    # Проверка доступности
    if curl -s --max-time 10 http://localhost > /dev/null; then
        echo "$(date): Web server is responding" >> $log_file
    else
        echo "$(date): Web server is NOT responding" >> $log_file
        # Перезапуск сервера
        systemctl restart nginx
    fi
    
    # Количество активных соединений
    connections=$(ss -tulpn | grep :80 | wc -l)
    echo "$(date): Active connections: $connections" >> $log_file
    
    # Использование памяти веб-сервером
    nginx_mem=$(ps aux | grep nginx | awk '{sum+=$6} END {print sum/1024 " MB"}')
    echo "$(date): Nginx memory usage: $nginx_mem" >> $log_file
}

while true; do
    check_webserver
    sleep 60
done
```

##### 🔸 Мониторинг базы данныx
```bash
# Мониторинг MySQL/PostgreSQL
# 1. Активные соединения
mysql -e "SHOW PROCESSLIST;" | wc -l
sudo -u postgres psql -c "SELECT count(*) FROM pg_stat_activity;"

# 2. Медленные запросы
mysql -e "SHOW VARIABLES LIKE 'slow_query_log';"
tail -f /var/log/mysql/slow.log

# 3. Размер базы данных
mysql -e "SELECT table_schema, ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS 'DB Size in MB' FROM information_schema.tables GROUP BY table_schema;"

# Скрипт мониторинга MySQL
#!/bin/bash
mysql_monitor() {
    # Количество соединений
    connections=$(mysql -e "SHOW STATUS LIKE 'Threads_connected';" | tail -1 | awk '{print $2}')
    max_connections=$(mysql -e "SHOW VARIABLES LIKE 'max_connections';" | tail -1 | awk '{print $2}')
    
    if [ $connections -gt $((max_connections * 80 / 100)) ]; then
        echo "$(date): High MySQL connections: $connections/$max_connections"
    fi
    
    # Заблокированные процессы
    locked=$(mysql -e "SHOW PROCESSLIST;" | grep -i "locked" | wc -l)
    if [ $locked -gt 0 ]; then
        echo "$(date): Found $locked locked MySQL processes"
    fi
}
```

##### 🔸 Универсальный скрипт системного мониторинга
```bash
#!/bin/bash
# system_monitor.sh

LOG_FILE="/var/log/system_monitor.log"
EMAIL="admin@example.com"

log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S'): $1" | tee -a $LOG_FILE
}

check_cpu() {
    load=$(uptime | awk '{print $10}' | cut -d, -f1)
    if (( $(echo "$load > 5.0" | bc -l) )); then
        log_message "HIGH LOAD: $load"
        ps aux --sort=-%cpu | head -5 >> $LOG_FILE
    fi
}

check_memory() {
    mem_usage=$(free | grep "^Mem:" | awk '{printf "%.2f", ($3/$2)*100}')
    if (( $(echo "$mem_usage > 90" | bc -l) )); then
        log_message "HIGH MEMORY USAGE: ${mem_usage}%"
        ps aux --sort=-%mem | head -5 >> $LOG_FILE
    fi
}

check_disk() {
    df -h | awk 'NR>1 && $5+0 > 90 {print}' | while read line; do
        log_message "HIGH DISK USAGE: $line"
    done
}

check_services() {
    services=("nginx" "mysql" "ssh")
    for service in "${services[@]}"; do
        if ! systemctl is-active --quiet $service; then
            log_message "SERVICE DOWN: $service"
            systemctl start $service
        fi
    done
}

# Основной цикл мониторинга
while true; do
    check_cpu
    check_memory
    check_disk
    check_services
    sleep 60
done
```

Настройка через systemd timer
```bash
# /etc/systemd/system/system-monitor.service
[Unit]
Description=System Monitor
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/system_monitor.sh
Restart=always
User=root

[Install]
WantedBy=multi-user.target

# /etc/systemd/system/system-monitor.timer
[Unit]
Description=Run System Monitor every minute
Requires=system-monitor.service

[Timer]
OnCalendar=*:0/1
Persistent=true

[Install]
WantedBy=timers.target

# Активация
sudo systemctl enable system-monitor.timer
sudo systemctl start system-monitor.timer
```
---