#auth #python #jwt #fast-api

для токенов будем использовать `JWT`

```bash
# install jwt package
pip install pyjwt
```

Создаем в корне проекта публичный и приватный ключи для шифрования (можно и в отдельной папке типа  `cert`)
```bash
# создаем приватный ключ (с именем - jwt-private.pem, можно задавать другое имя) с размером 2048
openssl genrsa -out jwt-private.pem 2048

# создаем публичный ключ на основе приватного ключа
openssl rsa -in jwt-private.pem -outform PEM -pubout -out jwt-public.pem
```

Для шифрования паролей будем использовать пакет `bcrypt`
```bash
pip install bcrypt
```


