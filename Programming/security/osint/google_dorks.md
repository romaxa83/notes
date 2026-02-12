#osint #google

**Google Dorks** — це техніка інформаційного пошуку з використанням операторів Google (Advanced Search Operators).
**Оператори пошуку Google** — це спеціальні символи та запити, які розширюють можливості звичайного текстового пошуку.

З допомогою Google Dorks можна виконувати різноманітні задачі з OSINT та кібербезпеки, виявляти потенційні вразливості сайтів і додатків, знаходити і попереджати витоки даних, виявляти приховану інформацію, яка була проіндексована роботами і потрапила в результати пошукової видачі (Search Engine Result Page, SERP).

#### 🔹 Які дані можна зібрати з допомогою Google Dorks?
- Пошук людей (імена та прізвища, нікнейми);
- Пошук інформації у соцмережах;
- Метадані і log-файли;
- Електронні адреси;
- Файли і документи (pdf, doc, txt, sql, mp3, jpeg, avi та ін.);
- Веб-сторінки (URL);
- Веб-служби та сервери;
- Мережеві пристрої (IoT);
- Адміністративні частини електронних ресурсів і додатків (адмін-панелі);
- Вразливості в електронних ресурсах та системах;
---
#### 🔹 Які типові задачі допомогає вирішити Google Dorks?
- Пошук службових, адміністративних, конфіденційних, технічно несправних веб-сторінок, форм входу і авторизації, які знаходяться у вільному доступі і створюють ризики безпеки;
- Пошук вразливих URL-адрес до інклюдів, ін’єкцій, переборів, фаззінгу та ін.;
- Пошук службових, конфіденційних, користувацьких файлів і папок, які опинилися у відкритому доступі й створюють ризики безпеки;
- Пошук будь-яких інформаційних витоків, баз даних, імен користувачів та доступів, які опинилися у відкритому доступі і компрометують систему;
- Пошук вразливостей у популярних веб-серверах, операційних системах, CMS-системах, програмному забезпеченні;
- Пошук інших скомпрометованих веб-вузлів ІТ-інфраструктури, платформ, систем;
- Швидкий і ефективний збір різної інформації в інтернеті, накопичення даних/фактів з відкритих джерел про конкретний об’єкт/бренд/особу. Аналітика і дослідження джерельних даних, Big Data операції, конкурентна розвідка (Business Intelligence, BI) та ін.

Фактично, Google Dorks може виступати у ролі ручного пошукового сканера і парсера для аудиту кібербезпеки та OSINT-досліджень.

---
#### 🔹 Синтаксис Google Dorks

```text
operator:пошуковий_запит
```

==Використовуються такі символи:==
- `(+)` —  додати ще один запит;
- `(-)` —  використати стоп-слово;
- `(“ ”)` —  шукати в точній відповідності;
- `(.)` —  розділювач;
- `(*)` —  перелік рандомних значень;
- `(|)` — булеановий запит ‘OR’ (АБО).

==Типові оператори Google:==
- `inurl` —  пошук по заданому URL;
- `intext` —  пошук по заданому тексту;
- `filetype`  —  пошук заданих файлів;
- `cache` —  пошук кешованої версії сторінки;
- `site` —  пошук по домену;
- `intitle` —  пошук по заголовку.
---
#### 🔹 Підбірка дорків

больше дорков - https://research.kr-labs.com.ua/google-dorks-for-osint/

##### 🔸 Пошук файлів

```bash
# шукати на вказаному веб-сайті файли заданих форматів
site:example.com (inurl:pdf OR inurl:txt OR inurl:doc OR inurl:docx OR inurl:mp4 OR inurl:avi OR inurl:xlsx OR inurl:log)

# шукати на вказаному веб-сайты PDF-файли, які містять задане ключове слово в Title (заголовку)
site:example.com intitle:ключове_слово filetype:pdf

# пошук файлів вказаних форматів, що містять сайти у конкретній доменній зоні
filetype:"xls | xlsx | doc | docx | txt | pdf" site:.com

# пошук лістингків директорій (Directory Listing), що містять PDF-файли і задане ключове слово
intitle:index.of pdf inurl:ключове_слово

# пошук файлів статистики php
inurl:phpinfo.php

# пошук SQL-дампів
“\#mysqldump” filetype:sql
“\# Dumping data for table”
“information_schema” filetype:sql
“\# phpMyAdmin MySQL-Dump”
“index of” “database.sql.zip”

# пошук бекапів
inurl:backup intitle:index.of inurl:admin
“Index of /backup”

# пошук системних логів
intext:"index of /" "Index of" access_log
intext:"index of /" "Index of" error_log
intitle:"index of" "debug.log"
intitle:"index of" "PHP_errors.log"

# пошук лістингу директорії root
allintitle: "index of/root"

# пошук лістинга директорій на веб-серверах Apache
intitle:"index of" "powered by apache " "port 80"

# пошук файлів образів ISO
?intitle:index.of? iso

# пошук музичних файлів MP3
intext:”parent directory” intext:”[MP3]“

# пошук файлів звіту безпеки сканера Acunetix
intitle: "Generated by Acunetix WVS Reporter"

# пошук файлів звіту безпеки сканера Burp Suite
intitle:"Burp Scanner Report" | "Report generated by Burp Scanner"

# пошук файлів системи збору статистики відвідування Webalizer
"Index of" inurl:webalizer

# пошук Google-таблиць за вказаним ключовим словом
site:docs.google.com “/spreadsheets/d/” “keyword”

# пошук документів Google Docs за вказаним ключовим словом
site:docs.google.com inurl:/document/d/ “keyword”

# пошук файлів на Google Disc за вказаним ключовим словом
site:drive.google.com inurl:/file/d/ “keyword”

# пошук розшарених папок за вказаним ключовим словом
site:drive.google.com inurl:/drive/folders/ “keyword”

# пошук публічнодоступних спільнот Viber
site:invite.viber.com intitle:хакер

intext:Host Vulnerability Summary Report filetype:pdf
filetype:conf inurl:firewall -intitle:cvs
filetype:xls username password email

# пошук конфігураційних файлів баз даних
filetype:sql “INSERT INTO” “VALUES” AND intext:“password”

# пошук Cloud Storage Buckets (AWS S3, Google Cloud)
site:s3.amazonaws.com “target.com” OR - site:storage.googleapis.com “bucket”

# пошук витоків API-ключів
ext:env “API_KEY” OR “API_SECRET” OR “DB_PASSW_ORD”
```

##### 🔸 Пошук витоків даних

```bash
site:pastebin.com "CVV"
site:pastebin.com "DB_NAME"
site:jsfiddle.net | site:codepen.io "api key"
site:gist.github.com api key
site:trello.com intext:@gmail.com
site:trello.com intext:admin
site:trello.com intext:FB
site:trello.com intext:accesskey
site:trello.com intext:sql intext:sa
site:trello.com intext:postgresql intext:root
site:trello.com intext:BEGIN RSA PRIVATE KEY
site:github.com "BEGIN RSA PRIVATE KEY"
site:pastebin.com "API_KEY="
site:example.com ext:log | ext:json "password"
intext:.DS_Store & intitle:index -github
intitle:"index of" intext:".ds_store"
inurl:.DS_Store intitle:index of
inurl:.DS_Store intitle:index.of
inurl:users.json + "username"
inurl:(htm|html|php) intitle:”index of” +”last modified” +”parent directory” +description +size +(wma|mp3)
index of /mp3 greatest hits
intitle:"Index of" config.php site:*.ua
intitle:"Index of" *.log site:*.ua
intitle:"Index of" *.logs site:*.ua
intitle:"Index of" *.backup site:*.ua
intitle:"Index of" *.backups site:*.ua
intitle:"Index of" *.sql site:*.ua
intitle:"Index of" *.htaccess site:*.ua
intitle:"Index of" *.debug site:*.ua
intitle:"Index of" *.auth site:*.ua
intitle:"Index of" *.keys site:*.ua
intitle:"Index of" *.admin site:*.ua
intitle:"Index of" *.vpn site:*.ua
inurl:”auth_user_file.txt”
“not for distribution” confidential
“not for distribution” confidential site:*.ua
intitle:"Index of" *.etc site:*.ua
intitle:"Index of" *.www site:*.ua
intitle:"Index of" *.database site:*.ua
intitle:"Index of" *.xlsx site:*.ua
intitle:"Index of" *.docx site:*.ua
intitle:"Index of" *.pdf intext:заява site:*.ua
Index of /password
Index of / +passwd
Index of / password.txt
inurl:reset password site:*.ua
intitle:"Index of" *.smtp site:*.ua
intitle:index.of parent directory
intitle:index.of parent directory site:*.ua
intitle:index.of name size site:*.ua
intitle:index.of server.at
intitle:index.of server.at site:*.ua
intitle:index.of cgiirc.config
intitle:index.of finances.xls
intitle:Usage Statistics for Generated by Webalizer site:*.ua
inurl:/plesk-stat/webstat "2024"
intitle:index.of trillian.ini
intitle:Index.of etc shadow site:*.ua
intitle:index.of.etc site:*.ua
s3 site:amazonaws.com filetype:xls password
intitle:"index of" intext:login.csv
"password.xlsx" ext:xlsx
filetype:doc inurl:"gov" intext:"default password is"
filetype:xls inurl:”email.xls” site:*.ua
inurl:"?db_backup" | inurl:"dbbackup" -site:github.com "sql.gz" | "sql.tgz" | "sql.tar" | "sql.7z"
```

##### 🔸 Пошук вразливих веб-серверів, адмін-панелей

```bash
# пошук адмін-панелей
inurl:admin intitle:login

# пошук адмін-панелей, форм авторизації, сторінок входу, сторінок привітання
intitle:“Admin Login” “Welcome to” inurl:admin

# пошук сторінок входу і авторизації
site:*/auth intitle:login

# пошук сторінок контроль-панелей
inurl:admincp/index.php

# пошук адмін-панелей на конкретному сайті
site:.com inurl:admin login

# пошук сторінок входу у панель адміністрування
inurl:/administrator/

# пошук адмінок поштових клієнтів
inurl:/webmail/

# пошук адмінок cPanel
intitle:cPanel login inurl:*cpanel *2083

# пошук URL-адреси системи управління базами данних PhpMyAdmin
"Index of" inurl:phpmyadmin

# пошук систем централізованого моніторингу Kibbana
inurl:app/kibana intext:Loading Kibana

# пошук систем моніторингу Zabbix
inurl:8080/dashboard.php

# пошук NGINX-серверів
intitle:”Welcome to nginx!”

# пошук Debian серверів
intext:”Welcome to nginx on Debian!”

# пошук UBUNTU-сервера
intitle:”Apache2 Ubuntu Default Page: It works”

# пошук ORACLE-серверів
“Oracle HTTP Server/* Server at” intitle:index.of

# пошук сайтів на CMS Drupal
inurl:”/user/register” “Powered by Drupal” -CAPTCHA -”Access denied”

# пошук FTP-серверів з лістингом директорій
intitle:”index of” inurl:ftp

# пошук адмін-панелей VESTA CP
intitle:”VESTA*” inurl:”*8083”

# пошук адмін-панелей CyberPanel
intitle:”CyberPanel*” inurl:”*8090”

# пошук вразливих до атаки Shellshock серверів в заданій доменній зоні
filetype:cgi inurl:cgi-bin site:.cn

intitle:index.of Apache/2.0.40 Server at
allintitle:Welcome to Windows 2000 Internet Services
"Running in Child mode"
"This report was generated by WebLog"
intitle:"Pi-hole Admin Console"
"please sign in" "sign in" "gophish" +"login"
"login" intitle:"scada login"
"iTop Installation Wizard" "Prerequisites validation"

# пошук серверів з файловим менеджером Roxy, який вразливий до несанкціонованого доступу до файлів (читання, оновлення, видалення)
intitle:”Roxy file manager”

# доступ до сервера Prometheus з несанкціонованим доступом до дашборду
"Prometheus Time Series Collection and Processing Server"

# сервери на базі SudeKiq
inurl:/sidekiq intext:"memory usage" intext:"polling interval"

# пошук витоків даних серверів X-Prober
intitle:xprober intext: CPU

# пошук витоків серверів X-Prober
inurl:/xprober ext:php

# пошук публічно доступних дашбордів на порту 8080
inurl:8080/dashboard
```

##### 🔸 Пошук IoT-пристроїв

```bash
# пошук камер спостереження у відкритому доступі
inurl:”view.shtml” “Network Camera“
inurl:/ViewerFrame? intitle:”Network Camera NetworkCamera”

# пошук камер спостереження за вказаною назвою
inurl:/config/cam_portal.cgi “Panasonic“

# пошук IP-камер
intitle:”IP CAMERA Viewer” intext:”setting | Client setting”

# пошук веб-камер
inurl:top.htm inurl:currenttime
inurl:"wvhttp-01"
inurl:"viewerframe?mode=”
inurl:"videostream.cgi"
inurl:"webcapture”
inurl:”snap.jpg”
inurl:”snapshot.jpg”
inurl:”video.mjpg”
```

##### 🔸 Пошук поштових серверів і електронних листів

```bash
# пошук файлів email-заголовків
filetype:eml eml +intext:”Subject” +intext:”From”

# пошук електронних листів в форматі PST
filetype:pst pst -from -to -date

# помилка CGI-скрипту з технічними деталями відправки email-листа
intitle:”Execution of this script not permitted”

# заголовки електронних листів, які не були відправлені, доступні на UNIX-серверах
intitle:index.of dead.letter

# пошук кешованих даних, пов’язаних з відправкою електронних листів, з доступом до лістингу директорій
intitle:index.of inbox

# логи поштових серверів
intitle:”Index Of” -inurl:maillog maillog size

# пошук документів MS Excel з електронними адресами
filetype:xls inurl:”email.xls”

# знайти файли поштових серверів з інформацією за вказаними ключовими запитами
( filetype:mail | filetype:eml | filetype:mbox | filetype:mbx ) intext:querie|subject

# логи Windows Registry Files, які містять email-інформацію
filetype:reg reg +intext:”internet account manager”

# пошук баз даних email-адрес
intext:”@gmail.com” AND intext:”@yahoo.com” filetype:sql

# пошук pdf-файлів з email-адресами
filetype:pdf <domain> “email”
Index of /mail
```

##### 🔸 Пошук сторінок, блогів, резюме

```bash
# пошук блогів на вказаному домені
inurl:.edu+inurl:blog

# пошук сайтів-блогів на вказаному домені, які містять задане ключове слово
“ключове слово” blog site:.org
cybersecurity +inurl:blog site:.edu

# пошук Google Таблиць з заданим ключовим словом у тексті
site:https://docs.google.com/spreadsheets intext:ключове слово

# пошук сайтів з підпапкою security, яке містять задане ключове слово
intext:ключове слово inurl:/security

# пошук публічних Whatsapp-чатів, що містять вказані ключові слова
site:chat.whatsapp.com crypto | forex | bitcoin | invest

# пошук сторінок Linkedin за вказаними параметрами;
site:https://linkedin.com/in “Cyber Security Analyst” (☎ OR ☏ OR ✆ OR 📱) +”London”

# пошук резюме (CV) у публічному доступі
intitle:CV+index of

# пошук резюме у відкритому доступі за вказаним ключовим словом
inurl:resume “devops”

# пошук профілів, які у своєму описі містять вказаний домен
site:linkedin.com/in OR site:linkedin.com/pub intext:"@microsoft.com"

# пошук профілів LinkedIn
site:linkedin.com “Your place of employment”
site:linkedin.com “Your LinkedIn headline”
site:linkedin.com/in/ "open to work" "penetration tester"
site:linkedin.com/in "CompanyName"
site:linkedin.com/in "Цукерберг Марк Орестович"
site:ru.linkedin.com/in "gmail.com" DevOps -recruiter
site:ua.linkedin.com/in "gmail.com" SEO -recruiter
site:ru.linkedin.com/pub -pub.dir DevOps -recruiter
site:ru.linkedin.com/in "gmail.com" DevOps -recruiter
site:ru.linkedin.com/in devops "cybersecurity"
site:ru.linkedin.com/in DevOps (Anna OR Olga OR Tatiana OR Svetlana OR Anastasia) -recruiter
site:ru.linkedin.com/in Kubernetes Zabbix -recruiter -moscow
site:linkedin.com “Front end developers group” (.Net |dot Net) Greater Boston Area) -dir -job -jobs -sample -samples -template -resume service -resume writers -resume writing
site:linkedin.com inurl:(in | pub) “logo Boolean strings” -inurl:dir
site:linkedin.com “front end developers logo” (C# |.Net) “location * Greater Boston Area”
site:linkedin.com “people you know” “logo * Ernst & Young Employees and Alumni” java
site:linkedin.com/in OR site:linkedin.com/pub "Jane" AND intitle:"Microsoft" AND (intext:"Language*" AND "Mandarin")
site:linkedin.com/in OR site:linkedin.com/pub intitle:"Vice President" AND intitle:"Microsoft" AND ("Education" AND "UNSW")
site:linkedin.com/in OR site:linkedin.com/pub intext:"@microsoft.com"
site:ask.fm "anacastellonvm@icloud.com"
```

## Як захистити сайт від Google Dorks і хакінгу?

1. Встановити і налаштувати Firewall на рівні сервера — CSF/USW, IPtables, Fail2ban, ModSecurity etc. і на рівні Web Apllication (WAF) — WordFence, All in One Security, Sucuri та інші;
2. Перевести сайт на DNS-обслуговування в CloudFlare (захист мережевої інфраструктури — IP-адрес, TCP/UDP-портів, анти-DDOS/Brute-Force і т.д.);
3. Налаштувати фільтрацію трафіка і вхідних запитів;
4. Налаштувати примусове HTTPS-з’єднання та налаштувати усі заголовки безпеки ([HTTP Security Headers](https://owasp.org/www-project-secure-headers/));
5. Налаштувати блокування токсичних IP-адрес, User-Agent’ів, підозрілих ботів і т.д. ([працювати з AbuseIPDB](https://www.abuseipdb.com/));
6. Налаштувати файл конфігурації robots.txt та заборонити пошуковикам індексувати конфіденційні URL-адреси, файли, папки;
7. Окремі сторінки додатково виключити з індексу пошукових систем з допомогою тегу `<meta robots>`;
8. Налаштувати коректні права доступу (file permissions) для директорій і файлів (див. [CHMOD-калькулятор](https://chmod-calculator.com/));
9. Налаштувати і захистити мапу сайту XML Sitemap від несанкціонованого парсингу;
10. Налаштувати і захистити стрічку новин RSS від несанкціонованого парсингу;
11. Увесь ексклюзивний контент на сайті захистити з допомогою систем захисту від несанкціонованого копіювання — [DMCA](https://www.dmca.com/) і [Copyscape](https://www.copyscape.com/);
12. Вимкнути перегляд вихідного коду на сайті та заборонити копіювання текстів з вашого сайту (або тільки з автоматичним посиланням на ресурс);
13. Регулярно здійснювати пошук інформації на своєму сайті з допомогою Google Dorks. Окремі URL-адреси або контент видаляти з пошукової видачі Google через онлайн-інструменти: [Google Remove Outdated Conent](https://search.google.com/search-console/remove-outdated-content), [Removing Content From Google](https://support.google.com/legal/troubleshooter/1114905), [Personal Information Removal Request](https://www.google.com/webmasters/tools/legal-removal-request?complaint_type=14), [Google Search Сonsole](https://search.google.com/search-console/removals).

