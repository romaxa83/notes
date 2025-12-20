#testing #utils #api

#### 🔹 Install

```bash
# for ubuntu
sudo snap install postman
```
---
#### 🔹 Скрипт для авторизации 

Записывает в перемнные окружения токены должна быть переменная `token`, `refresh_token`

```json
var jsonData = JSON.parse(responseBody).data;
if (jsonData.token_type && jsonData.access_token) {
    postman.setEnvironmentVariable("token", jsonData.token_type + " " + jsonData.access_token);
} else {
    postman.setEnvironmentVariable("token", "");
}
if (jsonData.refresh_token) {
    postman.setEnvironmentVariable("refresh_token", jsonData.refresh_token);
} else {
    postman.setEnvironmentVariable("refresh_token", "");
}
```
---
#### 🔹 Генерация документации

- пакет для генерации документации на основе коллекции из postman - https://github.com/thedevsaddam/docgen-bin

```bash
docgen build -i nmnd.postman_collection.json -o index.html
```
---