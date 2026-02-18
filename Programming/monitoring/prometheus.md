#monitor #server #prometheus

- https://prometheus.io
- https://github.com/prometheus-community
- Expotrers - https://prometheus.io/docs/instrumenting/exporters
- Download - https://prometheus.io/download

Это бесплатная система мониторинга и оповещения. Собирает и хранит метрики в виде данных **Time Series** в **TSDB(Time Series Data Base)**, то есть информация о метриках сохраняется с отметкой времени, в которой она была записана. Работает по системе **Pull** (здесь подробнее [[Programming/monitoring/info|info]])
**Monitoring Agent** в Prometheus называется **Exporter**

Есть большое кол-во готовых Exporter:
 - Node Exporter - мониторинг Linux серверов
 - Windows Exporter -  мониторинг Windows серверов
 - MySQL Exporter -  мониторинг MySQL серверов
 - Apache Exporter -  мониторинг Apache серверов
 - Nvidia GPU Exporter -  мониторинг Nvidia GPU
 - cAdvisor - мониторинг контейнеров (docker, k8s) 
 - .....

**Prometheus Metrics**
- После того как вы устанавливаете и запускаете **Exporter**, все метрики которые он мониторит и собирает будут доступны как web-страница под определенным HTTP портом. К примеру для **Node Exporter**:
	-  если private IP вашего сервера `10.0.0.1`
	-  **Node Exporter** публикует все метрики на порту `9100` и будут доступны по - `http://10.0.0.1:9100`
	-  сами метрики будут доступна - `http://10.0.0.1:9100/metrics`

**Prometheus Metric Types**
 - **Counter** :
	 -  Метрика которая может только увеличиваться
	 -  Можно обнулить если перезапустить сервис который мониторится
- **Gauge** :
	-  Метрика которая может увеличиваться и уменьшаться
- **Histogram** :
	-  распределение величин метрик по группам 
- **Summary** :
	-  Показывает Percentile \ Quantile из histogram

**Prometheus Querying**
- для запросов метрик используется **PromQL** 

#### 🔹 Установка на сервере

Скачиваем нужный файл с сайта - https://prometheus.io/download
```bash
mkdir prometheus && cd prometheus
wget <link>
# распаковыввем
tar xvfz prometheus-2.11.linux.tar.gz

# после распаковки можем удалить все папки и файла, кроме бинарника prometheus и файла prometheus.yaml (конфиг), сам конфиг можно тоже почистить оставив только секции - global (где указываем с каким интервалом будем забирать метрики) и scrape_configs (где указываем откуда забираем метрики) 

# простой запуск
./prometheus

# более правильная настройка
# отправляем бинарник к исполняемым файлам
mv prometheus /usr/bin
mkdir /etc/prometheus
mkdir /etc/prometheus/data
mv prometheus.yaml /etc/prometheus

# создаем системного пользователя (без домашней директории и логина) для работы с prometheus
useradd -rs /bin/false prometheus
# данного пользователя делаем owner бинарника и конфигов
chown prometheus:prometheus /usr/bin/prometheus
chown prometheus:prometheus -R /etc/prometheus
# теперь нужно бинарник prometheus запускать как сервис
nano /etc/systemd/system/prometheus.service
# добавляем
[Unit]
Description=Prometheus Server
After=network.target

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
ExecStart=/usr/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yaml \
    --storage.tsdb.path /etc/prometheus/data

[Install]
WantedBy=multi-user.target

# перезапускаем демона и запускаем наш сервис
systemctl daemon-reload
systemctl start prometheus
systemctl status prometheus

# чтоб сервис запускался при перезапуске компа
systemctl enable prometheus
```

или можно использовать скрипт, который это все делает - [[install_prometheus]]

#### 🔹 Установка на node_exporter
здесь скрипт по установки [[install_prometheus_node_exporter]]

> если нужно настраивать мониторинг при auto_scalling подробнее здесь - https://www.youtube.com/watch?v=RICJljsn8jE