#linux #network

#### 🔹 Сетевые соединения

```bash
# netstat - сетевые соединения (устаревшая)  
netstat -tulpn              # все соединения с процессами  
netstat -i                  # сетевые интерфейсы  
netstat -r                  # таблица маршрутизации  
  
# ss - современная замена netstat  
ss -tulpn                   # TCP и UDP соединения  
ss -t -a                    # все TCP соединения  
ss -u -a                    # все UDP соединения  
ss -l                       # только слушающие порты  
ss -p                       # с информацией о процессах  
ss -s                       # статистика сокетов  
  
# lsof - открытые файлы и сетевые соединения  
lsof -i                     # все сетевые соединения  
lsof -i :80                 # кто использует порт 80  
lsof -i TCP:22              # TCP соединения на порту 22  
lsof -u username            # файлы пользователя  
lsof -p PID                 # файлы процесса
```
---
#### 🔹 Сетевая статистика

```bash
# iftop - мониторинг сетевого трафика
sudo iftop                  # общий трафик
sudo iftop -i eth0          # конкретный интерфейс
sudo iftop -n               # не разрешать имена

# nethogs - трафик по процессам
sudo nethogs                # трафик по процессам
sudo nethogs eth0           # для конкретного интерфейса

# vnstat - статистика сетевого трафика
vnstat                      # общая статистика
vnstat -d                   # по дням
vnstat -m                   # по месяцам
vnstat -h                   # по часам
```
---
#### 🔹 Пакет iproute2

**iproute2** - это коллекция утилит для управления сетью в Linux, которая заменяет устаревшие инструменты из пакета net-tools. iproute2 предоставляет современный интерфейс для настройки сетевых интерфейсов, маршрутизации, туннелей, QoS и других сетевых функций.
```bash
# команды (iproute2)  
ip addr, ip link  
ip route  
ip neigh  
ss  
ip tunnel  
ip link set name  
ip maddr
```

##### 🔸 Команда ip - главная утилита
```bash
# Базовый синтаксис
ip [OPTIONS] OBJECT { COMMAND | help }

# OBJECTS (объекты):
link     # сетевые интерфейсы
addr     # IP адреса
route    # таблицы маршрутизации  
neigh    # ARP/neighbor таблицы
rule     # правила маршрутизации
tunnel   # туннели
maddr    # multicast адреса
mroute   # multicast маршруты
monitor  # мониторинг изменений
netns    # network namespaces
```

##### 🔸 Управление сетевыми интерфейсами (ip link)
```bash
# Просмотр всех интерфейсов
ip link show
ip link list
ip l  # краткая форма

# Просмотр конкретного интерфейса
ip link show eth0
ip link show dev eth0

# Включение/отключение интерфейса
sudo ip link set eth0 up
sudo ip link set eth0 down

# Изменение MAC адреса
sudo ip link set eth0 down
sudo ip link set eth0 address 02:01:02:03:04:05
sudo ip link set eth0 up

# Изменение имени интерфейса
sudo ip link set eth0 down
sudo ip link set eth0 name wan0
sudo ip link set wan0 up

# Изменение MTU
sudo ip link set eth0 mtu 1500

# Создание виртуальных интерфейсов
# VLAN интерфейс
sudo ip link add link eth0 name eth0.100 type vlan id 100
sudo ip link set eth0.100 up

# Bridge интерфейс
sudo ip link add name br0 type bridge
sudo ip link set br0 up

# Виртуальный интерфейс (dummy)
sudo ip link add dummy0 type dummy
sudo ip link set dummy0 up

# VETH пара (для контейнеров)
sudo ip link add veth0 type veth peer name veth1

# Удаление интерфейса
sudo ip link delete eth0.100
sudo ip link delete br0
```

```bash
# Просмотр всех адресов
ip addr show
ip addr list
ip a  # краткая форма

# Просмотр адресов конкретного интерфейса
ip addr show eth0
ip addr show dev eth0

# Добавление IP адреса
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip addr add 10.0.0.1/8 dev eth0

# Добавление адреса с меткой
sudo ip addr add 192.168.1.101/24 dev eth0 label eth0:1

# Добавление временного адреса
sudo ip addr add 192.168.1.102/24 dev eth0 valid_lft 3600 preferred_lft 1800

# Удаление IP адреса
sudo ip addr del 192.168.1.100/24 dev eth0

# Очистка всех адресов с интерфейса
sudo ip addr flush dev eth0

# Просмотр только IPv4 адресов
ip -4 addr show

# Просмотр только IPv6 адресов
ip -6 addr show

# Просмотр адресов в кратком формате
ip -br addr show
ip -brief addr show
```

###### Управление маршрутизацией (ip route)
```bash
# Просмотр таблицы маршрутизации
ip route show
ip route list
ip r  # краткая форма

# Просмотр маршрутов для конкретной сети
ip route show 192.168.1.0/24

# Добавление маршрута по умолчанию
sudo ip route add default via 192.168.1.1 dev eth0

# Добавление маршрута к сети
sudo ip route add 10.0.0.0/8 via 192.168.1.1 dev eth0

# Добавление маршрута через определенный интерфейс
sudo ip route add 172.16.0.0/16 dev eth1

# Маршрут с метрикой
sudo ip route add 10.0.0.0/8 via 192.168.1.1 metric 100

# Удаление маршрута
sudo ip route del 10.0.0.0/8 via 192.168.1.1
sudo ip route del default

# Замена маршрута
sudo ip route replace default via 192.168.1.1 dev eth0

# Просмотр маршрута к конкретному адресу
ip route get 8.8.8.8
ip route show to match 8.8.8.8

# Работа с альтернативными таблицами маршрутизации
# Просмотр всех таблиц
ip route show table all

# Создание маршрута в конкретной таблице
sudo ip route add 10.0.0.0/8 via 192.168.1.1 table 100

# Просмотр конкретной таблицы
ip route show table 100
```

###### Управление соседями (ip neigh)
```bash
# Просмотр ARP таблицы
ip neigh show
ip neigh list
ip n  # краткая форма

# Просмотр соседей конкретного интерфейса
ip neigh show dev eth0

# Добавление статической ARP записи
sudo ip neigh add 192.168.1.1 lladdr 00:11:22:33:44:55 dev eth0

# Изменение ARP записи
sudo ip neigh change 192.168.1.1 lladdr 00:11:22:33:44:56 dev eth0

# Удаление ARP записи
sudo ip neigh del 192.168.1.1 dev eth0

# Очистка ARP таблицы
sudo ip neigh flush all
sudo ip neigh flush dev eth0

# Принудительное обновление записи
sudo ip neigh replace 192.168.1.1 lladdr 00:11:22:33:44:57 dev eth0
```

###### Правила маршрутизации (ip rule)
```bash
# Просмотр правил маршрутизации
ip rule show
ip rule list

# Добавление правила по источнику
sudo ip rule add from 192.168.1.0/24 table 100

# Правило по назначению
sudo ip rule add to 10.0.0.0/8 table 200

# Правило по интерфейсу
sudo ip rule add iif eth0 table 300
sudo ip rule add oif eth1 table 400

# Правило с приоритетом
sudo ip rule add from 192.168.1.100 table 100 priority 500

# Удаление правила
sudo ip rule del from 192.168.1.0/24 table 100

# Правило с меткой (fwmark)
sudo ip rule add fwmark 1 table 100
```

##### 🔸 Команда ss
**ss** (Socket Statistics) - это современная утилита командной строки для исследования сокетов в Linux системах
```bash
# Преимущества ss:
✓ Значительно быстрее работает (особенно на системах с большим количеством соединений)
✓ Показывает больше информации о TCP соединениях  
✓ Поддерживает мощные фильтры для поиска конкретных соединений
✓ Активно развивается и поддерживается
✓ Встроена в современные дистрибутивы по умолчанию
✓ Лучше обрабатывает IPv6
✓ Показывает внутреннюю информацию TCP стека
```

###### ▫️ Базовый синтаксис
```bash
ss [опции] [FILTER]

# Основные опции:
-t, --tcp           # показать TCP сокеты
-u, --udp           # показать UDP сокеты  
-l, --listening     # показать слушающие сокеты
-a, --all           # показать все сокеты (слушающие и не слушающие)
-n, --numeric       # не разрешать имена сервисов
-p, --processes     # показать процессы, использующие сокеты
-r, --resolve       # разрешать имена хостов
-s, --summary       # показать статистику сокетов
-4, --ipv4          # показать только IPv4 сокеты
-6, --ipv6          # показать только IPv6 сокеты
-x, --unix          # показать Unix сокеты
-w, --raw           # показать RAW сокеты
-i, --info          # показать внутреннюю информацию TCP
-e, --extended      # показать расширенную информацию
-m, --memory        # показать использование памяти сокетами
-o, --options       # показать TCP опции
-H, --no-header     # не показывать заголовок
```

###### ▫️ Просмотр всех соединений
```bash
# Все сокеты
ss -a

# Все с числовыми портами
ss -an

# TCP и UDP сокеты  
ss -tu

# Все TCP/UDP сокеты с процессами
ss -tulpn

# Пример вывода:
# State    Recv-Q Send-Q Local Address:Port  Peer Address:Port Process
# LISTEN   0      128    0.0.0.0:22          0.0.0.0:*     users:(("sshd",pid=1234,fd=3))
# ESTAB    0      0      192.168.1.10:22     192.168.1.5:54321 users:(("sshd",pid=5678,fd=4))
```

###### ▫️ Слушающие порты
```bash
# Все слушающие сокеты
ss -l

# TCP слушающие порты
ss -tl
ss -tlp    # с процессами
ss -tlpn   # с процессами и числовыми портами

# UDP слушающие порты
ss -ul
ss -ulp    # с процессами

# Все слушающие TCP и UDP
ss -tulpn

# Только IPv4 слушающие порты
ss -4 -tlpn

# Только IPv6 слушающие порты
ss -6 -tlpn
```

###### ▫️Конкретные протоколы и состояния
```bash
# Только TCP соединения
ss -t

# Только TCP в состоянии ESTABLISHED
ss -t state established

# Только TCP в состоянии LISTEN
ss -t state listening

# TCP соединения в состоянии TIME-WAIT
ss -t state time-wait

# Все состояния TCP:
# established, syn-sent, syn-recv, fin-wait-1, fin-wait-2, 
# time-wait, closed, close-wait, last-ack, listening, closing
```

###### ▫️Расширенная информация о TCP
```bash
# Показать внутреннюю информацию TCP (RTT, cwnd, ssthresh, и т.д.)
ss -i

# Расширенная информация
ss -e

# TCP опции
ss -o

# Информация о памяти
ss -m

# Пример вывода с -i:
# ESTAB  0  0  192.168.1.10:22  192.168.1.5:54321
#        cubic wscale:7,7 rto:201 rtt:0.5/0.25 cwnd:10 ssthresh:7 send 231.4Mbps
```

###### ▫️ Unix сокеты
```bash
# Показать Unix domain сокеты
ss -x

# Unix сокеты с процессами
ss -xp

# Слушающие Unix сокеты
ss -xl

# Все Unix сокеты
ss -xa
```

###### ▫️ Статистика сокетов
```bash
# Общая статистика
ss -s

# Пример вывода:
# Total: 1843
# TCP:   12 (estab:5, closed:2, orphaned:0, timewait:2)
# Transport Total     IP        IPv6
# RAW       1         0         1        
# UDP       7         5         2        
# TCP       10        6         4        
# INET      18        11        7        
# FRAG      0         0         0
```

###### ▫️ Фильтрация по портам
```bash
# Соединения на порту 80
ss -tln sport = :80
ss -tln dport = :80

# Соединения на портах 80 или 443
ss -tln '( sport = :80 or sport = :443 )'

# Диапазон портов
ss sport ge :1024
ss sport le :1024  
ss dport 80-443

# Исключить определенный порт
ss sport != :22

# Примеры фильтрации:
ss -t dst 192.168.1.100          # соединения к конкретному IP
ss -t src 192.168.1.0/24         # соединения от подсети
ss -t dport = :80 or dport = :443 # HTTP и HTTPS
ss -t state established dport = :3306  # установленные соединения к MySQL
```

###### ▫️ Фильтрация по адресам
```bash
# Соединения к конкретному IP
ss dst 192.168.1.100

# Соединения от конкретного IP  
ss src 192.168.1.50

# Соединения в подсети
ss dst 192.168.1.0/24
ss src 10.0.0.0/8

# Локальные соединения
ss src 127.0.0.1

# Внешние соединения (не локальные)
ss '( dst != 127.0.0.1/8 and dst != 10.0.0.0/8 and dst != 192.168.0.0/16 )'
```

###### ▫️ Комплексные фильтры
```bash
# Установленные HTTP/HTTPS соединения
ss -o state established '( dport = :80 or dport = :443 )'

# MySQL соединения в различных состояниях
ss -t '( dport = :3306 or sport = :3306 )'

# SSH соединения с внутренней информацией TCP
ss -ti '( dport = :22 or sport = :22 )'

# Все соединения кроме локальных на портах выше 1024
ss -t '( sport gt :1024 and dst != 127.0.0.1 )'

# Соединения в TIME-WAIT состоянии на HTTP портах
ss -t state time-wait '( sport = :80 or dport = :80 )'
```

###### ▫️ Диагностика веб-сервера
```bash
#!/bin/bash
# web_server_diagnostics.sh

echo "=== Web Server Diagnostic Report ==="
echo "Generated at: $(date)"
echo

# Проверка слушающих веб-портов
echo "1. Web server listening ports:"
ss -tlpn '( sport = :80 or sport = :443 or sport = :8080 or sport = :8443 )'
echo

# Активные HTTP соединения
http_connections=$(ss -t state established dport = :80 | wc -l)
https_connections=$(ss -t state established dport = :443 | wc -l)

echo "2. Active connections:"
echo "HTTP (port 80): $http_connections connections"
echo "HTTPS (port 443): $https_connections connections"
echo

# Топ клиентских IP на HTTP/HTTPS
echo "3. Top client IPs (HTTP/HTTPS):"
ss -tn state established '( dport = :80 or dport = :443 )' | \
    awk 'NR>1 {print $4}' | cut -d: -f1 | sort | uniq -c | sort -nr | head -10
echo

# Анализ состояний соединений
echo "4. Connection states analysis:"
echo "HTTP (port 80):"
ss -t '( sport = :80 or dport = :80 )' | awk 'NR>1 {print $1}' | sort | uniq -c
echo
echo "HTTPS (port 443):"
ss -t '( sport = :443 or dport = :443 )' | awk 'NR>1 {print $1}' | sort | uniq -c
echo

# Проверка перегрузки (TIME-WAIT соединения)
time_wait_count=$(ss -t state time-wait '( sport = :80 or sport = :443 )' | wc -l)
echo "5. TIME-WAIT connections: $time_wait_count"
if [[ $time_wait_count -gt 100 ]]; then
    echo "⚠️  WARNING: High number of TIME-WAIT connections detected!"
fi
echo

# TCP информация для активных соединений
echo "6. TCP performance metrics (sample of active connections):"
ss -ti state established '( dport = :80 or dport = :443 )' | head -5
echo

# Проверка нагрузки на сервер
established_total=$(ss -t state established | wc -l)
echo "7. Total established connections: $established_total"

# Алерт при высокой нагрузке
if [[ $established_total -gt 200 ]]; then
    echo "🚨 ALERT: High connection count detected!"
    echo "Consider checking server resources and connection limits."
fi
```

###### ▫️Мониторинг базы данных
```bash
#!/bin/bash
# database_monitoring.sh

echo "=== Database Connection Monitoring ==="
echo "Timestamp: $(date)"
echo

# Функция мониторинга конкретной БД
monitor_database() {
    local port=$1
    local name=$2
    local max_connections=${3:-100}
    
    echo "=== $name Database (port $port) ==="
    
    # Проверка, что БД слушает
    listening=$(ss -tlpn sport = :$port | grep -v State)
    if [[ -z "$listening" ]]; then
        echo "❌ $name is NOT listening on port $port"
        return 1
    fi
    
    echo "✅ $name is listening:"
    echo "$listening"
    echo
    
    # Активные соединения
    connections=$(ss -tn state established dport = :$port | wc -l)
    echo "Active connections: $connections"
    
    # Проверка лимита соединений
    if [[ $connections -gt $max_connections ]]; then
        echo "⚠️  WARNING: Connection count ($connections) exceeds recommended limit ($max_connections)"
    fi
    echo
    
    # Клиентские IP
    echo "Client IPs:"
    ss -tn state established dport = :$port | awk 'NR>1 {print $4}' | \
        cut -d: -f1 | sort | uniq -c | sort -nr | head -5
    echo
    
    # Состояния соединений
    echo "Connection states:"
    ss -t dport = :$port | awk 'NR>1 {print $1}' | sort | uniq -c
    echo
    
    # TCP производительность для образца соединений
    echo "TCP performance metrics (sample):"
    ss -ti state established dport = :$port | head -3
    echo
}

# Мониторинг популярных БД
monitor_database 3306 "MySQL/MariaDB" 150
monitor_database 5432 "PostgreSQL" 100  
monitor_database 27017 "MongoDB" 200
monitor_database 6379 "Redis" 300

# Общая статистика соединений БД
echo "=== Database Connections Summary ==="
db_ports=(3306 5432 27017 6379)
total_db_connections=0

for port in "${db_ports[@]}"; do
    count=$(ss -tn state established dport = :$port | wc -l)
    total_db_connections=$((total_db_connections + count))
    if [[ $count -gt 0 ]]; then
        case $port in
            3306) db_name="MySQL" ;;
            3307) db_name="PostgreSQL" ;;
            3308) db_name="MongoDB" ;;
            3309) db_name="Redis" ;;
        esac
        echo "$db_name (port $port): $count connections"
    fi
done

echo "Total database connections: $total_db_connections"

# Проверка на connection leaks
echo
echo "=== Connection Leak Detection ==="
long_connections=$(ss -to state established | awk '$2 > 3600 {count++} END {print count+0}')
echo "Long-running connections (>1 hour): $long_connections"

if [[ $long_connections -gt 10 ]]; then
    echo "⚠️  Potential connection leak detected!"
    echo "Long-running connections:"
    ss -tpo state established | awk '$2 > 3600 {print}'
fi
```

###### ▫️ Анализ производительности сети
```bash
#!/bin/bash
# network_performance_analysis.sh

echo "=== Network Performance Analysis ==="
echo "Analysis time: $(date)"
echo

# Общая статистика сокетов
echo "1. Socket Statistics:"
ss -s
echo

# Анализ TCP производительности
echo "2. TCP Performance Analysis:"
echo "Active TCP connections with performance metrics:"
ss -ti state established | head -10
echo

# Поиск медленных соединений
echo "3. Slow Connections Detection:"
echo "Connections with high RTT (>100ms):"
ss -ti state established | awk '/rtt:/ {
    match($0, /rtt:([0-9.]+)/, arr)
    if (arr[1] > 100) print
}'
echo

# Анализ пропускной способности
echo "4. Bandwidth Analysis:"
echo "Connections with congestion window info:"
ss -ti state established | awk '/cwnd:/ {print}' | head -5
echo

# Проверка buffer overruns
echo "5. Buffer Analysis:"
echo "Connections with non-zero send/receive queues:"
ss -tn | awk 'NR>1 && ($2 > 0 || $3 > 0) {print "Send-Q:", $2, "Recv-Q:", $3, "Connection:", $4, "->", $5}' | head -10
echo

# Анализ перегрузки по портам
echo "6. Port Congestion Analysis:"
high_traffic_ports=$(ss -tn state established | awk 'NR>1 {print $4}' | cut -d: -f2 | sort | uniq -c | sort -nr | head -5)
echo "Top ports by connection count:"
echo "$high_traffic_ports"
echo

# Memory usage by sockets
echo "7. Socket Memory Usage:"
ss -m | grep -A1 -B1 "mem:" | head -10
echo

# Проверка на потерянные пакеты (через retrans info)
echo "8. Retransmission Analysis:"
retrans_info=$(ss -ti | grep -E "(retrans|lost)" | head -5)
if [[ -n "$retrans_info" ]]; then
    echo "Connections with retransmissions detected:"
    echo "$retrans_info"
else
    echo "✅ No significant retransmissions detected"
fi
echo

# Анализ состояний TCP для выявления проблем
echo "9. TCP State Analysis:"
tcp_states=$(ss -t | awk 'NR>1 {print $1}' | sort | uniq -c | sort -nr)
echo "$tcp_states"
echo

# Предупреждения на основе анализа
echo "10. Performance Warnings:"
time_wait_count=$(ss -t state time-wait | wc -l)
established_count=$(ss -t state established | wc -l)
close_wait_count=$(ss -t state close-wait | wc -l)

if [[ $time_wait_count -gt 1000 ]]; then
    echo "⚠️  High TIME-WAIT count: $time_wait_count (consider tuning tcp_tw_reuse)"
fi

if [[ $established_count -gt 500 ]]; then
    echo "⚠️  High ESTABLISHED count: $established_count (monitor server load)"
fi

if [[ $close_wait_count -gt 50 ]]; then
    echo "⚠️  High CLOSE-WAIT count: $close_wait_count (possible application issue)"
fi

if [[ $time_wait_count -lt 10 && $established_count -lt 10 && $close_wait_count -lt 5 ]]; then
    echo "✅ TCP state distribution looks healthy"
fi
```

###### ▫️ Безопасность и мониторинг соединений
```bash
#!/bin/bash  
# security_monitoring.sh

echo "=== Network Security Monitoring ==="
echo "Scan timestamp: $(date)"
echo

# Функция для анализа подозрительных соединений
analyze_suspicious_connections() {
    echo "1. Suspicious Connection Analysis:"
    
    # Соединения от внешних IP (не RFC1918)
    echo "External connections (non-private IPs):"
    ss -tn state established | awk 'NR>1 {print $5}' | cut -d: -f1 | \
        grep -vE '^(127\.|10\.|172\.(1[6-9]|2[0-9]|3[01])\.|192\.168\.)' | \
        sort | uniq -c | sort -nr | head -10
    echo
    
    # IP адреса с большим количеством соединений
    echo "IPs with high connection counts (potential DDoS/scan):"
    ss -tn | awk 'NR>1 {print $5}' | cut -d: -f1 | sort | uniq -c | \
        awk '$1 > 10 {print $1, $2}' | sort -nr
    echo
}

# Проверка открытых портов и сервисов
check_open_ports() {
    echo "2. Open Ports Security Check:"
    
    # Потенциально опасные порты
    dangerous_ports=(21 23 25 53 110 143 993 995 1433 3389 5432 3306)
    
    echo "Checking for potentially risky services:"
    for port in "${dangerous_ports[@]}"; do
        service_info=$(ss -tlpn sport = :$port 2>/dev/null | grep -v State)
        if [[ -n "$service_info" ]]; then
            case $port in
                21) risk="HIGH - FTP (unencrypted)" ;;
                22) risk="CRITICAL - Telnet (unencrypted)" ;;
                23) risk="MEDIUM - SMTP" ;;
                24) risk="LOW - DNS" ;;
                25) risk="MEDIUM - POP3 (consider POP3S)" ;;
                26) risk="MEDIUM - IMAP (consider IMAPS)" ;;
                27) risk="LOW - IMAPS (encrypted)" ;;
                28) risk="LOW - POP3S (encrypted)" ;;
                29) risk="HIGH - MSSQL" ;;
                30) risk="HIGH - RDP" ;;
                31) risk="MEDIUM - PostgreSQL" ;;
                32) risk="MEDIUM - MySQL" ;;
            esac
            echo "⚠️  Port $port is open - Risk: $risk"
            echo "   $service_info"
        fi
    done
    echo
}

# Анализ необычных портов
check_unusual_ports() {
    echo "3. Unusual Port Analysis:"
    
    # Высокие порты (выше 10000) которые слушают
    echo "High ports listening (>10000):"
    ss -tlpn | awk 'NR>1 {print $4}' | cut -d: -f2 | awk '$1 > 10000' | sort -n | uniq
    echo
    
    # Проверка на backdoor порты
    backdoor_ports=(4444 5555 6666 7777 8888 9999 31337 12345 54321)
    echo "Checking common backdoor ports:"
    backdoor_found=false
    for port in "${backdoor_ports[@]}"; do
        if ss -tlpn sport = :$port 2>/dev/null | grep -q .; then
            echo "🚨 ALERT: Potential backdoor port $port is listening!"
            ss -tlpn sport = :$port
            backdoor_found=true
        fi
    done
    
    if [[ $backdoor_found == false ]]; then
        echo "✅ No common backdoor ports detected"
    fi
    echo
}

# Мониторинг SSH соединений
monitor_ssh() {
    echo "4. SSH Security Monitoring:"
    
    ssh_connections=$(ss -tn state established dport = :22 | wc -l)
    echo "Active SSH connections: $ssh_connections"
    
    if [[ $ssh_connections -gt 0 ]]; then
        echo "SSH client IPs:"
        ss -tn state established dport = :22 | awk 'NR>1 {print $4}' | \
            cut -d: -f1 | sort | uniq -c | sort -nr
        
        # Проверка на множественные SSH соединения (подозрительно)
        multiple_ssh=$(ss -tn state established dport = :22 | awk 'NR>1 {print $4}' | \
            cut -d: -f1 | sort | uniq -c | awk '$1 > 3 {print $2, $1}')
        
        if [[ -n "$multiple_ssh" ]]; then
            echo "⚠️  IPs with multiple SSH connections:"
            echo "$multiple_ssh"
        fi
    fi
    echo
}

# Проверка на port scanning активность
detect_port_scanning() {
    echo "5. Port Scanning Detection:"
    
    # Соединения в состоянии SYN-RECV (может указывать на сканирование)
    syn_recv_count=$(ss -t state syn-recv | wc -l)
    echo "SYN-RECV connections: $syn_recv_count"
    
    if [[ $syn_recv_count -gt 20 ]]; then
        echo "⚠️  High SYN-RECV count detected - possible port scan"
        echo "Top source IPs in SYN-RECV state:"
        ss -tn state syn-recv | awk 'NR>1 {print $5}' | cut -d: -f1 | \
            sort | uniq -c | sort -nr | head -5
    fi
    echo
}

# Анализ сетевого трафика по времени
analyze_traffic_patterns() {
    echo "6. Traffic Pattern Analysis:"
    
    # Подсчет соединений по часам (требует исторических данных)
    current_hour=$(date +%H)
    total_connections=$(ss -tn | wc -l)
    
    echo "Current hour: ${current_hour}:00"
    echo "Total current connections: $total_connections"
    
    # Простая проверка на необычную активность (в ночное время)
    if [[ $current_hour -ge 2 && $current_hour -le 6 ]] && [[ $total_connections -gt 100 ]]; then
        echo "⚠️  Unusual high activity during night hours"
    fi
    echo
}

# Генерация алертов
generate_alerts() {
    echo "7. Security Alerts Summary:"
    
    alerts=()
    
    # Проверка критических портов
    if ss -tlpn sport = :23 | grep -q .; then
        alerts+=("CRITICAL: Telnet service detected")
    fi
    
    # Проверка внешних соединений к чувствительным портам
    sensitive_ports=(22 3306 5432 1433)
    for port in "${sensitive_ports[@]}"; do
        external_conns=$(ss -tn state established sport = :$port | \
            awk 'NR>1 {print $5}' | cut -d: -f1 | \
            grep -vE '^(127\.|10\.|172\.(1[6-9]|2[0-9]|3[01])\.|192\.168\.)' | wc -l)
        
        if [[ $external_conns -gt 0 ]]; then
            alerts+=("WARNING: External connections to sensitive port $port")
        fi
    done
    
    # Высокое количество TIME-WAIT (возможная DDoS атака)
    time_wait_count=$(ss -t state time-wait | wc -l)
    if [[ $time_wait_count -gt 500 ]]; then
        alerts+=("WARNING: High TIME-WAIT count ($time_wait_count) - possible DDoS")
    fi
    
    # Вывод алертов
    if [[ ${#alerts[@]} -eq 0 ]]; then
        echo "✅ No security alerts detected"
    else
        for alert in "${alerts[@]}"; do
            echo "🚨 $alert"
        done
    fi
}

# Основные функции
analyze_suspicious_connections
check_open_ports  
check_unusual_ports
monitor_ssh
detect_port_scanning
analyze_traffic_patterns
generate_alerts

echo "=== Security monitoring completed ==="
```

###### ▫️ Автоматизированный мониторинг производительности
```bash
#!/bin/bash
# performance_monitoring.sh

LOGFILE="/var/log/network_performance.log"
ALERT_EMAIL="admin@company.com"
THRESHOLD_CONNECTIONS=200
THRESHOLD_TIME_WAIT=100

# Функция логирования
log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S'): $1" | tee -a "$LOGFILE"
}

# Сбор метрик производительности
collect_performance_metrics() {
    local metrics=()
    
    # Основные метрики
    local total_connections=$(ss -t | wc -l)
    local established=$(ss -t state established | wc -l)
    local time_wait=$(ss -t state time-wait | wc -l)
    local close_wait=$(ss -t state close-wait | wc -l)
    local listen=$(ss -t state listening | wc -l)
    
    # TCP метрики из ss -s
    local tcp_stats=$(ss -s | grep TCP)
    
    metrics+=("total_connections:$total_connections")
    metrics+=("established:$established")
    metrics+=("time_wait:$time_wait")
    metrics+=("close_wait:$close_wait")
    metrics+=("listening:$listen")
    
    # Память, используемая сокетами
    local socket_memory=$(ss -m | grep -c "mem:")
    metrics+=("socket_memory_usage:$socket_memory")
    
    # Вывод метрик
    log_message "METRICS: $(IFS=','; echo "${metrics[*]}")"
    
    # Проверка на превышение порогов
    if [[ $established -gt $THRESHOLD_CONNECTIONS ]]; then
        log_message "ALERT: High connection count: $established > $THRESHOLD_CONNECTIONS"
        send_alert "High Connection Count" "Current established connections: $established"
    fi
    
    if [[ $time_wait -gt $THRESHOLD_TIME_WAIT ]]; then
        log_message "ALERT: High TIME-WAIT count: $time_wait > $THRESHOLD_TIME_WAIT"
        send_alert "High TIME-WAIT Count" "Current TIME-WAIT connections: $time_wait"
    fi
}

# Анализ производительности TCP
analyze_tcp_performance() {
    log_message "Starting TCP performance analysis"
    
    # Соединения с высоким RTT
    high_rtt_connections=$(ss -ti state established | awk '
        /rtt:/ {
            match($0, /rtt:([0-9.]+)/, arr)
            if (arr[1] > 200) {
                match($0, /([0-9.]+\.[0-9.]+\.[0-9.]+\.[0-9.]+):([0-9]+).*([0-9.]+\.[0-9.]+\.[0-9.]+\.[0-9.]+):([0-9]+)/, conn)
                print conn[1]":"conn[2]" -> "conn[3]":"conn[4]" RTT:"arr[1]"ms"
            }
        }
    ')
    
    if [[ -n "$high_rtt_connections" ]]; then
        log_message "WARNING: High RTT connections detected:"
        echo "$high_rtt_connections" | while read line; do
            log_message "  $line"
        done
    fi
    
    # Соединения с ретрансмиссиями
    retrans_connections=$(ss -ti | grep -c "retrans")
    if [[ $retrans_connections -gt 0 ]]; then
        log_message "WARNING: $retrans_connections connections with retransmissions"
    fi
    
    # Анализ congestion window
    ss -ti state established | awk '/cwnd:/ {
        match($0, /cwnd:([0-9]+)/, arr)
        if (arr[1] < 10) count++
    } END {
        if (count > 0) print "WARNING: "count" connections with small congestion window"
    }' | while read warning; do
        if [[ -n "$warning" ]]; then
            log_message "$warning"
        fi
    done
}

# Мониторинг конкретных сервисов
monitor_services() {
    declare -A services=(
        [80]="HTTP"
        [443]="HTTPS" 
        [22]="SSH"
        [3306]="MySQL"
        [5432]="PostgreSQL"
        [6379]="Redis"
        [25]="SMTP"
    )
    
    log_message "Service monitoring:"
    
    for port in "${!services[@]}"; do
        local service_name="${services[$port]}"
        
        # Проверка слушающего сокета
        if ! ss -tlpn sport = :$port | grep -q .; then
            log_message "WARNING: $service_name service not listening on port $port"
            continue
        fi
        
        # Подсчет соединений
        local connections=$(ss -tn state established dport = :$port | wc -l)
        log_message "$service_name (port $port): $connections active connections"
        
        # Специфичные проверки для разных сервисов
        case $port in
            80|443)
                # Веб-серверы: проверка TIME-WAIT
                local tw_count=$(ss -t state time-wait sport = :$port | wc -l)
                if [[ $tw_count -gt 50 ]]; then
                    log_message "WARNING: High TIME-WAIT count for $service_name: $tw_count"
                fi
                ;;
            3306|5432)
                # Базы данных: проверка долгих соединений
                local long_conns=$(ss -to state established dport = :$port | awk '$2 > 3600 {count++} END {print count+0}')
                if [[ $long_conns -gt 0 ]]; then
                    log_message "INFO: $service_name has $long_conns long-running connections"
                fi
                ;;
        esac
    done
}

# Отправка алертов
send_alert() {
    local subject="$1"
    local message="$2"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    # Отправка email (если настроен mail)
    if command -v mail >/dev/null 2>&1; then
        echo "Alert Time: $timestamp
        
$message

Server: $(hostname)
Current Metrics:
$(ss -s)" | mail -s "Network Alert: $subject" "$ALERT_EMAIL"
    fi
    
    # Можно добавить отправку в Slack, Telegram и т.д.
}

# Генерация отчета
generate_report() {
    local report_file="/tmp/network_report_$(date +%Y%m%d_%H%M%S).txt"
    
    {
        echo "=== Network Performance Report ==="
        echo "Generated: $(date)"
        echo "Server: $(hostname)"
        echo
        
        echo "Socket Statistics:"
        ss -s
        echo
        
        echo "Top 10 ports by connection count:"
        ss -tn | awk 'NR>1 {print $4}' | cut -d: -f2 | sort | uniq -c | sort -nr | head -10
        echo
        
        echo "Connection states distribution:"
        ss -t | awk 'NR>1 {print $1}' | sort | uniq -c
        echo
        
        echo "Active services:"
        ss -tlpn | awk 'NR>1 {print $4, $6}' | sort
        echo
        
        echo "Recent log entries:"
        tail -20 "$LOGFILE"
        
    } > "$report_file"
    
    log_message "Report generated: $report_file"
    
    # Отправка отчета по email
    if command -v mail >/dev/null 2>&1; then
        mail -s "Daily Network Report - $(hostname)" -a "$report_file" "$ALERT_EMAIL" < /dev/null
    fi
}

# Главная функция мониторинга
main_monitoring() {
    log_message "Starting network performance monitoring"
    
    collect_performance_metrics
    analyze_tcp_performance  
    monitor_services
    
    log_message "Network monitoring completed"
}

# Обработка аргументов командной строки
case "$1" in
    "daemon")
        # Режим демона - запуск каждую минуту
        log_message "Starting monitoring daemon"
        while true; do
            main_monitoring
            sleep 60
        done
        ;;
    "report")
        # Генерация отчета
        generate_report
        ;;
    "once")
        # Однократный запуск
        main_monitoring
        ;;
    *)
        echo "Usage: $0 {daemon|report|once}"
        echo "  daemon - Run continuous monitoring"
        echo "  report - Generate performance report"  
        echo "  once   - Run monitoring once"
        exit 1
        ;;
esac
```