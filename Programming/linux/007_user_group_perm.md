#linux
#### 🔹 Основы системы пользователей в Linux
##### 🔸 Концепция многопользовательской системы
Linux изначально проектировался как многопользовательская система, где несколько пользователей могут одновременно работать с одной машиной. Каждый пользователь имеет:
- Уникальный идентификатор (UID)
- Домашний каталог
- Набор разрешений и ограничений
- Принадлежность к группам

##### 🔸 Типы пользователей
```bash
# 1. Суперпользователь (root)
# UID = 0, полные права в системе
whoami  # root

# 2. Системные пользователи (system users)
# UID 1-999, для служб и демонов
# Примеры: www-data, mysql, nginx, daemon

# 3. Обычные пользователи (regular users)
# UID >= 1000, интерактивные пользователи
```

##### 🔸 Файлы конфигурации пользователей
```bash
# /etc/passwd - основная информация о пользователях
cat /etc/passwd
# Формат: username:password:UID:GID:GECOS:home:shell
# root:x:0:0:root:/root:/bin/bash
# user:x:1000:1000:User Name,,,:/home/user:/bin/bash

# /etc/shadow - зашифрованные пароли (только root может читать)
sudo cat /etc/shadow
# Формат: username:encrypted_password:last_change:min:max:warn:inactive:expire

# /etc/group - информация о группах
cat /etc/group
# Формат: groupname:password:GID:members
# sudo:x:27:user1,user2

# /etc/gshadow - зашифрованные групповые пароли
sudo cat /etc/gshadow
```

##### 🔸 Sudo пользлователь
```bash
sudo su nameUser - переходит под пользователем nameUser
sudo -i - переходит в режим пользователя (выйти ctrl + D)
```
---
#### 🔹 Создание и управление пользователями

##### 🔸 Создание пользователей
```bash
# Базовое создание пользователя
sudo useradd username

# Создание с домашним каталогом
sudo useradd -m username

# Полное создание пользователя с параметрами
sudo useradd -m -s /bin/bash -c "John Doe" -G users,sudo john

# Параметры useradd:
# -m, --create-home    создать домашний каталог
# -s, --shell         указать shell
# -c, --comment       комментарий (полное имя)
# -G, --groups        дополнительные группы
# -g, --gid           основная группа
# -u, --uid           конкретный UID
# -d, --home-dir      домашний каталог
# -e, --expiredate    дата истечения аккаунта
# -f, --inactive      дни после истечения пароля до блокировки

# Примеры создания различных типов пользователей:

# Обычный пользователь
sudo useradd -m -s /bin/bash -c "Alice Smith" alice
sudo passwd alice

# Пользователь-администратор
sudo useradd -m -s /bin/bash -c "Admin User" -G sudo admin
sudo passwd admin

# Системный пользователь для службы
sudo useradd -r -s /bin/false -c "Web Service User" webservice

# Пользователь с ограниченным сроком действия
sudo useradd -m -s /bin/bash -e 2024-12-31 tempuser

# Пользователь с конкретным UID
sudo useradd -m -u 2000 -s /bin/bash specificuser
```

##### 🔸 Модификация пользователей
```bash
# Изменение основных параметров
sudo usermod -c "New Full Name" username    # изменить комментарий
sudo usermod -s /bin/zsh username          # изменить shell
sudo usermod -d /new/home username         # изменить домашний каталог
sudo usermod -m -d /new/home username      # с перемещением файлов

# Управление группами
sudo usermod -G group1,group2 username     # заменить все дополнительные группы
sudo usermod -aG newgroup username         # добавить к существующим группам
sudo usermod -g newprimarygroup username   # изменить основную группу

# Блокировка и разблокировка
sudo usermod -L username                   # заблокировать аккаунт
sudo usermod -U username                   # разблокировать аккаунт
sudo passwd -l username                    # заблокировать только пароль
sudo passwd -u username                    # разблокировать пароль

# Установка срока действия
sudo usermod -e 2024-06-30 username        # установить дату истечения
sudo usermod -e "" username                # убрать дату истечения

# Изменение UID (осторожно!)
sudo usermod -u 1500 username
# После изменения UID нужно изменить права на файлы:
sudo find /home/username -user 1000 -exec chown 1500 {} \;
```

##### 🔸 Удаление пользователей
```bash
# Удаление только аккаунта (файлы остаются)
sudo userdel username

# Удаление аккаунта с домашним каталогом и почтовым спулом
sudo userdel -r username

# Принудительное удаление (даже если пользователь залогинен)
sudo userdel -f username

# Безопасное удаление с архивированием
sudo tar -czf /backup/user_backup_$(date +%Y%m%d).tar.gz /home/username
sudo userdel -r username
```
---
#### 🔹 Создание и управление группами

##### 🔸 Основы групп
```bash
# Типы групп:
# Основная группа (primary group) - одна на пользователя, используется по умолчанию
# Дополнительные группы (supplementary groups) - до 15 дополнительных групп

# Системные группы (GID < 1000):
sudo       # администраторы с правами sudo
www-data   # веб-сервер
mysql      # база данных MySQL
docker     # пользователи Docker

# Пользовательские группы (GID >= 1000):
users      # обычные пользователи
developers # разработчики
designers  # дизайнеры
```

##### 🔸 Создание и управление группами
```bash
# Создание группы
sudo groupadd groupname
sudo groupadd -g 2000 groupname        # с конкретным GID
sudo groupadd -r systemgroup           # системная группа

# Примеры создания групп для разных целей:
sudo groupadd developers               # группа разработчиков
sudo groupadd designers                # группа дизайнеров
sudo groupadd managers                 # группа менеджеров
sudo groupadd backup-users             # пользователи с правами резервного копирования

# Изменение группы
sudo groupmod -n newname oldname       # переименовать группу
sudo groupmod -g 3000 groupname       # изменить GID

# Удаление группы
sudo groupdel groupname
```

##### 🔸 Управление членством в группах
```bash
# Добавление пользователей в группы
sudo usermod -aG groupname username           # добавить в дополнительную группу
sudo usermod -G group1,group2 username       # заменить все дополнительные группы
sudo gpasswd -a username groupname           # альтернативный способ добавления

# Удаление из группы
sudo gpasswd -d username groupname           # удалить из группы
sudo deluser username groupname              # Ubuntu/Debian способ

# Назначение администратора группы
sudo gpasswd -A admin_user groupname         # сделать администратором группы

# Установка пароля группы (редко используется)
sudo gpasswd groupname
```

##### 🔸 Просмотр информации о пользователях и группах
```bash
# Информация о текущем пользователе
whoami                         # имя пользователя
id                             # UID, GID и все группы
id username                    # информация о конкретном пользователе
groups                         # группы текущего пользователя
groups username                # группы конкретного пользователя

# Подробная информация
finger username                # детальная информация (если установлен)
getent passwd username         # запись из /etc/passwd
getent group groupname         # информация о группе

# Список всех пользователей и групп
getent passwd                  # все пользователи
getent group                   # все группы
cut -d: -f1 /etc/passwd        # только имена пользователей
cut -d: -f1 /etc/group         # только имена групп

# Активные пользователи в системе
who                          # кто залогинен
w                            # подробная информация о сессиях
last                         # история входов
lastlog                      # последний вход каждого пользователя
```
---
#### 🔹 Реальные кейсы использования

##### 🔸 Настройка среды разработки
```bash
# Создание групп для разработки
sudo groupadd developers
sudo groupadd designers
sudo groupadd devops
sudo groupadd qa

# Создание пользователей-разработчиков
sudo useradd -m -s /bin/bash -c "Alice Developer" -G developers alice
sudo useradd -m -s /bin/bash -c "Bob Backend" -G developers bob
sudo useradd -m -s /bin/bash -c "Carol Designer" -G designers carol
sudo useradd -m -s /bin/bash -c "David DevOps" -G devops,sudo david

# Установка паролей
echo "alice:password123" | sudo chpasswd
echo "bob:password123" | sudo chpasswd
echo "carol:password123" | sudo chpasswd
echo "david:password123" | sudo chpasswd

# Создание общих каталогов проектов
sudo mkdir -p /opt/projects/{frontend,backend,design}
sudo mkdir -p /opt/shared/{docs,tools,templates}

# Настройка прав доступа
sudo chgrp -R developers /opt/projects/frontend /opt/projects/backend
sudo chgrp -R designers /opt/projects/design
sudo chgrp -R users /opt/shared

sudo chmod -R 775 /opt/projects
sudo chmod -R 755 /opt/shared

# Установка SGID для наследования группы
sudo chmod g+s /opt/projects/frontend
sudo chmod g+s /opt/projects/backend  
sudo chmod g+s /opt/projects/design

# Настройка umask для правильных прав по умолчанию
echo "umask 002" | sudo tee -a /home/alice/.bashrc
echo "umask 002" | sudo tee -a /home/bob/.bashrc
echo "umask 002" | sudo tee -a /home/carol/.bashrc
```

##### 🔸 Веб-сервер с несколькими сайтами
```bash
# Создание групп для разных сайтов
sudo groupadd web-site1
sudo groupadd web-site2
sudo groupadd web-admins

# Создание пользователей-администраторов сайтов
sudo useradd -m -s /bin/bash -c "Site1 Admin" -G web-site1,web-admins site1admin
sudo useradd -m -s /bin/bash -c "Site2 Admin" -G web-site2,web-admins site2admin

# Добавление существующих пользователей в группы
sudo usermod -aG web-site1 alice
sudo usermod -aG web-site2 bob

# Создание структуры каталогов
sudo mkdir -p /var/www/{site1,site2}/{public,logs,backups}

# Настройка владельцев и прав
sudo chown -R www-data:web-site1 /var/www/site1
sudo chown -R www-data:web-site2 /var/www/site2

# Права на каталоги
sudo chmod -R 755 /var/www/site1/public
sudo chmod -R 755 /var/www/site2/public
sudo chmod -R 770 /var/www/site1/logs
sudo chmod -R 770 /var/www/site2/logs
sudo chmod -R 750 /var/www/site1/backups
sudo chmod -R 750 /var/www/site2/backups

# SGID для наследования группы
sudo chmod g+s /var/www/site1
sudo chmod g+s /var/www/site2

# Настройка логротации для каждого сайта
sudo cat << 'EOF' > /etc/logrotate.d/site1
/var/www/site1/logs/*.log {
    weekly
    missingok
    rotate 52
    compress
    delaycompress
    create 640 www-data web-site1
}
EOF
```

##### 🔸 Файловый сервер с отделами
```bash
# Создание групп по отделам
sudo groupadd accounting
sudo groupadd marketing  
sudo groupadd hr
sudo groupadd management
sudo groupadd it

# Создание пользователей
declare -A users=(
    ["john"]="accounting"
    ["mary"]="marketing" 
    ["sarah"]="hr"
    ["mike"]="management"
    ["admin"]="it,sudo"
)

for user in "${!users[@]}"; do
    sudo useradd -m -s /bin/bash -G "${users[$user]}" "$user"
    echo "$user:TempPass123!" | sudo chpasswd
    # Принуждение к смене пароля при первом входе
    sudo chage -d 0 "$user"
done

# Создание общих каталогов
sudo mkdir -p /shared/{accounting,marketing,hr,management,common}

# Настройка прав доступа по отделам
departments=("accounting" "marketing" "hr" "management")
for dept in "${departments[@]}"; do
    sudo chgrp "$dept" "/shared/$dept"
    sudo chmod 770 "/shared/$dept"
    sudo chmod g+s "/shared/$dept"  # SGID для наследования группы
done

# Общий каталог доступен всем
sudo chgrp users /shared/common
sudo chmod 775 /shared/common
sudo chmod g+s /shared/common

# Создание политик квот диска (если поддерживается)
# sudo setquota -u john 1000000 1200000 1000 1200 /shared
```

##### 🔸 Сервер баз данных
```bash
# Создание групп для работы с БД
sudo groupadd dba              # администраторы БД
sudo groupadd db-developers    # разработчики БД
sudo groupadd db-users         # пользователи БД
sudo groupadd backup-operators # операторы резервного копирования

# Создание пользователей
sudo useradd -m -s /bin/bash -c "DB Admin" -G dba,sudo dbadmin
sudo useradd -m -s /bin/bash -c "DB Developer" -G db-developers,dba alice
sudo useradd -r -s /bin/false -c "Backup Service" -G backup-operators backupuser

# MySQL/PostgreSQL настройки
sudo usermod -aG mysql dbadmin     # для MySQL
sudo usermod -aG postgres dbadmin  # для PostgreSQL

# Создание каталогов для скриптов и бэкапов
sudo mkdir -p /opt/database/{scripts,backups,logs}
sudo mkdir -p /opt/database/scripts/{maintenance,monitoring,migrations}

# Настройка прав
sudo chgrp -R dba /opt/database/scripts
sudo chgrp -R backup-operators /opt/database/backups
sudo chgrp -R dba /opt/database/logs

sudo chmod -R 750 /opt/database/scripts
sudo chmod -R 770 /opt/database/backups
sudo chmod -R 750 /opt/database/logs

# Создание sudoers правил для операторов бэкапа
sudo cat << 'EOF' > /etc/sudoers.d/backup-operators
%backup-operators ALL=(ALL) NOPASSWD: /usr/bin/mysqldump, /usr/bin/pg_dump, /bin/gzip, /bin/tar
EOF

# Создание скрипта бэкапа
sudo cat << 'EOF' > /opt/database/scripts/maintenance/backup.sh
#!/bin/bash
BACKUP_DIR="/opt/database/backups"
DATE=$(date +%Y%m%d_%H%M%S)

# MySQL backup
mysqldump --all-databases | gzip > "$BACKUP_DIR/mysql_backup_$DATE.sql.gz"

# PostgreSQL backup  
pg_dumpall | gzip > "$BACKUP_DIR/postgres_backup_$DATE.sql.gz"

# Set permissions
chown backupuser:backup-operators "$BACKUP_DIR"/*backup_$DATE.sql.gz
chmod 640 "$BACKUP_DIR"/*backup_$DATE.sql.gz
EOF

sudo chmod +x /opt/database/scripts/maintenance/backup.sh
```

##### 🔸 Контейнеризация и DevOps
```bash
# Создание групп для DevOps
sudo groupadd docker-users
sudo groupadd k8s-admins  
sudo groupadd ci-cd
sudo groupadd monitoring

# Пользователи DevOps
sudo useradd -m -s /bin/bash -c "DevOps Engineer" -G docker-users,sudo devops1
sudo useradd -m -s /bin/bash -c "SRE" -G docker-users,k8s-admins,monitoring sre1
sudo useradd -r -s /bin/bash -c "CI/CD Service" -G docker-users,ci-cd jenkins

# Добавление в группу Docker
sudo usermod -aG docker devops1
sudo usermod -aG docker sre1  
sudo usermod -aG docker jenkins

# Настройка прав для мониторинга
sudo mkdir -p /opt/monitoring/{config,data,logs}
sudo chgrp -R monitoring /opt/monitoring
sudo chmod -R 775 /opt/monitoring
sudo chmod g+s /opt/monitoring

# Создание пользователя для сервисов
sudo useradd -r -s /bin/false -c "Prometheus Service" -G monitoring prometheus
sudo useradd -r -s /bin/false -c "Grafana Service" -G monitoring grafana

# Настройка sudo правил для управления сервисами
sudo cat << 'EOF' > /etc/sudoers.d/devops
%docker-users ALL=(ALL) NOPASSWD: /bin/systemctl start docker, /bin/systemctl stop docker, /bin/systemctl restart docker
%k8s-admins ALL=(ALL) NOPASSWD: /usr/local/bin/kubectl, /usr/local/bin/helm
%monitoring ALL=(ALL) NOPASSWD: /bin/systemctl * prometheus, /bin/systemctl * grafana-server
EOF
```
---
#### 🔹 Безопасность и лучшие практики

##### 🔸 Принципы безопасности
```bash
# 1. Принцип минимальных привилегий
# Давать только необходимые права

# 2. Разделение обязанностей  
# Разные роли - разные пользователи

# 3. Регулярный аудит
# Проверка активных пользователей и их прав

# 4. Надежные пароли
# Сложные пароли и их регулярная смена

# 5. Мониторинг активности
# Логирование и анализ действий пользователей
```

##### 🔸 Настройка политик паролей
```bash
# Установка модуля PAM для политик паролей
sudo apt install libpam-pwquality  # Ubuntu/Debian
sudo yum install libpwquality      # CentOS/RHEL

# Настройка политики паролей в /etc/pam.d/common-password
sudo sed -i 's/pam_pwquality.so/& minlen=12 dcredit=-1 ucredit=-1 lcredit=-1 ocredit=-1/' /etc/pam.d/common-password

# Настройка истечения паролей в /etc/login.defs
sudo sed -i 's/PASS_MAX_DAYS.*/PASS_MAX_DAYS   90/' /etc/login.defs
sudo sed -i 's/PASS_MIN_DAYS.*/PASS_MIN_DAYS   7/' /etc/login.defs  
sudo sed -i 's/PASS_WARN_AGE.*/PASS_WARN_AGE   14/' /etc/login.defs

# Применение политики к существующим пользователям
for user in $(cut -d: -f1 /etc/passwd | grep -v "^#" | grep -v "root"); do
    sudo chage -M 90 -m 7 -W 14 "$user" 2>/dev/null
done
```

##### 🔸 Настройка sudo
```bash
# Создание группы администраторов
sudo groupadd wheel

# Добавление пользователей в группу
sudo usermod -aG wheel admin_user

# Настройка sudoers для группы wheel
echo "%wheel ALL=(ALL) ALL" | sudo tee -a /etc/sudoers.d/wheel

# Настройка sudo с паролем и тайм-аутом
cat << 'EOF' | sudo tee /etc/sudoers.d/security
Defaults passwd_timeout=1
Defaults passwd_tries=3
Defaults timestamp_timeout=15
Defaults requiretty
Defaults log_host, log_year, logfile="/var/log/sudo.log"
EOF

# Специфичные права для групп
cat << 'EOF' | sudo tee /etc/sudoers.d/groups
%developers ALL=(www-data) NOPASSWD: /bin/systemctl restart apache2, /bin/systemctl reload apache2
%dba ALL=(mysql,postgres) NOPASSWD: /usr/bin/mysql, /usr/bin/psql
%backup-operators ALL=(ALL) NOPASSWD: /usr/bin/mysqldump, /usr/bin/pg_dump, /bin/tar, /bin/rsync
EOF
```

##### 🔸 Мониторинг и аудит
```bash
# Скрипт аудита пользователей
#!/bin/bash
# user_audit.sh

AUDIT_LOG="/var/log/user_audit.log"
DATE=$(date '+%Y-%m-%d %H:%M:%S')

echo "=== User Security Audit - $DATE ===" >> $AUDIT_LOG

# Пользователи с UID 0 (должен быть только root)
echo "Users with UID 0:" >> $AUDIT_LOG
awk -F: '$3==0 {print $1}' /etc/passwd >> $AUDIT_LOG

# Пользователи без пароля
echo "Users without password:" >> $AUDIT_LOG  
awk -F: '($2 == "" || $2 == "*") {print $1}' /etc/shadow 2>/dev/null >> $AUDIT_LOG

# Пользователи с истекшими паролями
echo "Users with expired passwords:" >> $AUDIT_LOG
for user in $(cut -d: -f1 /etc/passwd); do
    if chage -l "$user" 2>/dev/null | grep -q "Password expires.*in the past"; then
        echo "$user" >> $AUDIT_LOG
    fi
done

# Неактивные пользователи (не входили больше 90 дней)
echo "Inactive users (90+ days):" >> $AUDIT_LOG
lastlog -b 90 | grep -v "Never logged in" | awk 'NR>1 {print $1}' >> $AUDIT_LOG

# Пользователи с правами sudo
echo "Users with sudo privileges:" >> $AUDIT_LOG
getent group sudo | cut -d: -f4 | tr ',' '\n' >> $AUDIT_LOG
getent group wheel | cut -d: -f4 | tr ',' '\n' >> $AUDIT_LOG

# Проверка домашних каталогов
echo "Home directories with wrong permissions:" >> $AUDIT_LOG
ls -la /home | awk 'NR>1 && substr($1,8,1)=="w" {print $9}' >> $AUDIT_LOG

echo "=== End Audit ===" >> $AUDIT_LOG
```

##### 🔸 Автоматизация управления пользователями
```bash
#!/bin/bash
# user_management.sh - Скрипт для управления пользователями

# Создание пользователя с полной настройкой
create_user() {
    local username=$1
    local fullname=$2
    local groups=$3
    local department=$4
    
    # Создание пользователя
    sudo useradd -m -s /bin/bash -c "$fullname" -G "$groups" "$username"
    
    # Генерация временного пароля
    temp_password=$(openssl rand -base64 12)
    echo "$username:$temp_password" | sudo chpasswd
    
    # Принуждение к смене пароля при первом входе
    sudo chage -d 0 "$username"
    
    # Создание персонального каталога в отделе
    if [[ -n "$department" && -d "/shared/$department" ]]; then
        sudo mkdir -p "/shared/$department/$username"
        sudo chown "$username:$department" "/shared/$department/$username"
        sudo chmod 750 "/shared/$department/$username"
    fi
    
    # Настройка SSH каталога
    sudo mkdir -p "/home/$username/.ssh"
    sudo chmod 700 "/home/$username/.ssh"
    sudo chown "$username:$username" "/home/$username/.ssh"
    
    # Добавление стандартных алиасов
    cat << 'EOF' | sudo tee -a "/home/$username/.bashrc"

# Custom aliases
alias ll='ls -la'
alias la='ls -A'  
alias l='ls -CF'
alias ..='cd ..'
alias ...='cd ../..'
alias grep='grep --color=auto'
alias h='history'
alias c='clear'
EOF
    
    sudo chown "$username:$username" "/home/$username/.bashrc"
    
    echo "User $username created successfully"
    echo "Temporary password: $temp_password"
    echo "User must change password on first login"
}

# Деактивация пользователя (вместо удаления)
deactivate_user() {
    local username=$1
    
    # Блокировка аккаунта
    sudo usermod -L "$username"
    
    # Изменение shell на /bin/false
    sudo usermod -s /bin/false "$username"
    
    # Установка даты истечения на вчера
    sudo usermod -e $(date -d "yesterday" '+%Y-%m-%d') "$username"
    
    # Архивирование домашнего каталога
    if [[ -d "/home/$username" ]]; then
        sudo tar -czf "/backup/deactivated_user_${username}_$(date +%Y%m%d).tar.gz" \
            -C /home "$username"
        echo "Home directory archived to /backup/deactivated_user_${username}_$(date +%Y%m%d).tar.gz"
    fi
    
    echo "User $username deactivated"
}

# Реактивация пользователя
reactivate_user() {
    local username=$1
    
    # Разблокировка аккаунта  
    sudo usermod -U "$username"
    
    # Восстановление shell
    sudo usermod -s /bin/bash "$username"
    
    # Убрать дату истечения
    sudo usermod -e "" "$username"
    
    # Принудить смену пароля
    sudo chage -d 0 "$username"
    
    echo "User $username reactivated"
    echo "User must change password on next login"
}

# Массовое создание пользователей из CSV файла
bulk_create_users() {
    local csv_file=$1
    
    # Формат CSV: username,fullname,groups,department
    while IFS=',' read -r username fullname groups department; do
        if [[ "$username" != "username" ]]; then  # пропустить заголовок
            create_user "$username" "$fullname" "$groups" "$department"
        fi
    done < "$csv_file"
}

# Отчет по пользователям
generate_user_report() {
    local report_file="/tmp/user_report_$(date +%Y%m%d).txt"
    
    {
        echo "=== User Report - $(date) ==="
        echo
        echo "Total users: $(getent passwd | wc -l)"
        echo "Active users (UID >= 1000): $(getent passwd | awk -F: '$3 >= 1000 {count++} END {print count+0}')"
        echo
        echo "Users by group:"
        for group in $(cut -d: -f1 /etc/group | sort); do
            members=$(getent group "$group" | cut -d: -f4)
            if [[ -n "$members" ]]; then
                echo "$group: $members"
            fi
        done
        echo
        echo "Recently created users (last 30 days):"
        find /home -maxdepth 1 -type d -newerct "30 days ago" -printf "%f\n" | sort
        echo
        echo "Last login information:"
        lastlog | head -20
    } > "$report_file"
    
    echo "Report generated: $report_file"
}

# Использование функций
case "$1" in
    create)
        create_user "$2" "$3" "$4" "$5"
        ;;
    deactivate) 
        deactivate_user "$2"
        ;;
    reactivate)
        reactivate_user "$2"
        ;;
    bulk)
        bulk_create_users "$2"
        ;;
    report)
        generate_user_report
        ;;
    *)
        echo "Usage: $0 {create|deactivate|reactivate|bulk|report}"
        echo "Examples:"
        echo "  $0 create john 'John Doe' 'users,developers' 'development'"
        echo "  $0 bulk users.csv"
        echo "  $0 report"
        ;;
esac
```

 ##### 🔸 Настройка автоматической очистки
 ```bash
 #!/bin/bash
# cleanup_users.sh - Автоматическая очистка неактивных пользователей

# Поиск и деактивация неактивных пользователей
cleanup_inactive_users() {
    local inactive_days=${1:-365}  # по умолчанию 365 дней
    
    echo "Searching for users inactive for more than $inactive_days days..."
    
    # Получить список пользователей, которые не входили указанное количество дней
    lastlog -b "$inactive_days" | awk 'NR>1 && $1 != "root" && !/Never logged in/ {print $1}' | \
    while read -r username; do
        # Проверить, что это не системный пользователь
        user_uid=$(id -u "$username" 2>/dev/null)
        if [[ $user_uid -ge 1000 ]]; then
            echo "Deactivating inactive user: $username"
            
            # Деактивировать пользователя
            sudo usermod -L "$username"
            sudo usermod -s /bin/false "$username"
            
            # Отправить уведомление администратору
            echo "User $username has been deactivated due to inactivity (${inactive_days}+ days)" | \
                mail -s "User Deactivation Notice" admin@company.com
        fi
    done
}

# Очистка временных файлов пользователей
cleanup_temp_files() {
    # Очистка старых файлов в /tmp принадлежащих деактивированным пользователям
    find /tmp -type f -mtime +7 -exec ls -la {} \; | \
    while read -r line; do
        owner=$(echo "$line" | awk '{print $3}')
        file=$(echo "$line" | awk '{print $NF}')
        
        # Проверить, деактивирован ли пользователь
        if ! sudo passwd -S "$owner" 2>/dev/null | grep -q "P "; then
            echo "Removing temp file of deactivated user: $file"
            sudo rm -f "$file"
        fi
    done
}

# Архивирование старых логов пользователей
archive_user_logs() {
    local log_retention_days=${1:-90}
    
    find /var/log -name "*.log" -mtime +$log_retention_days -type f | \
    while read -r logfile; do
        echo "Archiving old log file: $logfile"
        sudo gzip "$logfile"
    done
}

# Еженедельное выполнение очистки
if [[ "$1" == "weekly" ]]; then
    cleanup_inactive_users 180
    cleanup_temp_files
    archive_user_logs 90
fi
 ```

 ##### 🔸 Monitoring и логирование
 ```bash
 #!/bin/bash
# user_monitoring.sh - Мониторинг активности пользователей

# Настройка расширенного аудита с auditd
setup_audit() {
    sudo apt install auditd audispd-plugins  # Ubuntu/Debian
    
    # Правила аудита для пользователей
    cat << 'EOF' | sudo tee -a /etc/audit/rules.d/user-monitoring.rules
# Monitor user authentication
-w /etc/passwd -p wa -k user-modify
-w /etc/shadow -p wa -k user-modify
-w /etc/group -p wa -k group-modify
-w /etc/sudoers -p wa -k sudoers-modify

# Monitor user login/logout
-w /var/log/auth.log -p wa -k auth-log
-w /var/log/lastlog -p wa -k last-log

# Monitor sudo usage
-a always,exit -F arch=b64 -S execve -F euid=0 -F auid>=1000 -k sudo-commands
EOF
    
    sudo systemctl restart auditd
}

# Ежедневный отчет о активности пользователей
daily_activity_report() {
    local report_date=${1:-$(date +%Y-%m-%d)}
    local report_file="/var/log/user_activity_$report_date.log"
    
    {
        echo "=== User Activity Report - $report_date ==="
        echo
        
        echo "Failed login attempts:"
        grep "Failed password" /var/log/auth.log | grep "$report_date" | \
            awk '{print $1, $2, $3, $9, $11}' | sort | uniq -c | sort -nr
        echo
        
        echo "Successful logins:"
        grep "Accepted password\|Accepted publickey" /var/log/auth.log | grep "$report_date" | \
            awk '{print $1, $2, $3, $9, $11}' | sort | uniq
        echo
        
        echo "Sudo usage:"
        grep "sudo:" /var/log/auth.log | grep "$report_date" | \
            awk '{print $1, $2, $3, $5, $8, $9}' | sort
        echo
        
        echo "User modifications:"
        grep -E "(useradd|userdel|usermod|passwd)" /var/log/auth.log | grep "$report_date"
        
    } > "$report_file"
    
    # Отправить отчет администраторам
    if [[ -s "$report_file" ]]; then
        mail -s "Daily User Activity Report - $report_date" admin@company.com < "$report_file"
    fi
}

# Обнаружение подозрительной активности
detect_suspicious_activity() {
    # Множественные неудачные попытки входа
    suspicious_ips=$(grep "Failed password" /var/log/auth.log | \
        awk '{print $11}' | sort | uniq -c | awk '$1 > 10 {print $2}')
    
    if [[ -n "$suspicious_ips" ]]; then
        echo "Suspicious IPs with multiple failed logins:"
        echo "$suspicious_ips"
        
        # Автоматическая блокировка (опционально)
        # for ip in $suspicious_ips; do
        #     sudo iptables -I INPUT -s "$ip" -j DROP
        # done
    fi
    
    # Входы в необычное время
    unusual_logins=$(grep "Accepted" /var/log/auth.log | \
        awk '$3 < "06:00:00" || $3 > "22:00:00" {print}')
    
    if [[ -n "$unusual_logins" ]]; then
        echo "Unusual time logins detected:"
        echo "$unusual_logins"
    fi
}

# Настройка cron задач для мониторинга
setup_monitoring_cron() {
    # Ежедневный отчет в 8:00
    echo "0 8 * * * /usr/local/bin/user_monitoring.sh daily_report" | sudo crontab -
    
    # Проверка подозрительной активности каждый час
    echo "0 * * * * /usr/local/bin/user_monitoring.sh detect_suspicious" | sudo crontab -
    
    # Еженедельная очистка в воскресенье в 2:00
    echo "0 2 * * 0 /usr/local/bin/cleanup_users.sh weekly" | sudo crontab -
}
 ```
 ---
#### 🔹 Основы прав доступа

##### 🔸 Типы пользователей
```bash
# Три категории пользователей для каждого файла/каталога:
Owner (u) - владелец файла
Group (g) - группа владельца
Others (o) - все остальные пользователи
```

##### 🔸 Типы разрешений
```bash
# Базовые разрешения:
Read (r) - чтение (4)
Write (w) - запись (2)
Execute (x) - выполнение (1)

# Для файлов:
r - можно читать содержимое файла
w - можно изменять содержимое файла
x - можно выполнять файл как программу

# Для каталогов:
r - можно просматривать список файлов в каталоге
w - можно создавать, удалять, переименовывать файлы в каталоге
x - можно входить в каталог (cd) и обращаться к файлам внутри
```
---
#### 🔹 Просмотр и изменение прав доступа
##### 🔸 Команда ls -l
```bash
# Детальный просмотр
ls -l file.txt
# Вывод: -rw-r--r-- 1 user group 1234 Jan 15 10:30 file.txt
#        ↑         ↑ ↑    ↑     ↑    ↑
#        │         │ │    │     │    └─ имя файла
#        │         │ │    │     └──── размер
#        │         │ │    └────────── группа
#        │         │ └─────────────── пользователь
#        │         └───────────────── количество ссылок
#        └─────────────────────────── права доступа

# Расшифровка строки прав: -rw-r--r--
# Позиция 1: тип файла (- обычный файл, d каталог, l ссылка)
# Позиции 2-4: права владельца (rw-)
# Позиции 5-7: права группы (r--)
# Позиции 8-10: права остальных (r--)

# Дополнительные опции
ls -la                      # включая скрытые файлы
ls -lh                      # размеры в читаемом формате
ls -ld /path/to/directory   # права самого каталога
ls -lR                      # рекурсивно
```

##### 🔸 Команда stat
```bash
# Подробная информация о файле
stat file.txt
stat -c "%a %n" file.txt    # только числовые права и имя
stat -c "%A %n" file.txt    # только символьные права и имя

# Пример вывода stat:
# Access: (0644/-rw-r--r--)  Uid: (1000/username)   Gid: (1000/username)
```

##### 🔸 Команда chmod
```bash
## Символьный режим

# Базовый синтаксис: chmod [ugoa][+-=][rwx] file

# Добавление прав
chmod u+x file.txt          # добавить выполнение владельцу
chmod g+w file.txt          # добавить запись группе
chmod o+r file.txt          # добавить чтение остальным
chmod a+x file.txt          # добавить выполнение всем

# Удаление прав
chmod u-x file.txt          # убрать выполнение у владельца
chmod g-w file.txt          # убрать запись у группы
chmod o-r file.txt          # убрать чтение у остальных
chmod a-w file.txt          # убрать запись у всех

# Установка конкретных прав
chmod u=rw file.txt         # владелец: только чтение и запись
chmod g=r file.txt          # группа: только чтение
chmod o= file.txt           # остальные: никаких прав
chmod u=rwx,g=rx,o=r file.txt  # комбинированная установка

# Комбинированные операции
chmod ug+x file.txt         # добавить выполнение владельцу и группе
chmod a+r,u+w file.txt      # всем чтение, владельцу дополнительно запись
chmod u+rwx,g+rx,o+r file.txt  # полная установка прав

## Числовой (восьмеричный) режим

# Числовые значения:
# 4 = r (чтение)
# 2 = w (запись)  
# 1 = x (выполнение)

# Распространенные комбинации:
0 = --- (никаких прав)
1 = --x (только выполнение)
2 = -w- (только запись)
3 = -wx (запись + выполнение)
4 = r-- (только чтение)
5 = r-x (чтение + выполнение)
6 = rw- (чтение + запись)
7 = rwx (все права)

# Примеры использования
chmod 644 file.txt          # rw-r--r-- (владелец: rw, группа: r, остальные: r)
chmod 755 script.sh         # rwxr-xr-x (владелец: rwx, группа: rx, остальные: rx)
chmod 600 private.txt       # rw------- (владелец: rw, группа: нет, остальные: нет)
chmod 777 file.txt          # rwxrwxrwx (все права всем - НЕ РЕКОМЕНДУЕТСЯ!)
chmod 000 file.txt          # --------- (никаких прав никому)

# Распространенные права файлов:
chmod 644 file.txt          # обычный файл
chmod 664 file.txt          # файл для совместной работы группы
chmod 600 file.txt          # приватный файл
chmod 400 file.txt          # только чтение для владельца

# Распространенные права каталогов:
chmod 755 directory/        # обычный каталог
chmod 775 directory/        # каталог для совместной работы группы
chmod 700 directory/        # приватный каталог
chmod 750 directory/        # группа может просматривать, но не изменять
```

##### 🔸 Рекурсивное изменение прав
```bash
# Рекурсивное применение прав ко всем файлам и каталогам
chmod -R 755 /path/to/directory

# Раздельная установка прав для файлов и каталогов
find /path -type f -exec chmod 644 {} \;      # файлы: 644
find /path -type d -exec chmod 755 {} \;      # каталоги: 755

# Установка прав только для определенных типов файлов
find /path -name "*.sh" -exec chmod +x {} \;  # сделать скрипты исполнимыми
find /path -name "*.txt" -exec chmod 644 {} \; # текстовые файлы
```
---
#### 🔹 Изменение владельца и группы

##### 🔸 Команда chown
```bash
# Базовый синтаксис: chown [пользователь]:[группа] файл

# Изменение только владельца
sudo chown newuser file.txt
sudo chown newuser /path/to/directory

# Изменение владельца и группы
sudo chown newuser:newgroup file.txt
sudo chown user:group /path/to/directory

# Изменение только группы (через chown)
sudo chown :newgroup file.txt

# Рекурсивное изменение
sudo chown -R user:group /path/to/directory

# Примеры практического использования
sudo chown www-data:www-data /var/www/html/*    # веб-файлы
sudo chown mysql:mysql /var/lib/mysql/*         # файлы MySQL
sudo chown -R user:users /home/user/project     # проект пользователя
```

##### 🔸 Команда chgrp
```bash
# Изменение только группы
sudo chgrp newgroup file.txt
sudo chgrp -R developers /path/to/project

# Примеры
sudo chgrp www-data /var/www/html/config.php
sudo chgrp -R staff /shared/documents
```
---
#### 🔹 Специальные права доступа

##### 🔸 SUID, SGID и Sticky bit
```bash
# SUID (Set User ID) - бит 4000
# Файл выполняется с правами владельца, а не пользователя, который его запускает
chmod u+s file              # установить SUID
chmod 4755 file             # числовой способ (4000 + 755)
ls -l /usr/bin/passwd       # пример: -rwsr-xr-x (s вместо x у владельца)

# SGID (Set Group ID) - бит 2000
# Для файлов: выполняется с правами группы
# Для каталогов: новые файлы наследуют группу каталога
chmod g+s directory         # установить SGID
chmod 2755 directory        # числовой способ (2000 + 755)
ls -ld /tmp                 # пример: drwxrwxrwt (t в конце)

# Sticky bit - бит 1000
# В каталоге файлы может удалять только владелец файла или root
chmod +t directory          # установить sticky bit
chmod 1755 directory        # числовой способ (1000 + 755)
ls -ld /tmp                 # пример: drwxrwxrwt

# Удаление специальных битов
chmod u-s file              # убрать SUID
chmod g-s directory         # убрать SGID
chmod -t directory          # убрать sticky bit

# Просмотр специальных прав
find /usr/bin -perm -4000   # найти файлы с SUID
find /usr/bin -perm -2000   # найти файлы с SGID
find /tmp -perm -1000       # найти каталоги со sticky bit
```
---
#### 🔹 Практические сценарии

##### 🔸 Настройка прав для веб-сервера
```bash
# Типичная настройка для Apache/Nginx
sudo chown -R www-data:www-data /var/www/html
find /var/www/html -type d -exec chmod 755 {} \;  # каталоги
find /var/www/html -type f -exec chmod 644 {} \;  # файлы

# Для файлов конфигурации (более строгие права)
sudo chmod 600 /etc/apache2/sites-available/default-ssl.conf
sudo chown root:root /etc/apache2/sites-available/*

# Для логов
sudo chown -R www-data:adm /var/log/apache2
sudo chmod -R 640 /var/log/apache2
```

##### 🔸 Настройка прав для разработки
```bash
# Создание группы разработчиков
sudo groupadd developers

# Добавление пользователей в группу
sudo usermod -aG developers alice
sudo usermod -aG developers bob

# Настройка общего каталога проекта
sudo mkdir /var/www/project
sudo chown -R root:developers /var/www/project
sudo chmod -R 775 /var/www/project
sudo chmod g+s /var/www/project  # SGID для наследования группы

# Установка umask для группы
echo "umask 002" >> /home/alice/.bashrc
echo "umask 002" >> /home/bob/.bashrc
```

##### 🔸 Настройка прав для базы данных
```bash
# MySQL
sudo chown -R mysql:mysql /var/lib/mysql
sudo chmod -R 700 /var/lib/mysql
sudo chmod 600 /etc/mysql/debian.cnf

# PostgreSQL
sudo chown -R postgres:postgres /var/lib/postgresql
sudo chmod -R 700 /var/lib/postgresql
sudo chmod 600 /etc/postgresql/*/main/pg_hba.conf
```
---
#### 🔹 Аудит и мониторинг прав доступа

##### 🔸 Поиск файлов с определенными правами
```bash
# Файлы с правами 777 (небезопасно)
find /path -perm 777

# Файлы с SUID битом
find /usr -perm -4000 -type f

# Файлы, доступные для записи всем
find /path -perm -002 -type f

# Файлы без владельца
find /path -nouser -o -nogroup

# Файлы, изменившиеся за последние 7 дней
find /etc -type f -mtime -7 -exec ls -la {} \;
```

##### 🔸 Скрипт аудита прав доступа
```bash
#!/bin/bash
# security_audit.sh

echo "=== Security Audit Report $(date) ==="

# Файлы с подозрительными правами
echo "Files with 777 permissions:"
find / -perm 777 -type f 2>/dev/null | head -10

echo "Files with SUID bit:"
find /usr /bin /sbin -perm -4000 -type f 2>/dev/null

echo "World-writable files:"
find /etc /usr /var -perm -002 -type f 2>/dev/null | head -10

# Проверка важных файлов
important_files=("/etc/passwd" "/etc/shadow" "/etc/sudoers")
echo "Important files permissions:"
for file in "${important_files[@]}"; do
    if [[ -f $file ]]; then
        ls -l $file
    fi
done

# Пользователи с UID 0
echo "Users with UID 0 (should be only root):"
awk -F: '$3==0 {print $1}' /etc/passwd

# Пустые пароли
echo "Users with empty passwords:"
awk -F: '($2 == "") {print $1}' /etc/shadow 2>/dev/null
```
---
#### 🔹 Лучшие практики безопасности

##### 🔸 Принципы назначения прав
```bash
# Принцип минимальных привилегий
# Давать только необходимые права

# Обычные файлы
chmod 644 file.txt              # владелец: rw, остальные: r

# Исполняемые файлы
chmod 755 script.sh             # владелец: rwx, остальные: rx

# Конфиденциальные файлы
chmod 600 private_key.pem       # только владелец: rw

# Каталоги
chmod 755 public_directory/     # владелец: rwx, остальные: rx
chmod 700 private_directory/    # только владелец: rwx

# Системные файлы
chmod 644 /etc/hosts           # конфигурационные файлы
chmod 600 /etc/shadow          # файлы с паролями
chmod 755 /usr/bin/program     # исполняемые файлы
```

##### 🔸 Регулярные проверки безопасности
```bash
# Еженедельный скрипт проверки
#!/bin/bash
# weekly_security_check.sh

# Логирование
LOG="/var/log/security_check.log"
echo "=== Security Check $(date) ===" >> $LOG

# Проверка файлов с SUID/SGID
echo "SUID/SGID files:" >> $LOG
find / -type f \( -perm -4000 -o -perm -2000 \) 2>/dev/null >> $LOG

# Проверка файлов с широкими правами
echo "World-writable files:" >> $LOG
find / -type f -perm -002 2>/dev/null | head -20 >> $LOG

# Проверка последних входов
echo "Recent logins:" >> $LOG
last -10 >> $LOG

# Отправка отчета администратору
mail -s "Weekly Security Report" admin@company.com < $LOG
```
---

