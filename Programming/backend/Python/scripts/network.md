#### 🔹  TCP клиент и сервер
```python
import socket
import threading
import time

class TCPServer:
    """Простой TCP сервер"""
    
    def __init__(self, host='localhost', port=8888):
        self.host = host
        self.port = port
        self.socket = None
        self.running = False
        self.clients = []
    
    def start(self):
        """Запуск сервера"""
        try:
            # Создаем сокет
            self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            
            # Позволяем переиспользовать адрес
            self.socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
            
            # Привязываем к адресу
            self.socket.bind((self.host, self.port))
            
            # Начинаем слушать (максимум 5 подключений в очереди)
            self.socket.listen(5)
            
            self.running = True
            print(f"TCP сервер запущен на {self.host}:{self.port}")
            
            while self.running:
                try:
                    # Принимаем подключение
                    client_socket, client_address = self.socket.accept()
                    print(f"Подключен клиент: {client_address}")
                    
                    # Запускаем обработку клиента в отдельном потоке
                    client_thread = threading.Thread(
                        target=self.handle_client,
                        args=(client_socket, client_address)
                    )
                    client_thread.daemon = True
                    client_thread.start()
                    
                except socket.error:
                    if self.running:
                        print("Ошибка принятия подключения")
                    break
                    
        except Exception as e:
            print(f"Ошибка запуска сервера: {e}")
        finally:
            self.stop()
    
    def handle_client(self, client_socket, client_address):
        """Обработка клиента"""
        try:
            self.clients.append(client_socket)
            
            # Отправляем приветствие
            welcome_msg = f"Добро пожаловать на сервер! Ваш адрес: {client_address}\n"
            client_socket.send(welcome_msg.encode('utf-8'))
            
            while self.running:
                # Получаем данные от клиента
                data = client_socket.recv(1024)
                
                if not data:
                    break
                
                message = data.decode('utf-8').strip()
                print(f"Получено от {client_address}: {message}")
                
                # Эхо-ответ
                if message.lower() == 'quit':
                    break
                elif message.lower() == 'time':
                    response = f"Текущее время: {time.strftime('%Y-%m-%d %H:%M:%S')}\n"
                elif message.lower() == 'clients':
                    response = f"Подключенных клиентов: {len(self.clients)}\n"
                else:
                    response = f"Эхо: {message}\n"
                
                client_socket.send(response.encode('utf-8'))
                
        except Exception as e:
            print(f"Ошибка обработки клиента {client_address}: {e}")
        finally:
            if client_socket in self.clients:
                self.clients.remove(client_socket)
            client_socket.close()
            print(f"Клиент {client_address} отключен")
    
    def stop(self):
        """Остановка сервера"""
        self.running = False
        
        # Закрываем клиентские подключения
        for client in self.clients[:]:
            try:
                client.close()
            except:
                pass
        
        # Закрываем серверный сокет
        if self.socket:
            self.socket.close()
        
        print("Сервер остановлен")

class TCPClient:
    """Простой TCP клиент"""
    
    def __init__(self, host='localhost', port=8888):
        self.host = host
        self.port = port
        self.socket = None
    
    def connect(self):
        """Подключение к серверу"""
        try:
            self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            
            # Устанавливаем таймаут
            self.socket.settimeout(10)
            
            # Подключаемся
            self.socket.connect((self.host, self.port))
            print(f"Подключен к серверу {self.host}:{self.port}")
            
            return True
            
        except socket.timeout:
            print("Таймаут подключения")
            return False
        except socket.error as e:
            print(f"Ошибка подключения: {e}")
            return False
    
    def send_message(self, message):
        """Отправка сообщения"""
        try:
            self.socket.send(message.encode('utf-8'))
            
            # Получаем ответ
            response = self.socket.recv(1024)
            return response.decode('utf-8')
            
        except Exception as e:
            print(f"Ошибка отправки сообщения: {e}")
            return None
    
    def close(self):
        """Закрытие соединения"""
        if self.socket:
            self.socket.close()
            print("Соединение закрыто")

def tcp_demo():
    """Демонстрация TCP клиента и сервера"""
    
    print("=== TCP клиент-сервер ===")
    
    # Запускаем сервер в отдельном потоке
    server = TCPServer('localhost', 8888)
    server_thread = threading.Thread(target=server.start)
    server_thread.daemon = True
    server_thread.start()
    
    # Даем серверу время запуститься
    time.sleep(1)
    
    # Создаем и тестируем клиента
    client = TCPClient('localhost', 8888)
    
    if client.connect():
        # Получаем приветствие
        welcome = client.socket.recv(1024)
        print(f"Сервер: {welcome.decode('utf-8').strip()}")
        
        # Отправляем несколько сообщений
        test_messages = ['Привет, сервер!', 'time', 'clients', 'Тестовое сообщение']
        
        for msg in test_messages:
            print(f"Отправляем: {msg}")
            response = client.send_message(msg)
            if response:
                print(f"Ответ: {response.strip()}")
            time.sleep(0.5)
        
        # Закрываем соединение
        client.send_message('quit')
        client.close()
    
    # Останавливаем сервер
    time.sleep(1)
    server.stop()

tcp_demo()
```

#### 🔹 UDP клиент и сервер
```python
import socket
import threading
import time

class UDPServer:
    """UDP сервер"""
    
    def __init__(self, host='localhost', port=8889):
        self.host = host
        self.port = port
        self.socket = None
        self.running = False
        self.clients = set()
    
    def start(self):
        """Запуск UDP сервера"""
        try:
            self.socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
            self.socket.bind((self.host, self.port))
            
            self.running = True
            print(f"UDP сервер запущен на {self.host}:{self.port}")
            
            while self.running:
                try:
                    # Получаем данные (UDP не требует accept)
                    data, client_address = self.socket.recvfrom(1024)
                    
                    self.clients.add(client_address)
                    message = data.decode('utf-8').strip()
                    
                    print(f"UDP от {client_address}: {message}")
                    
                    # Формируем ответ
                    if message.lower() == 'time':
                        response = f"Время: {time.strftime('%H:%M:%S')}"
                    elif message.lower() == 'clients':
                        response = f"Клиентов: {len(self.clients)}"
                    elif message.lower() == 'ping':
                        response = "pong"
                    else:
                        response = f"UDP эхо: {message}"
                    
                    # Отправляем ответ
                    self.socket.sendto(response.encode('utf-8'), client_address)
                    
                except socket.error:
                    if self.running:
                        print("Ошибка получения UDP данных")
                    break
                    
        except Exception as e:
            print(f"Ошибка UDP сервера: {e}")
        finally:
            self.stop()
    
    def stop(self):
        """Остановка сервера"""
        self.running = False
        if self.socket:
            self.socket.close()
        print("UDP сервер остановлен")

class UDPClient:
    """UDP клиент"""
    
    def __init__(self, host='localhost', port=8889):
        self.host = host
        self.port = port
        self.socket = None
    
    def connect(self):
        """Подготовка UDP клиента"""
        try:
            self.socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
            self.socket.settimeout(5)  # Таймаут для recv
            return True
        except Exception as e:
            print(f"Ошибка создания UDP сокета: {e}")
            return False
    
    def send_message(self, message):
        """Отправка UDP сообщения"""
        try:
            # Отправляем данные
            self.socket.sendto(message.encode('utf-8'), (self.host, self.port))
            
            # Получаем ответ
            data, server_address = self.socket.recvfrom(1024)
            return data.decode('utf-8')
            
        except socket.timeout:
            print("Таймаут получения ответа")
            return None
        except Exception as e:
            print(f"Ошибка UDP отправки: {e}")
            return None
    
    def close(self):
        """Закрытие UDP сокета"""
        if self.socket:
            self.socket.close()

def udp_demo():
    """Демонстрация UDP клиента и сервера"""
    
    print("\n=== UDP клиент-сервер ===")
    
    # Запускаем UDP сервер
    server = UDPServer('localhost', 8889)
    server_thread = threading.Thread(target=server.start)
    server_thread.daemon = True
    server_thread.start()
    
    time.sleep(1)
    
    # Тестируем UDP клиента
    client = UDPClient('localhost', 8889)
    
    if client.connect():
        test_messages = ['ping', 'Привет UDP!', 'time', 'clients']
        
        for msg in test_messages:
            print(f"UDP отправка: {msg}")
            response = client.send_message(msg)
            if response:
                print(f"UDP ответ: {response}")
            time.sleep(0.5)
        
        client.close()
    
    # Останавливаем сервер
    time.sleep(1)
    server.stop()

udp_demo()
```

#### 🔹 Работа с HTTP через сокеты
```python
import socket
import urllib.parse

class SimpleHTTPClient:
    """Простой HTTP клиент на сокетах"""
    
    def __init__(self):
        self.socket = None
    
    def get(self, url, headers=None):
        """HTTP GET запрос"""
        try:
            # Парсим URL
            parsed_url = urllib.parse.urlparse(url)
            host = parsed_url.netloc
            path = parsed_url.path or '/'
            port = parsed_url.port or (443 if parsed_url.scheme == 'https' else 80)
            
            # Для HTTPS нужен SSL, пока поддерживаем только HTTP
            if parsed_url.scheme == 'https':
                print("HTTPS не поддерживается в этом примере")
                return None
            
            # Создаем сокет
            self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            self.socket.settimeout(10)
            
            # Подключаемся
            self.socket.connect((host, port))
            
            # Формируем HTTP запрос
            request_lines = [
                f'GET {path} HTTP/1.1',
                f'Host: {host}',
                'Connection: close',
                'User-Agent: Python-Socket-Client/1.0'
            ]
            
            # Добавляем дополнительные заголовки
            if headers:
                for key, value in headers.items():
                    request_lines.append(f'{key}: {value}')
            
            request_lines.append('')  # Пустая строка для завершения заголовков
            request_lines.append('')  # Конец запроса
            
            request = '\r\n'.join(request_lines)
            
            # Отправляем запрос
            self.socket.send(request.encode('utf-8'))
            
            # Получаем ответ
            response = b''
            while True:
                chunk = self.socket.recv(4096)
                if not chunk:
                    break
                response += chunk
            
            return self.parse_response(response.decode('utf-8', errors='ignore'))
            
        except Exception as e:
            print(f"Ошибка HTTP запроса: {e}")
            return None
        finally:
            if self.socket:
                self.socket.close()
    
    def parse_response(self, response_text):
        """Парсинг HTTP ответа"""
        lines = response_text.split('\r\n')
        
        # Статусная строка
        status_line = lines[0]
        status_parts = status_line.split(' ', 2)
        
        if len(status_parts) >= 2:
            status_code = int(status_parts[1])
            status_message = status_parts[2] if len(status_parts) > 2 else ''
        else:
            status_code = 0
            status_message = 'Unknown'
        
        # Заголовки
        headers = {}
        body_start = 1
        
        for i, line in enumerate(lines[1:], 1):
            if line == '':  # Пустая строка - конец заголовков
                body_start = i + 1
                break
            
            if ':' in line:
                key, value = line.split(':', 1)
                headers[key.strip().lower()] = value.strip()
        
        # Тело ответа
        body = '\r\n'.join(lines[body_start:])
        
        return {
            'status_code': status_code,
            'status_message': status_message,
            'headers': headers,
            'body': body
        }

def http_demo():
    """Демонстрация HTTP клиента"""
    
    print("\n=== HTTP через сокеты ===")
    
    client = SimpleHTTPClient()
    
    # Тестируем GET запрос
    print("Выполнение HTTP GET запроса...")
    
    # Используем httpbin.org для тестирования
    response = client.get('http://httpbin.org/get')
    
    if response:
        print(f"Статус: {response['status_code']} {response['status_message']}")
        print("Заголовки:")
        for key, value in response['headers'].items():
            print(f"  {key}: {value}")
        print(f"Размер тела ответа: {len(response['body'])} байт")
        print(f"Начало тела ответа: {response['body'][:200]}...")
    else:
        print("Ошибка выполнения запроса")

http_demo()
```

#### 🔹 Многопоточный сервер
```python
import socket
import threading
import time
import json
from datetime import datetime

class ThreadedTCPServer:
    """Многопоточный TCP сервер"""
    
    def __init__(self, host='localhost', port=8890, max_connections=10):
        self.host = host
        self.port = port
        self.max_connections = max_connections
        self.socket = None
        self.running = False
        self.clients = {}
        self.client_lock = threading.Lock()
        self.stats = {
            'connections_total': 0,
            'connections_active': 0,
            'messages_received': 0,
            'start_time': None
        }
    
    def start(self):
        """Запуск сервера"""
        try:
            self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            self.socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
            self.socket.bind((self.host, self.port))
            self.socket.listen(self.max_connections)
            
            self.running = True
            self.stats['start_time'] = datetime.now()
            
            print(f"Многопоточный сервер запущен на {self.host}:{self.port}")
            print(f"Максимум подключений: {self.max_connections}")
            
            while self.running:
                try:
                    client_socket, client_address = self.socket.accept()
                    
                    with self.client_lock:
                        if len(self.clients) >= self.max_connections:
                            # Отклоняем подключение
                            client_socket.send(b"Server full. Try again later.\n")
                            client_socket.close()
                            continue
                        
                        # Добавляем клиента
                        client_id = f"{client_address[0]}:{client_address[1]}_{time.time()}"
                        self.clients[client_id] = {
                            'socket': client_socket,
                            'address': client_address,
                            'connected_at': datetime.now(),
                            'messages_count': 0
                        }
                        
                        self.stats['connections_total'] += 1
                        self.stats['connections_active'] += 1
                    
                    print(f"Новый клиент: {client_address} (ID: {client_id})")
                    
                    # Запускаем поток для обработки клиента
                    client_thread = threading.Thread(
                        target=self.handle_client,
                        args=(client_id,),
                        name=f"Client-{client_id}"
                    )
                    client_thread.daemon = True
                    client_thread.start()
                    
                except socket.error:
                    if self.running:
                        print("Ошибка принятия подключения")
                    break
        
        except Exception as e:
            print(f"Ошибка запуска сервера: {e}")
        finally:
            self.stop()
    
    def handle_client(self, client_id):
        """Обработка клиента"""
        client_info = self.clients[client_id]
        client_socket = client_info['socket']
        client_address = client_info['address']
        
        try:
            # Приветственное сообщение
            welcome = {
                'type': 'welcome',
                'message': f'Добро пожаловать, {client_address}!',
                'client_id': client_id,
                'server_time': datetime.now().isoformat()
            }
            
            self.send_json(client_socket, welcome)
            
            while self.running:
                try:
                    # Получаем данные
                    data = client_socket.recv(1024)
                    if not data:
                        break
                    
                    message_text = data.decode('utf-8').strip()
                    if not message_text:
                        continue
                    
                    with self.client_lock:
                        client_info['messages_count'] += 1
                        self.stats['messages_received'] += 1
                    
                    print(f"[{client_id}] Получено: {message_text}")
                    
                    # Обрабатываем команды
                    response = self.process_command(client_id, message_text)
                    
                    if response.get('disconnect'):
                        break
                    
                    self.send_json(client_socket, response)
                    
                except socket.timeout:
                    continue
                except socket.error:
                    break
        
        except Exception as e:
            print(f"Ошибка обработки клиента {client_id}: {e}")
        finally:
            self.disconnect_client(client_id)
    
    def process_command(self, client_id, message):
        """Обработка команд от клиента"""
        
        if message.lower() == 'stats':
            uptime = datetime.now() - self.stats['start_time']
            return {
                'type': 'stats',
                'data': {
                    'connections_total': self.stats['connections_total'],
                    'connections_active': self.stats['connections_active'],
                    'messages_received': self.stats['messages_received'],
                    'uptime_seconds': uptime.total_seconds(),
                    'current_time': datetime.now().isoformat()
                }
            }
        
        elif message.lower() == 'clients':
            with self.client_lock:
                clients_info = []
                for cid, info in self.clients.items():
                    clients_info.append({
                        'id': cid,
                        'address': f"{info['address'][0]}:{info['address'][1]}",
                        'connected_at': info['connected_at'].isoformat(),
                        'messages_count': info['messages_count']
                    })
            
            return {
                'type': 'clients_list',
                'data': clients_info
            }
        
        elif message.lower() == 'time':
            return {
                'type': 'time',
                'data': datetime.now().isoformat()
            }
        
        elif message.lower() in ['quit', 'exit', 'bye']:
            return {
                'type': 'goodbye',
                'message': 'До свидания!',
                'disconnect': True
            }
        
        else:
            return {
                'type': 'echo',
                'original_message': message,
                'response': f"Эхо: {message}",
                'timestamp': datetime.now().isoformat()
            }
    
    def send_json(self, client_socket, data):
        """Отправка JSON данных"""
        try:
            json_data = json.dumps(data, ensure_ascii=False, indent=2)
            client_socket.send((json_data + '\n').encode('utf-8'))
        except Exception as e:
            print(f"Ошибка отправки JSON: {e}")
    
    def disconnect_client(self, client_id):
        """Отключение клиента"""
        with self.client_lock:
            if client_id in self.clients:
                client_info = self.clients[client_id]
                try:
                    client_info['socket'].close()
                except:
                    pass
                
                del self.clients[client_id]
                self.stats['connections_active'] -= 1
                
                print(f"Клиент отключен: {client_id}")
    
    def stop(self):
        """Остановка сервера"""
        print("Останавливаем сервер...")
        self.running = False
        
        # Закрываем все клиентские подключения
        with self.client_lock:
            for client_id in list(self.clients.keys()):
                self.disconnect_client(client_id)
        
        # Закрываем серверный сокет
        if self.socket:
            self.socket.close()
        
        print("Сервер остановлен")
    
    def get_status(self):
        """Получение статуса сервера"""
        with self.client_lock:
            return {
                'running': self.running,
                'active_connections': len(self.clients),
                'total_connections': self.stats['connections_total'],
                'messages_received': self.stats['messages_received']
            }

def threaded_server_demo():
    """Демонстрация многопоточного сервера"""
    
    print("\n=== Многопоточный сервер ===")
    
    server = ThreadedTCPServer('localhost', 8890, max_connections=5)
    server_thread = threading.Thread(target=server.start)
    server_thread.daemon = True
    server_thread.start()
    
    time.sleep(1)
    
    # Создаем несколько клиентов для тестирования
    clients = []
    
    def create_test_client(client_num):
        """Создание тестового клиента"""
        try:
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.connect(('localhost', 8890))
            
            # Получаем приветствие
            welcome = sock.recv(1024)
            print(f"Клиент {client_num} получил приветствие")
            
            # Отправляем несколько команд
            commands = ['time', 'stats', f'Привет от клиента {client_num}', 'clients']
            
            for cmd in commands:
                sock.send(cmd.encode('utf-8'))
                response = sock.recv(2048)
                print(f"Клиент {client_num}: {cmd} -> получен ответ")
                time.sleep(0.5)
            
            # Завершаем
            sock.send(b'quit')
            sock.recv(1024)  # Получаем прощание
            sock.close()
            print(f"Клиент {client_num} завершил работу")
            
        except Exception as e:
            print(f"Ошибка клиента {client_num}: {e}")
    
    # Запускаем несколько клиентов параллельно
    client_threads = []
    
    for i in range(3):
        thread = threading.Thread(target=create_test_client, args=(i+1,))
        thread.start()
        client_threads.append(thread)
    
    # Ждем завершения всех клиентов
    for thread in client_threads:
        thread.join()
    
    # Показываем финальную статистику
    time.sleep(1)
    final_stats = server.get_status()
    print(f"\nФинальная статистика:")
    print(f"  Всего подключений: {final_stats['total_connections']}")
    print(f"  Активных подключений: {final_stats['active_connections']}")
    print(f"  Сообщений обработано: {final_stats['messages_received']}")
    
    server.stop()

threaded_server_demo()
```

#### 🔹 Асинхронный сервер с select
```python
import socket
import select
import time

class AsyncTCPServer:
    """Асинхронный TCP сервер с использованием select"""
    
    def __init__(self, host='localhost', port=8891):
        self.host = host
        self.port = port
        self.server_socket = None
        self.running = False
        self.sockets = []  # Список всех сокетов для мониторинга
        self.clients = {}  # Информация о клиентах
    
    def start(self):
        """Запуск асинхронного сервера"""
        try:
            # Создаем серверный сокет
            self.server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            self.server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
            self.server_socket.bind((self.host, self.port))
            self.server_socket.listen(5)
            
            # Делаем сокет неблокирующим
            self.server_socket.setblocking(False)
            
            # Добавляем серверный сокет в список для мониторинга
            self.sockets = [self.server_socket]
            self.running = True
            
            print(f"Асинхронный сервер запущен на {self.host}:{self.port}")
            
            while self.running:
                # Используем select для мониторинга сокетов
                # Таймаут 1 секунда для возможности остановки
                readable, writable, exceptional = select.select(
                    self.sockets, [], self.sockets, 1.0
                )
                
                # Обрабатываем readable сокеты
                for sock in readable:
                    if sock is self.server_socket:
                        # Новое подключение
                        self.accept_connection()
                    else:
                        # Данные от существующего клиента
                        self.handle_client_data(sock)
                
                # Обрабатываем exceptional сокеты (ошибки)
                for sock in exceptional:
                    self.remove_client(sock)
        
        except Exception as e:
            print(f"Ошибка асинхронного сервера: {e}")
        finally:
            self.stop()
    
    def accept_connection(self):
        """Принятие нового подключения"""
        try:
            client_socket, client_address = self.server_socket.accept()
            client_socket.setblocking(False)
            
            # Добавляем в списки мониторинга
            self.sockets.append(client_socket)
            self.clients[client_socket] = {
                'address': client_address,
                'connected_at': time.time(),
                'messages_count': 0
            }
            
            print(f"Принято подключение от {client_address}")
            
            # Отправляем приветствие
            welcome_msg = f"Добро пожаловать на асинхронный сервер! {client_address}\n"
            client_socket.send(welcome_msg.encode('utf-8'))
            
        except socket.error as e:
            print(f"Ошибка принятия подключения: {e}")
    
    def handle_client_data(self, client_socket):
        """Обработка данных от клиента"""
        try:
            data = client_socket.recv(1024)
            
            if not data:
                # Клиент закрыл соединение
                self.remove_client(client_socket)
                return
            
            message = data.decode('utf-8').strip()
            client_info = self.clients[client_socket]
            client_info['messages_count'] += 1
            
            print(f"[{client_info['address']}] Получено: {message}")
            
            # Обрабатываем команды
            if message.lower() == 'quit':
                self.remove_client(client_socket)
                return
            elif message.lower() == 'time':
                response = f"Время: {time.strftime('%H:%M:%S')}\n"
            elif message.lower() == 'clients':
                response = f"Подключено клиентов: {len(self.clients)}\n"
            elif message.lower() == 'stats':
                uptime = time.time() - client_info['connected_at']
                response = f"Статистика: сообщений={client_info['messages_count']}, время подключения={uptime:.1f}с\n"
            else:
                response = f"Асинхронное эхо: {message}\n"
            
            # Отправляем ответ
            client_socket.send(response.encode('utf-8'))
            
        except socket.error as e:
            print(f"Ошибка обработки данных клиента: {e}")
            self.remove_client(client_socket)
    
    def remove_client(self, client_socket):
        """Удаление клиента"""
        if client_socket in self.clients:
            client_info = self.clients[client_socket]
            print(f"Отключение клиента {client_info['address']}")
            
            # Удаляем из всех списков
            if client_socket in self.sockets:
                self.sockets.remove(client_socket)
            
            del self.clients[client_socket]
            
            try:
                client_socket.close()
            except:
                pass
    
    def stop(self):
        """Остановка сервера"""
        self.running = False
        
        # Закрываем все клиентские соединения
        for client_socket in list(self.clients.keys()):
            self.remove_client(client_socket)
        
        # Закрываем серверный сокет
        if self.server_socket:
            self.server_socket.close()
        
        print("Асинхронный сервер остановлен")

def async_server_demo():
    """Демонстрация асинхронного сервера"""
    
    print("\n=== Асинхронный сервер (select) ===")
    
    import threading
    
    server = AsyncTCPServer('localhost', 8891)
    server_thread = threading.Thread(target=server.start)
    server_thread.daemon = True
    server_thread.start()
    
    time.sleep(1)
    
    # Тестируем с несколькими клиентами
    def test_async_client(client_id):
        """Тестовый клиент для асинхронного сервера"""
        try:
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.connect(('localhost', 8891))
            
            # Получаем приветствие
            welcome = sock.recv(1024)
            print(f"Клиент {client_id}: {welcome.decode('utf-8').strip()}")
            
            # Отправляем команды
            commands = ['time', 'clients', f'Сообщение от клиента {client_id}', 'stats']
            
            for cmd in commands:
                sock.send(cmd.encode('utf-8'))
                response = sock.recv(1024)
                print(f"Клиент {client_id}: {cmd} -> {response.decode('utf-8').strip()}")
                time.sleep(0.3)
            
            # Завершаем
            sock.send(b'quit')
            sock.close()
            
        except Exception as e:
            print(f"Ошибка async клиента {client_id}: {e}")
    
    # Запускаем нескольких клиентов
    threads = []
    for i in range(3):
        thread = threading.Thread(target=test_async_client, args=(i+1,))
        thread.start()
        threads.append(thread)
    
    # Ждем завершения клиентов
    for thread in threads:
        thread.join()
    
    time.sleep(1)
    server.stop()

async_server_demo()
```

#### 🔹 Безопасность и лучшие практики
```python
import socket
import ssl
import hashlib
import threading
import time

class SecureTCPServer:
    """Безопасный TCP сервер с базовой аутентификацией"""
    
    def __init__(self, host='localhost', port=8892):
        self.host = host
        self.port = port
        self.socket = None
        self.running = False
        
        # Простая база пользователей (в реальности используйте БД)
        self.users = {
            'admin': self.hash_password('password123'),
            'user1': self.hash_password('secret456'),
            'guest': self.hash_password('guest789')
        }
        
        self.authenticated_clients = set()
        self.failed_attempts = {}  # IP -> количество неудачных попыток
        self.banned_ips = set()
    
    def hash_password(self, password):
        """Хеширование пароля"""
        return hashlib.sha256(password.encode('utf-8')).hexdigest()
    
    def is_ip_banned(self, ip):
        """Проверка, заблокирован ли IP"""
        return ip in self.banned_ips
    
    def register_failed_attempt(self, ip):
        """Регистрация неудачной попытки входа"""
        if ip not in self.failed_attempts:
            self.failed_attempts[ip] = 0
        
        self.failed_attempts[ip] += 1
        
        # Блокируем после 3 неудачных попыток
        if self.failed_attempts[ip] >= 3:
            self.banned_ips.add(ip)
            print(f"IP {ip} заблокирован за превышение лимита попыток")
    
    def authenticate_user(self, username, password):
        """Аутентификация пользователя"""
        if username not in self.users:
            return False
        
        password_hash = self.hash_password(password)
        return self.users[username] == password_hash
    
    def start(self):
        """Запуск безопасного сервера"""
        try:
            self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            self.socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
            
            # Настройки безопасности
            self.socket.settimeout(30)  # Таймаут для accept
            
            self.socket.bind((self.host, self.port))
            self.socket.listen(5)
            
            self.running = True
            print(f"Безопасный сервер запущен на {self.host}:{self.port}")
            
            while self.running:
                try:
                    client_socket, client_address = self.socket.accept()
                    client_ip = client_address[0]
                    
                    # Проверяем, не заблокирован ли IP
                    if self.is_ip_banned(client_ip):
                        client_socket.send(b"Access denied. IP blocked.\n")
                        client_socket.close()
                        continue
                    
                    # Обрабатываем клиента в отдельном потоке
                    client_thread = threading.Thread(
                        target=self.handle_secure_client,
                        args=(client_socket, client_address)
                    )
                    client_thread.daemon = True
                    client_thread.start()
                    
                except socket.timeout:
                    continue
                except socket.error:
                    if self.running:
                        print("Ошибка принятия подключения")
                    break
        
        except Exception as e:
            print(f"Ошибка запуска безопасного сервера: {e}")
        finally:
            self.stop()
    
    def handle_secure_client(self, client_socket, client_address):
        """Безопасная обработка клиента"""
        client_ip = client_address[0]
        authenticated = False
        
        try:
            # Устанавливаем таймаут для клиентского сокета
            client_socket.settimeout(60)
            
            # Запрашиваем аутентификацию
            client_socket.send(b"=== SECURE SERVER ===\n")
            client_socket.send(b"Username: ")
            
            username_data = client_socket.recv(1024)
            if not username_data:
                return
            
            username = username_data.decode('utf-8').strip()
            
            client_socket.send(b"Password: ")
            password_data = client_socket.recv(1024)
            if not password_data:
                return
            
            password = password_data.decode('utf-8').strip()
            
            # Проверяем аутентификацию
            if self.authenticate_user(username, password):
                authenticated = True
                self.authenticated_clients.add(client_socket)
                
                # Очищаем счетчик неудачных попыток
                if client_ip in self.failed_attempts:
                    del self.failed_attempts[client_ip]
                
                welcome_msg = f"Добро пожаловать, {username}! Вы успешно аутентифицированы.\n"
                client_socket.send(welcome_msg.encode('utf-8'))
                
                print(f"Успешная аутентификация: {username} от {client_address}")
                
                # Обрабатываем команды аутентифицированного пользователя
                self.handle_authenticated_session(client_socket, client_address, username)
            
            else:
                # Неудачная попытка
                self.register_failed_attempt(client_ip)
                client_socket.send(b"Authentication failed!\n")
                print(f"Неудачная попытка входа от {client_address}")
        
        except socket.timeout:
            client_socket.send(b"Session timeout\n")
        except Exception as e:
            print(f"Ошибка обработки безопасного клиента {client_address}: {e}")
        finally:
            if authenticated and client_socket in self.authenticated_clients:
                self.authenticated_clients.remove(client_socket)
            
            try:
                client_socket.close()
            except:
                pass
    
    def handle_authenticated_session(self, client_socket, client_address, username):
        """Обработка сессии аутентифицированного пользователя"""
        
        client_socket.send(b"Введите команды (help для справки):\n")
        
        while self.running:
            try:
                client_socket.send(b"> ")
                data = client_socket.recv(1024)
                
                if not data:
                    break
                
                command = data.decode('utf-8').strip().lower()
                
                if command == 'quit' or command == 'exit':
                    client_socket.send(b"Goodbye!\n")
                    break
                
                elif command == 'help':
                    help_text = """
Доступные команды:
- help: показать эту справку
- whoami: показать текущего пользователя
- time: показать время сервера
- stats: статистика сервера
- users: список активных пользователей
- quit/exit: выход
"""
                    client_socket.send(help_text.encode('utf-8'))
                
                elif command == 'whoami':
                    response = f"Вы вошли как: {username}\n"
                    client_socket.send(response.encode('utf-8'))
                
                elif command == 'time':
                    response = f"Время сервера: {time.strftime('%Y-%m-%d %H:%M:%S')}\n"
                    client_socket.send(response.encode('utf-8'))
                
                elif command == 'stats':
                    stats = f"""
Статистика сервера:
- Активных аутентифицированных клиентов: {len(self.authenticated_clients)}
- Заблокированных IP: {len(self.banned_ips)}
- Неудачных попыток входа: {sum(self.failed_attempts.values())}
"""
                    client_socket.send(stats.encode('utf-8'))
                
                elif command == 'users':
                    response = f"Зарегистрированных пользователей: {len(self.users)}\n"
                    response += f"Активных сессий: {len(self.authenticated_clients)}\n"
                    client_socket.send(response.encode('utf-8'))
                
                else:
                    response = f"Неизвестная команда: {command}\nВведите 'help' для справки.\n"
                    client_socket.send(response.encode('utf-8'))
            
            except socket.timeout:
                client_socket.send(b"Session timeout due to inactivity\n")
                break
            except socket.error:
                break
    
    def stop(self):
        """Остановка сервера"""
        self.running = False
        
        # Закрываем аутентифицированные соединения
        for client_socket in list(self.authenticated_clients):
            try:
                client_socket.send(b"Server shutting down...\n")
                client_socket.close()
            except:
                pass
        
        if self.socket:
            self.socket.close()
        
        print("Безопасный сервер остановлен")

def secure_server_demo():
    """Демонстрация безопасного сервера"""
    
    print("\n=== Безопасный сервер ===")
    
    server = SecureTCPServer('localhost', 8892)
    server_thread = threading.Thread(target=server.start)
    server_thread.daemon = True
    server_thread.start()
    
    time.sleep(1)
    
    def test_secure_client(username, password, client_id):
        """Тестовый клиент для безопасного сервера"""
        try:
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.connect(('localhost', 8892))
            
            # Получаем приветствие
            welcome = sock.recv(1024)
            print(f"Клиент {client_id}: {welcome.decode('utf-8').strip()}")
            
            # Отправляем имя пользователя
            username_prompt = sock.recv(1024)
            sock.send(username.encode('utf-8'))
            
            # Отправляем пароль
            password_prompt = sock.recv(1024)
            sock.send(password.encode('utf-8'))
            
            # Получаем результат аутентификации
            auth_result = sock.recv(1024)
            result_text = auth_result.decode('utf-8').strip()
            print(f"Клиент {client_id}: {result_text}")
            
            if "успешно" in result_text:
                # Аутентификация успешна, тестируем команды
                commands = ['help', 'whoami', 'time', 'stats', 'quit']
                
                for cmd in commands:
                    # Получаем приглашение
                    prompt = sock.recv(1024)
                    
                    # Отправляем команду
                    sock.send(cmd.encode('utf-8'))
                    
                    # Получаем ответ
                    response = sock.recv(2048)
                    print(f"Клиент {client_id}: {cmd} -> получен ответ")
                    
                    if cmd == 'quit':
                        break
            
            sock.close()
            
        except Exception as e:
            print(f"Ошибка secure клиента {client_id}: {e}")
    
    # Тестируем с разными учетными данными
    print("Тестирование аутентификации...")
    
    # Успешная аутентификация
    thread1 = threading.Thread(
        target=test_secure_client, 
        args=('admin', 'password123', 1)
    )
    thread1.start()
    
    # Неудачная аутентификация
    thread2 = threading.Thread(
        target=test_secure_client, 
        args=('admin', 'wrongpassword', 2)
    )
    thread2.start()
    
    # Успешная аутентификация другого пользователя
    thread3 = threading.Thread(
        target=test_secure_client, 
        args=('user1', 'secret456', 3)
    )
    thread3.start()
    
    # Ждем завершения тестов
    thread1.join()
    thread2.join()
    thread3.join()
    
    time.sleep(1)
    server.stop()

secure_server_demo()
```

#### 🔹 Лучшие практики и советы
```python
import socket
import errno
import time

def socket_best_practices():
    """Лучшие практики работы с сокетами"""
    
    print("\n=== Лучшие практики ===")
    
    print("1. ✅ Правильная обработка ошибок:")
    
    def safe_socket_operation():
        """Безопасная операция с сокетом"""
        sock = None
        try:
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.settimeout(5)  # Таймаут 5 секунд
            
            # Попытка подключения к несуществующему серверу
            sock.connect(('192.0.2.1', 12345))  # RFC 5737 test address
            
        except socket.timeout:
            print("   Таймаут подключения")
        except socket.gaierror as e:
            print(f"   Ошибка разрешения имени: {e}")
        except ConnectionRefusedError:
            print("   Подключение отклонено")
        except socket.error as e:
            print(f"   Общая ошибка сокета: {e}")
        finally:
            if sock:
                sock.close()
    
    safe_socket_operation()
    
    print("\n2. ✅ Использование контекстных менеджеров:")
    
    class SocketContextManager:
        """Контекстный менеджер для сокетов"""
        
        def __init__(self, family=socket.AF_INET, type=socket.SOCK_STREAM):
            self.family = family
            self.type = type
            self.socket = None
        
        def __enter__(self):
            self.socket = socket.socket(self.family, self.type)
            return self.socket
        
        def __exit__(self, exc_type, exc_val, exc_tb):
            if self.socket:
                self.socket.close()
    
    # Использование
    try:
        with SocketContextManager() as sock:
            sock.settimeout(2)
            # sock автоматически закроется
            print("   Сокет будет автоматически закрыт")
    except Exception as e:
        print(f"   Ошибка: {e}")
    
    print("\n3. ✅ Правильная настройка сокетов:")
    
    def configure_socket_properly():
        """Правильная настройка сокета"""
        with SocketContextManager() as sock:
            # Переиспользование адреса
            sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
            
            # Отключение алгоритма Nagle для низких задержек
            sock.setsockopt(socket.IPPROTO_TCP, socket.TCP_NODELAY, 1)
            
            # Размер буферов
            sock.setsockopt(socket.SOL_SOCKET, socket.SO_RCVBUF, 65536)
            sock.setsockopt(socket.SOL_SOCKET, socket.SO_SNDBUF, 65536)
            
            # Таймауты
            sock.settimeout(10)
            
            print("   Сокет правильно настроен")
    
    configure_socket_properly()
    
    print("\n4. ✅ Обработка частичной отправки/получения:")
    
    def send_all(sock, data):
        """Отправка всех данных"""
        bytes_sent = 0
        while bytes_sent < len(data):
            try:
                sent = sock.send(data[bytes_sent:])
                if sent == 0:
                    raise RuntimeError("Сокет закрыт")
                bytes_sent += sent
            except socket.error as e:
                if e.errno == errno.EAGAIN or e.errno == errno.EWOULDBLOCK:
                    # Неблокирующий сокет, повторяем попытку
                    continue
                raise
        return bytes_sent
    
    def recv_all(sock, length):
        """Получение точного количества данных"""
        data = b''
        while len(data) < length:
            try:
                chunk = sock.recv(length - len(data))
                if not chunk:
                    raise RuntimeError("Сокет закрыт")
                data += chunk
            except socket.error as e:
                if e.errno == errno.EAGAIN or e.errno == errno.EWOULDBLOCK:
                    continue
                raise
        return data
    
    print("   Функции send_all и recv_all определены")
    
    print("\n5. ✅ Мониторинг производительности:")
    
    def socket_performance_tips():
        """Советы по производительности"""
        tips = [
            "Используйте большие буферы для высокой пропускной способности",
            "Отключите алгоритм Nagle (TCP_NODELAY) для низких задержек",
            "Используйте неблокирующие сокеты с select/epoll для масштабируемости",
            "Переиспользуйте соединения когда возможно",
            "Используйте connection pooling для частых подключений",
            "Мониторьте использование памяти при работе с множеством соединений"
        ]
        
        for i, tip in enumerate(tips, 1):
            print(f"   {i}. {tip}")
    
    socket_performance_tips()
    
    print("\n6. ✅ Безопасность:")
    
    security_tips = [
        "Всегда валидируйте входящие данные",
        "Используйте таймауты для предотвращения DoS атак",
        "Ограничивайте количество одновременных соединений",
        "Логируйте подозрительную активность",
        "Используйте SSL/TLS для шифрования",
        "Фильтруйте входящие подключения по IP",
        "Реализуйте rate limiting"
    ]
    
    for i, tip in enumerate(security_tips, 1):
        print(f"   {i}. {tip}")

socket_best_practices()
```