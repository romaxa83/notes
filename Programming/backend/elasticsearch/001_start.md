
- DOCS - https://www.elastic.co/guide/en/elasticsearch/reference/index.html
- О elastic - https://www.youtube.com/watch?v=urtom-xqZRo&t=1622s
- https://habr.com/ru/post/280488/
- https://medium.com/nuances-of-programming/%D0%BD%D0%B0%D1%87%D0%B0%D0%BB%D0%BE-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B-%D1%81-elasticsearch-%D0%B2-python-%D1%87%D0%B0%D1%81%D1%82%D1%8C-1-73d6aa6be215
- MANUAL - https://codedzen.ru/elasticsearch-urok-3-1-mapping-skhema-dokumentov/
- СТАТЬЯ ПРО ПОЛНОТЕКСТОВЫЙ ПОИСК - https://code.burlakov.io/elastic-search/
- КНИГА ПО ELK - http://flibusta.site/b/554461/read
- INFO - https://ru.bmstu.wiki/Elastic_Stack
- СВЯЗКА ELK и MONOLOG - https://phpprofi.ru/blogs/post/74
- ПОИСК НЕТОЧНЫХ СООТВЕТСТВИЙ - https://ruhighload.com/elastic+%D0%BF%D0%BE%D0%B8%D1%81%D0%BA+%D0%BD%D0%B5%D1%82%D0%BE%D1%87%D0%BD%D1%8B%D1%85+%D1%81%D0%BE%D0%BE%D1%82%D0%B2%D0%B5%D1%82%D1%81%D1%82%D0%B2%D0%B8%D0%B9
- INSTALL WITH DOCKER - https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html
- РЕПОЗИТОРИЙ С ELK - https://github.com/sqshq/ELK-docker
- РАБОТА С LOGSTASH - https://habr.com/ru/post/165059/
- Ошибки при использовании elastic - https://habr.com/ru/company/yamoney/blog/419041/
- УСТАНОВКА ES 7 - https://badcode.ru/kak-ustanovit-elasticsearch-ubuntu-vagrant-homestead-docker-windows/
- Метрика в es - https://habr.com/ru/company/yamoney/blog/358550/
- https://habr.com/ru/company/directum/blog/460263/
- Мониторинг логов на основе Elastiсsearch+Fluentd+Kibana - https://kamaok.org.ua/?p=3368
- Установка на ELK на Debian - http://dev-lab.info/2019/04/%d1%83%d1%81%d1%82%d0%b0%d0%bd%d0%be%d0%b2%d0%ba%d0%b0-elasticsearch-logstash-%d0%b8-kibana-%d0%bf%d0%be%d0%b4-debian-linux/#more-2240
- Служебная информация -  https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html
- https://github.com/cosminseceleanu/tutorials/tree/master/docker-logs-elk/dummy-app
- сортировка по гео данным - https://www.elastic.co/guide/en/elasticsearch/reference/6.8/search-request-sort.html#geo-sorting

- Обрабатываем логи в Logstash - https://dotsandbrackets.com/processing-logs-logstash-ru/

- Тестовые логи для апачач (для logstash) - https://github.com/elastic/elk-index-size-tests/blob/master/logs.gz
---
#### 🔹 Стек elk-docker
 
 - https://elk-docker.readthedocs.io/
- https://habr.com/ru/post/282866/

==перед стартом образа нужно увеличить виртуальную память==
```bash
sudo sysctl -w vm.max_map_count=262144
```
---
#### 🔹 Кластер

Elasticsearch — это распределенная система, что означает она состоит из одного или нескольких узлов, которые действуют как одно целое, что позволяет масштабировать и обрабатывать нагрузку. В кластер могут входить несколько узлов, возможно добавлять и удалять узлы на лету.

==Shard==
Индекс представляет собой набор из одного или нескольких шардов. ==Шард== - это экземпляр Apache Lucene где хранятся данные. Большое кол-во данных можно разделить на несколько шардов(указать на сколько, elastic сам разобьет). Есть два типа шардов - мастер и реплика.Мастер(главный) в него происходит запись и чтение ,реплика - точная копия мастера, с нее можно только читать.

> Обновления в elastic дорогостоящая операция,(получение старого документа, изменение и повторная индексация в качестве нового документа) нужно пытаться свести обновления до минимума.

```
реляционая бд         	| elastic
-----------------------------------------------
база данных(database) 	| индекс(index)
таблица(table)			| тип(type)
запись(row)				| документ(document)
колонка(column)			| поле(field)
схема(schema)			| отображение(mapping)
sql						| query dsl
```
---
#### 🔹 Curl

```bash
# Загрузка данных через curl
curl -H "Content-Type: application/json" -XPOST "http://localhost:9200/product/default/_bulk?pretty"
--data-binary "@product-bulk.json"
#где "@product-bulk.json" - файл в котором находяться данные в формате json

# проверить кластер
curl -X GET "http://192.168.132.102:9200/_cluster/health?pretty"

# Запрос для проверки анализатора, как он сохраняент введеный текст
curl -X GET "http://192.168.132.102:9200/_analyze?pretty" -H 'Content-Type: application/json' -d'
{
  "analyzer" : "russian",
  "text" : "Ремкоплект отладочный"
}'
```
---
