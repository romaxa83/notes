#python #package #rabbitmq

[**FastStream**](https://github.com/airtai/faststream) - это python-фреймворк для работы с брокерами сообщений. Он был создан для максимального упрощения разработки event-driven систем.Если просто - это очень толстый клиент для брокеров, который позволяет вам писать меньше инфраструктурного кода и сконцентрироваться на бизнес-логике ваших приложений.

Фреймворк предоставляет как унифицированный способ взаимодействия с брокерами, так и специфичные для каждого из них методы. Типичный эндпоинт для сообщений с использованием **FastStream** выглядит следующим образом:
```python
from faststream import FastStream
from faststream.rabbit import RabbitBroker

broker = RabbitBroker("amqp://guest:guest@localhost:5672/")
app = FastStream(broker)

@broker.subscriber("test-queue")  # название очереди RMQ
async def handle(msg: str):
    print(msg)
```

запуск
```bash
faststream run main:app
```

Так, для следующего сервиса из одного подписчика и продюсера:
```python
from pydantic import BaseModel
from faststream import FastStream
from faststream.nats import NatsBroker

broker = NatsBroker()
app = FastStream(broker)

class InputData(BaseModel):
    data: bytes

class Prediction(BaseModel):
    result: float

@broker.subscriber("test-subject")
@broker.publisher("out-subject")
async def handle_prediction(
    msg: InputData
) -> Prediction:
    predict = model.predict(msg)
    return predict
```

#### 🔹 Тестирование сервиса

Так, тесты на сервис из первого примера будут выглядеть следующим образом
```python
import pytest
from pydantic import ValidationError
from faststream.rabbit import TestRabbitBroker

@pytest.mark.asyncio()
async def test_correct():
    async with TestRabbitBroker(broker) as br:
        await br.publish("Hi!", "test-queue")

@pytest.mark.asyncio()
async def test_invalid():
    async with TestRabbitBroker(broker) as br:
        with pytest.raises(ValidationError):
            await br.publish(1, "test-queue")
```

При этом все взаимодействи будет происходить "в памяти", но если вы хотите переиспользовать те же тесты для реального брокера
```python
async with TestRabbitBroker(broker, with_real=True) as br:
```

#### 🔹 Observability

На текущий момент **FastStream** имеет встроенный функционал для интеграции с любой системой логирования, а также готовое решение для **OpenTelemetry**, с помощью которого вы можете построить сквозные трейсы по всей вашей системе.
О логировании вы можете прочитать в [нашей документации](https://faststream.airt.ai/latest/getting-started/logging/), как и о [трейсинге](https://faststream.ag2.ai/latest/getting-started/observability/opentelemetry/). Я также настоятельно рекомендую заглянуть вот в этот [репозиторий](https://github.com/draincoder/faststream-monitoring) (спасибо нашему контрибутору Роману), чтобы посмотреть на готовый пример сервисов и инфраструктуры для того, чтобы получить вот такую красивую картинку ваших трейсов