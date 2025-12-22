#linux

#### 🔹 Подключение к другому компу

==получаем его ip== 
```bash
ping ip
```

==устанавливаем openssh-server==
```bash
#for ubuntu
sudo apt install openssh-server -y
service ssh status # проверяем статус
service ssh start # запускаем сервис
```

==подключение==
```bash
ssh user@ip
# где user - пользователь который есть на подключаемом компе
# ip - ip подключаемого компа
```

==после входа проверить что на другом компе==
```bash
uname -a	
```
---
#### 🔹 Если сбоит сеть

==определяем оборудывание==
```bash
lspci -knn | grep Net -A2
```
---
#### 🔹 My WIFI
tp-link Archer C20

вход http://tplinkwifi.net (or 192.168.0.1)
login:admin
password:RoMaXa1983_tp-link_admin

Облако
email - romaxa83@ukr.net
password:RoMaXa1983_tp-link_admin

сеть
romaxa83
RoMaXa1983_tp-link

---