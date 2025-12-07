#database

### 🔹 Install

###### 🔸 Linux
```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927

sudo echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

sudo apt-get update
sudo apt-get install -y --allow-unauthenticated mongodb-org

sudo sh -c 'echo "[Unit]  
Description=High-performance, schema-free document-oriented database  
After=network.target  
  
[Service]  
User=mongodb  
ExecStart=/usr/bin/mongod --quiet --config /etc/mongod.conf  
  
[Install]  
WantedBy=multi-user.target" > /etc/systemd/system/mongodb.service'

sudo service mongodb start
sudo service mongodb status
sudo systemctl enable mongodb

# вход в shell
mongo

use admin
# добовляет пользователя,так можно меннять пароль
db.createUser({user:"admin", pwd:"admin123", roles:[{role:"root", db:"admin"}]})

sudo systemctl restart mongodb

mongo -u admin -p admin123 --authenticationDatabase admin
```

OR

```bash
sudo apt-get install mongodb
sudo apt-get update 
sudo service mongodb start
```
---
### 🔹 Cloud

Подключение к mongoDB через облако

1. переходим по url - https://www.mongodb.com/cloud/atlas, если не зарегистрирован, то регистрируемся
2. выбираем aws, затем выбираем регион (бесплатный) и нажимаем create cluster
3. после чего мы попадаем в dashboard atlas,где мы управляем нашей бд
4. создаем пользователя, и выбираем привилегии (оптимально - Read and write to any database)
5.  переходим на вкладку ip whitelist и добовляем ip который сможет работать с бд (выбираем add current ip address)
6. переходим во вкладку overview, нажимаем connect и выбираем connect your application копируем строку с подключением (будет такого формата - `mongodb+srv://romaxa:<password>@cluster0-vnd12.mongodb.net/test?retryWrites=true&w=majority`)

---
### 🔹 Console command

###### 🔸 INFO

```bash
# покажет все бд
show dbs

# перейдет в указаную бд
use dbName

# показывает в какой бд мы находимся
db

# покажет все коллекции в даной бд
show collections
```

###### 🔸 SELECT

```bash
# выведет все записи
db.nameCollection.find()

# выведет в удобном виде
db.nameCollection.find().pretty()	
```

###### 🔸 DELETE

```bash
# Удалить бд (в которой находимся)
db.dropDatabases()

# удалит всю коллекцию
db.collection.drop()

# удалит все записи соответствующие данному селектору
db.collection.remove(selector)
```

###### 🔸 Export\Import

```bash
# Export to json
mongoexport -d database_name - c collection_name -o outfile.json

# Export to file csv
mongoexport --csv -o /tmp/people.csv -d school -c people -f firstName,lastName,telephone,email

# Import from json file
mongoimport -d database_name -c collection_name outfile.json

# Import from csv file
# --headerline: Using the first row of data as the column name of the Collection.
mongoimport -d database_name -c collection_name --type csv --file locations.csv --headerline
```
 

---
### 🔹 Article
https://github.com/jsmarkus/the-little-mongodb-book/blob/master/ru/mongodb.markdown

//Админка для MongoDB
https://gist.github.com/tomasevich/ef5b0428716e249402488f3c92a12324
---