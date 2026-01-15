#python #package

Библиотека `requests` в Python предостав­ляет простой и удобный способ отправки НТТР-запросов и работы с веб­ ресурсами. Чаще всего она используется для отправки или принятия данных на сервер и с сервера.

```python
# GET
response = requests.get('https://example.com')
print(response.text)

headers = {'User-Agent': 'Mozilla/5.О'}  
params = {'key': 'value'}  
response = requests.get('https://example.com', headers=headers, params=params)

# POST
data = { 'key': 'value'}
response = requests.post('https://example.com/post', data=data)

```

==Обработка ошибок==
```python
try:
	response = requests.get('https://example.com') 
	response.raise_for_status() # Генерирует исключение, если ответ содержит ошибку

except requests.HTTPError as е:
	print(f'НТТР error occurred: {е}') 
except requests.RequestException as е:
	print(f'Request error occurred: {е}')
```

Библиотека `requests` поддерживает сессии, которые позволяют сохранять состояние между запросами.
```python
with requests.Session() as session: 
	session.auth = ('user', 'pass')
	response = session.get('https://example.com')
```
