Пример работы с kafka (python)

**tags** `kafka` `mq` 
**date** 2024-06-30 12:39

Apache Kafka - это система для хранения и передачи данных в реальном времени. Она помогает организовать потоковую обработку данных между приложениями и сервисами.

**Kafka работает следующим образом:**
- Данные отправляются в Kafka в виде потока (stream) - это называется "продюсером" (producer).
- Kafka хранит данные в виде сообщений (messages), которые организованы в топиках (topics).
- Препараторы (consumers) могут подписаться на топик и получать сообщения в реальном времени.
- Данные можно хранить в Kafka на ограниченное время или навсегда, в зависимости от настроек.


**Apache Kafka часто используется для:**
- Реального времени обработки данных (например, для мониторинга системы)
- Масштабируемых и высокопроизводительных приложений
- Интеграции между разными сервисами и системами

## Docker compose для запуска (kafka.yml)
```yaml
version: '2'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
      - "22181:2181"

  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - "29092:29092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092,PLAINTEXT_HOST://localhost:29092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

### Команды запуска
```bash
docker compose -f kafka.yml up -d

# Остановить
docker compose -f kafka.yml down
```


## Листинг config.py (общие настройки)
```python
HOST = 'localhost'
PORT = 29092
WEATHER_TOPIC = 'weather'
```

## Листинг consumer.py (тот кто читает)
```python
#!/usr/bin/env python

import asyncio
import json
from aiokafka import AIOKafkaConsumer

import config


def deserializer(serialized):
    """
    Десериализатор получаемых данных
    """
    return json.loads(serialized)


async def event_handler(value):
    """
    Обработчик события. Как только мы получаем новое сообщение,
    будет отрабатывать данная функция
    """
    print(f"Temperature: {value['temp']}, weather: {value['weather']}")


async def consume():
    consumer = AIOKafkaConsumer(
        config.WEATHER_TOPIC,
        bootstrap_servers=f'{config.HOST}:{config.PORT}',
        value_deserializer=deserializer
    )
    await consumer.start()
    try:
        async for msg in consumer:
            await event_handler(msg.value)
    finally:
        await consumer.stop()


if __name__ == '__main__':
    asyncio.run(consume())
```

## Листинг producer.py (тот кто отправляет)
```python
#!/usr/bin/env python

import asyncio
import json
import random
from aiokafka import AIOKafkaProducer

import config


def serializer(value):
    """
    Обмен данными происходит в байтах, поэтому мы должны
    сначала перевести наше значение JSON, а затем в байты
    """
    return json.dumps(value).encode()


async def produce():
    producer = AIOKafkaProducer(
        bootstrap_servers=f"{config.HOST}:{config.PORT}",
        value_serializer=serializer,
        compression_type="gzip",
    )
    await producer.start()
    try:
        while True:
            data = {
                "temp": random.randint(10, 20),
                "weather": random.choice(("rainy", "sunny")),
            }
            await producer.send(config.WEATHER_TOPIC, data)
            await asyncio.sleep(random.randint(1, 5))
    finally:
        await producer.stop()


if __name__ == "__main__":
    asyncio.run(produce())
```