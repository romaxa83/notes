#curl #terminal

стать по работе с curl в терминале
https://losst.ru/kak-polzovatsya-curl

```bash
# GET request

# простой запрос
curl http://127.0.0.1

# получить только http-загаловки
curl -I http://127.0.0.1

# выведет всю информацию по запросу
curl -v http://127.0.0.1
```

---
```bash
# POST request

# простой запрос
curl -X POST http://127.0.0.1


# с заголовками
curl -X POST --header 'Accept: application/json' http://127.0.0.1
# или так
curl -X POST -H 'Content-type: application/json' http://127.0.0.1

# с данными
curl -X POST -H 'Content-type: application/json' --data "{\"title\": \"some title\", \"author\": \"some author\", \"rating\": 5}" http://127.0.0.1

curl -X POST -H "Content-Type: application/json" -d '{"id": 33}' http://127.0.0.1:8000/hi-post
```
