#linux #bash #scripts

```bash
#!/bin/bash

SCRIPT=`realpath -s $0`
SCRIPTPATH=`dirname $SCRIPT`

PATHDUMP="$SCRIPTPATH/dump.sql"
PATHDBDATA="$SCRIPTPATH/storage/db"

echo "Выгружаем dump.sql $PATHDUMP";
mysqldump -v -h 195.201.39.33 -u wezom_jddemodb -pIXoDqhTlbm wezom_jddemodb > $PATHDUMP

echo "Останавливаем контейнеры";
docker-compose down

echo "Удаляем данные из старой бд";
sudo rm -rf $PATHDBDATA

echo "Подымаем контейнеры";
docker-compose up -d

sleep 10s

echo "Накатываем dump в бд";
mysql -v -h 192.168.144.1 -u root -proot jd < $PATHDUMP

```