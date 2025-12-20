#linux #server #tools

 Система для управления сервисами (works) в  Unix-подобных систем, состоит из :
- `supervisord` - сервер обрабатывающий запрос от клиента и  считывающий файлы настроек для сервисов
- `supervisorctl` - CLI(интерфейс) для управления  процессами под контролем `supervisord`, подключаясь к нему через сокет или TCP порт
---
- docks - http://supervisord.org/
- repo- https://github.com/Supervisor/supervisor
 - https://rtfm.co.ua/linux-supervisor-upravlenie-processami-i-servisami/
- https://www.8host.com/blog/ustanovka-i-upravlenie-supervisor-na-servere-ubuntu-i-debian/
- https://ruhighload.com/%D0%97%D0%B0%D0%BF%D1%83%D1%81%D0%BA+%D0%BF%D1%80%D0%BE%D1%86%D0%B5%D1%81%D1%81%D0%BE%D0%B2+%D0%B2+supervisor

---

```bash
# install ubuntu
sudo apt-get install supervisor -y
supervisord -v

# конфиги находяться 
/etc/supervisor

# свои процессы описываем в папке 
/etc/supervisor/conf.d
```
---