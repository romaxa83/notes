#grpc #python

Про gRPC подробнее здесь [[Programming/backend/GO/recipes/grpc/info|info]]

- создание сервиса с использованием FastAPI - https://habr.com/ru/articles/821065/
- https://github.com/0xN1ck/grpc_example
- https://habr.com/ru/articles/986284/


нужно реализовать три микросервиса, для теста grpc, один на python, другой на go и третий на  php, и связать их с помощью grpc, какие есть варианты как организовать код, думаю разложить их по разным папкам и подымать все через  docker-compose

types-protobuf

Ставим библиотеки:
```bash
poetry add grpcio protobuf grpcio-reflection grpcio-health-checking
poetry add grpcio-tools types-protobuf --group dev
```
==где:== 
- **`grpcio`**: Ядро фреймворка. Содержит основные механизмы для обмена данными по протоколу HTTP/2, управления соединениями и обработки вызовов.
- **`protobuf`**: Библиотека для сериализации данных. Она превращает структурированные объекты в компактный двоичный формат, который gRPC использует для передачи сообщений между клиентом и сервером.
- **`grpcio-tools`**: Инструментарий разработчика. Включает компилятор протоколов (`protoc`) и плагин для Python, который генерирует код (`_pb2.py` и `_pb2_grpc.py`) из ваших `.proto` файлов.
- **`grpcio-reflection`**: Позволяет серверу «рассказывать» о своих методах и типах данных внешним инструментам (например, Postman или `grpc_cli`). Это избавляет от необходимости вручную импортировать `.proto` файлы в программы для тестирования.
- **`grpcio-health-checking`**: Стандартный протокол для мониторинга состояния сервиса. С его помощью оркестраторы (например, Kubernetes) могут проверять, жив ли ваш сервис и готов ли он принимать трафик. 
- **`types-protobuf`**: Аннотации типов. Чтобы IDE (PyCharm/VS Code) не ругалась и подсказывала поля внутри сгенерированных gRPC-классов.

---
#### 🔹 Определение сервиса
Первый шаг в создании gRPC сервиса — это определение интерфейса с помощью Protocol Buffers (protobuf). Пример определения сервиса для управления заказами может выглядеть так:
```proto
// order.proto
syntax = "proto3";

package order;

enum OrderNotificationTypeEnum {
  ORDER_NOTIFICATION_TYPE_ENUM_UNSPECIFIED = 0;
  ORDER_NOTIFICATION_TYPE_ENUM_OK = 1;
}

message Order {
  string uuid = 1;
  string name = 2;
  bool completed = 3;
  string date = 4;
}

message CreateOrderRequest {
    string name = 1;
    bool completed = 2;
    string date = 3;
}

message CreateOrderResponse {
  OrderNotificationTypeEnum notificationType = 1;
  Order order = 2;
}

service OrderService {
  rpc CreateOrder (CreateOrderRequest) returns (CreateOrderResponse);
}
```
Здесь мы определяем сервис `OrderService` с методом `CreateOrder`, который принимает `CreateOrderRequest` и возвращает `CreateOrderResponse`. Также следует обратить внимание на `enum OrderNotificationTypeEnum` — это перечисление, содержащее типы уведомлений о событиях, связанных с созданием заказа. В нашем случае `OrderNotificationTypeEnum` используется для указания типа уведомления при операциях с заказами. Эти статусы играют важную роль в общении между клиентом и сервером gRPC, обеспечивая стандартизированный и понятный способ передачи информации о результатах операций. В данном перечислении два значения:
-  `ORDER_NOTIFICATION_TYPE_ENUM_UNSPECIFIED (0)`: Используется, когда тип уведомления не указан.
-  `ORDER_NOTIFICATION_TYPE_ENUM_OK (1)`: Указывает, что операция с заказом выполнена успешно.

#### 🔹 Генерация gRPC кода

После создания protobuf файла, необходимо сгенерировать gRPC код для Python. Это можно сделать с помощью команды из директории проекта (файл находится по пути - `./grpc_core/protos/order`):
```bash
python -m grpc_tools.protoc --python_out=./grpc_core/protos/order --grpc_python_out=./grpc_core/protos/order --pyi_out=./grpc_core/protos/order --proto_path=./grpc_core/protos/order ./grpc_core/protos/order/*.proto
```
Эта команда создаст Python файлы, которые содержат код для работы с определенными в protobuf сообщениями и сервисами, также в сгенерированном файле order_pb2_grpc.py следует проверить импорт на корректность. В моем случае пришлось записать импорт следующим образом:
```python
from grpc_core.protos.order import order_pb2 as order__pb2
```

#### 🔹 Реализация gRPC сервера

Теперь мы можем приступить к реализации gRPC сервера. Начнем с создания обработчика запросов. Обработчики запросов должны наследоваться от автоматически сгенерированного класса `order_pb2_grpc.OrderServiceServicer`.
```python
from loguru import logger

from grpc_core.protos.order import order_pb2
from grpc_core.protos.order import order_pb2_grpc
from grpc_core.servers.schemas.order import OrderCreateRequest
from grpc_core.servers.handlers.order import OrderHandler


class OrderService(order_pb2_grpc.OrderServiceServicer):
    """
    gRPC сервис для управления заказами, реализующий методы сервиса OrderService, описанные в order.proto.
    """
    def __init__(self) -> None:
        """
        Создает объект GrpcParseMessage для преобразования сообщений между
        форматами gRPC и внутренними форматами данных.
        """
        self.message = GrpcParseMessage()

    async def CreateOrder(self, request, context) -> order_pb2.CreateOrderResponse:
        """
        Обрабатывает gRPC запрос на создание заказа.

        Преобразует запрос из формата gRPC в объект OrderCreateRequest, передает его в обработчик
        OrderHandler.create_order для создания заказа и возвращает результат.
        """
        request = OrderCreateRequest(**self.message.rpc_to_dict(request))
        logger.info(f'Received request is for create order: {request}')

        result = await OrderHandler.create_order(
            request=request
        )

        response = self.message.dict_to_rpc(
            data=result.dict(),
            request_message=order_pb2.CreateOrderResponse(),
        )
        return response
```

##### 🔸 Класс `GrpcParseMessage`
Класс `GrpcParseMessage` предоставляет методы для преобразования данных между форматами gRPC и Python словарями, что облегчает работу с данными.
```python
from google.protobuf.json_format import MessageToDict, ParseDict

class GrpcParseMessage:
    @staticmethod
    def rpc_to_dict(request) -> dict:
        """ Переводит ответ grpc сервера в json. """
        return MessageToDict(
            request,
            preserving_proto_field_name=True,
            use_integers_for_enums=False,
            always_print_fields_with_no_presence=True
        )

    @staticmethod
    def dict_to_rpc(data: dict, request_message, ignore_unknown_fields: bool = True):
        """ Переводит json в запрос grpc сервера. """
        return ParseDict(
            data,
            request_message,
            ignore_unknown_fields=ignore_unknown_fields,
        )
```

##### 🔸 Класс `Server`
Класс `Server` отвечает за инициализацию и запуск gRPC сервера.
```python
import grpc
from grpc import aio
from grpc_core.protos.order import order_pb2_grpc
from grpc_core.servers.order import OrderService
from settings import settings

class Server:
    """
    Singleton класс для настройки и запуска gRPC сервера.
    """
    _instance = None

    def __new__(cls, *args, **kwargs):
        """
        Создает и возвращает единственный экземпляр класса Server.
        """
        if not cls._instance:
            cls._instance = super().__new__(cls)
        return cls._instance

    def __init__(self) -> None:
        """
        Инициализирует сервер, если он еще не инициализирован.

        Устанавливает адрес сервера, создает сервер gRPC и добавляет незащищенный порт.
        """
        if not hasattr(self, 'initialized'):
            self.SERVER_ADDRESS = f'{settings.GRPC_HOST_LOCAL}:{settings.GRPC_PORT}'
            self.server = aio.server(ThreadPoolExecutor(max_workers=10))
            self.server.add_insecure_port(self.SERVER_ADDRESS)
            self.initialized = True

    def register(self) -> None:
        """
        Регистрирует сервисы gRPC на сервере.

        Регистрирует сервис OrderService на gRPC сервере.
        """
        order_pb2_grpc.add_OrderServiceServicer_to_server(
            OrderService(), self.server
        )

    async def run(self) -> None:
        """
        Запускает сервер и ожидает его завершения.

        Создает таблицу Order, если она еще не существует, регистрирует сервисы и запускает сервер.
        """
        await Order.create_table(if_not_exists=True)
        self.register()
        await self.server.start()
        logger.info(f'*** Сервис gRPC запущен: {self.SERVER_ADDRESS} ***')
        await self.server.wait_for_termination()

    async def stop(self) -> None:
        """
        Останавливает сервер.

        Останавливает gRPC сервер без периода ожидания (grace period).
        """
        logger.info('*** Сервис gRPC остановлен ***')
        await self.server.stop(grace=False)
```

#### 🔹 Основной сервер
В файле `main.py` описана инициализация и запуск FastAPI приложения и gRPC сервера.
```python
import asyncio
import uvicorn
from contextlib import asynccontextmanager
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

from grpc_core.servers.manager import Server
from settings import settings
from api import order

@asynccontextmanager
async def lifespan(app: FastAPI):
    asyncio.create_task(Server().run())
    try:
        yield
    finally:
        await Server().stop()

app = FastAPI(
    lifespan=lifespan,
    title='Example gRPC service on Python',
    description='This showing how to use gRPC on Python',
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(order.router)

if __name__ == '__main__':
    uvicorn.run('main:app', port=settings.SERVICE_PORT, host=settings.SERVICE_HOST_LOCAL, reload=True)
```

##### 🔸 Обработчик создания заказа
Обработчик создания заказа (`OrderHandler.create_order`) отвечает за обработку логики создания нового заказа в базе данных.

```python
from api.models import Order

class OrderHandler:
    @staticmethod
    async def create_order(request):
        order = Order(**request.dict())
        await order.save()
        return order
```

##### 🔸 Схема данных
Для удобства работы с данными мы используем Pydantic для определения схемы данных запроса и ответа.
```python
from pydantic import BaseModel, Field
import uuid

class OrderCreateRequest(BaseModel):
    uuid: str = Field(default_factory=lambda: str(uuid.uuid4()))
    name: str
    completed: bool
    date: str

class OrderCreateResponse(BaseModel):
    notificationType: str
    order: OrderCreateRequest
```

#### 🔹 Реализация клиентской части
В файле `order.py` (в папке clients) реализован клиент для взаимодействия с gRPC сервером.
```python
import grpc
from grpc_core.protos.order import order_pb2_grpc
from settings import settings

async def grpc_order_client():
    """
    Создает асинхронный gRPC клиент для сервиса OrderService.

    Эта функция создает незащищенный gRPC канал с сервером, используя параметры хоста и порта,
    указанные в настройках, и возвращает клиентский объект для взаимодействия с OrderService.

    Возвращает:
    -----------
    order_pb2_grpc.OrderServiceStub
        Клиентский объект для взаимодействия с gRPC сервисом OrderService.
    """
    channel = grpc.aio.insecure_channel(f'{settings.GRPC_HOST_LOCAL}:{settings.GRPC_PORT}')
    client = order_pb2_grpc.OrderServiceStub(channel)
    return client
```

Этот клиент создает канал связи с gRPC сервером и возвращает stub для взаимодействия с методами сервиса.

В файле `order.py` (в папке api) реализовано использование клиента для взаимодействия с gRPC сервером.
```python
async def create_order(
    name: str,
    completed: bool,
    date: str = f'{datetime.utcnow()}Z',
    client: t.Any = Depends(grpc_order_client),
) -> JSONResponse:
    """
    Создает новый заказ через gRPC сервис OrderService.

    Функция вызывает метод CreateOrder gRPC сервиса OrderService для создания нового заказа
    с указанными параметрами. В случае ошибки gRPC запроса, выбрасывается HTTPException.
    """
    try:
        order = await client.CreateOrder(
            order_pb2.CreateOrderRequest(
                name=name,
                completed=completed,
                date=date
            )
        )
    except AioRpcError as e:
        logger.error(e.details())
        raise HTTPException(status_code=404, detail=e.details())

    return JSONResponse(MessageToDict(order))
```

> ⚠️ Обратите внимание на параметр `client`, который представляет собой gRPC клиент, полученный через зависимость `grpc_order_client`. Этот клиент используется для вызова метода `CreateOrder` удаленного gRPC сервиса. Благодаря этому клиенту происходит обмен данными между клиентским и серверным приложениями, что позволяет выполнять операции, такие как создание заказа, на удаленном сервере.

#### 🔹 Запуск сервера
```bash
uvicorn main:app --reload
```






Web стек (API)

- **`fastapi` (+ `standard`)**: Современный фреймворк для создания HTTP API. Видимо, твой сервис — это «гибрид», который общается и по HTTP, и по gRPC.
- **`uvicorn`**: Сервер, на котором «крутится» FastAPI.
- **`sse-starlette`**: Для реализации Server-Sent Events (односторонняя потоковая передача данных от сервера к браузеру в реальном времени).

Данные и Валидация

- **`pydantic`**: Валидация входящих данных. Гарантирует, что если пришло число, то это число.
- **`pydantic-settings`**: Удобное управление конфигами. Позволяет считывать настройки проекта из переменных окружения.
- **`faker`**: Генератор фейковых данных (имена, адреса, даты). Полезно для тестов или наполнения базы заглушками.

Инструменты (Dev & Ops)

- **`loguru`**: Логирование «на стероидах». Заменяет стандартный `logging`, делает логи красивыми и простыми в настройке.
- **`python-dotenv`**: Загружает переменные из файла `.env` в окружение.
- **`ruff`**: Очень быстрый линтер и форматер кода. Следит, чтобы код был чистым и в едином стиле.

**Итог:** Ты строишь современный микросервис, который умеет быстро отдавать данные через gRPC, имеет админку или внешний API на FastAPI и при этом отлично типизирован.