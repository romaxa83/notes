#websocket

**Centrifugo** - готовый Websocet сервер

- DOCS - https://centrifugal.github.io/centrifugo/
- пакет `nodejs` для работы на клиенте - https://www.npmjs.com/package/centrifuge
- клиент `php` - https://github.com/centrifugal/phpcent

#### 🔹 Config

```json
{
  "secret": "",
  "api_key": "",
  "namespaces": [
    {
      "name": "alerts",   //название канал
      "publish": false,   //запрет на запись из js
      "watch": true,      //дублировать сообщение в админку(для разработки)
      "anonymous": false,
      "presence": true,
      "join_leave": true,
      "recover": false,
      "history_size": 10,
      "history_lifetime": 30
    }
  ]
}
```
---