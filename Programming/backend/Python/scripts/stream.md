#### 🔹 Обработки данных с использованием потоков
```python
import io
import csv
import json
import base64
from typing import List, Dict, Any

class DataProcessor:
    """Класс для обработки данных с использованием потоков"""
    
    @staticmethod
    def csv_to_json(csv_data: str) -> str:
        """Преобразование CSV данных в JSON через потоки"""
        
        # Создаем поток для CSV данных
        csv_stream = io.StringIO(csv_data)
        csv_reader = csv.DictReader(csv_stream)
        
        # Читаем данные
        rows = list(csv_reader)
        csv_stream.close()
        
        # Создаем поток для JSON
        json_stream = io.StringIO()
        json.dump(rows, json_stream, ensure_ascii=False, indent=2)
        
        result = json_stream.getvalue()
        json_stream.close()
        
        return result
    
    @staticmethod
    def create_report(data: List[Dict[str, Any]]) -> str:
        """Создание отчета в виде строки"""
        
        report_stream = io.StringIO()
        
        # Заголовок отчета
        report_stream.write("=== ОТЧЕТ ===\n")
        report_stream.write(f"Дата создания: {time.strftime('%Y-%m-%d %H:%M:%S')}\n")
        report_stream.write(f"Количество записей: {len(data)}\n\n")
        
        # Данные
        for i, item in enumerate(data, 1):
            report_stream.write(f"Запись #{i}:\n")
            for key, value in item.items():
                report_stream.write(f"  {key}: {value}\n")
            report_stream.write("\n")
        
        # Статистика
        report_stream.write("=== СТАТИСТИКА ===\n")
        if data:
            keys = data[0].keys()
            for key in keys:
                values = [item.get(key) for item in data if item.get(key) is not None]
                if values and all(isinstance(v, (int, float)) for v in values):
                    avg = sum(values) / len(values)
                    report_stream.write(f"Среднее {key}: {avg:.2f}\n")
        
        result = report_stream.getvalue()
        report_stream.close()
        
        return result
    
    @staticmethod
    def encode_data_to_base64(data: Dict[str, Any]) -> str:
        """Кодирование данных в Base64 через BytesIO"""
        
        # Сериализуем в JSON
        json_str = json.dumps(data, ensure_ascii=False)
        json_bytes = json_str.encode('utf-8')
        
        # Создаем BytesIO поток
        byte_stream = io.BytesIO(json_bytes)
        
        # Кодируем в Base64
        encoded_data = base64.b64encode(byte_stream.getvalue()).decode('ascii')
        
        byte_stream.close()
        return encoded_data
    
    @staticmethod
    def decode_data_from_base64(encoded_data: str) -> Dict[str, Any]:
        """Декодирование данных из Base64"""
        
        # Декодируем из Base64
        decoded_bytes = base64.b64decode(encoded_data.encode('ascii'))
        
        # Создаем поток из байтов
        byte_stream = io.BytesIO(decoded_bytes)
        
        # Читаем и парсим JSON
        json_str = byte_stream.read().decode('utf-8')
        data = json.loads(json_str)
        
        byte_stream.close()
        return data

def practical_examples():
    """Практические примеры использования потоков"""
    
    print("=== Практические применения ===")
    
    processor = DataProcessor()
    
    # Пример 1: CSV к JSON
    csv_data = """name,age,city
Анна,25,Москва
Иван,30,Санкт-Петербург
Мария,28,Новосибирск"""
    
    print("1. Преобразование CSV в JSON:")
    json_result = processor.csv_to_json(csv_data)
    print(json_result[:200] + "..." if len(json_result) > 200 else json_result)
    
    # Пример 2: Создание отчета
    sample_data = [
        {"name": "Анна", "age": 25, "salary": 50000},
        {"name": "Иван", "age": 30, "salary": 60000},
        {"name": "Мария", "age": 28, "salary": 55000}
    ]
    
    print("\n2. Создание отчета:")
    report = processor.create_report(sample_data)
    print(report[:300] + "..." if len(report) > 300 else report)
    
    # Пример 3: Кодирование в Base64
    test_data = {"message": "Привет, мир!", "numbers": [1, 2, 3, 4, 5]}
    
    print("\n3. Кодирование/декодирование Base64:")
    encoded = processor.encode_data_to_base64(test_data)
    print(f"Закодированные данные: {encoded[:50]}...")
    
    decoded = processor.decode_data_from_base64(encoded)
    print(f"Декодированные данные: {decoded}")
    print(f"Данные совпадают: {test_data == decoded}")

practical_examples()
```

#### 🔹 Оптимизация работы с потоками
```python
import io
import sys
from contextlib import redirect_stdout, redirect_stderr

def stream_optimization_tips():
    """Советы по оптимизации работы с потоками"""
    
    print("=== Советы по оптимизации ===")
    
    # 1. Используйте контекстные менеджеры
    print("1. Контекстные менеджеры:")
    with io.StringIO() as buffer:
        buffer.write("Автоматическое закрытие потока")
        content = buffer.getvalue()
    print(f"  Содержимое: {content}")
    
    # 2. Предварительное выделение памяти для BytesIO
    print("\n2. Предварительное выделение памяти:")
    
    # Менее эффективно
    small_buffer = io.BytesIO()
    for i in range(1000):
        small_buffer.write(f"Строка {i}\n".encode())
    
    # Более эффективно с предварительным выделением
    estimated_size = 1000 * 10  # Приблизительный размер
    large_buffer = io.BytesIO(b'\x00' * estimated_size)
    large_buffer.seek(0)
    large_buffer.truncate(0)
    
    for i in range(1000):
        large_buffer.write(f"Строка {i}\n".encode())
    
    print(f"  Размер буфера: {len(large_buffer.getvalue())} байт")
    
    # 3. Перенаправление stdout/stderr
    print("\n3. Перенаправление стандартных потоков:")
    
    # Захват stdout
    captured_output = io.StringIO()
    with redirect_stdout(captured_output):
        print("Этот текст будет захвачен")
        print("И этот тоже")
    
    print(f"  Захваченный вывод: {captured_output.getvalue().strip()}")
    
    # Захват stderr
    captured_errors = io.StringIO()
    with redirect_stderr(captured_errors):
        sys.stderr.write("Сообщение об ошибке\n")
    
    print(f"  Захваченная ошибка: {captured_errors.getvalue().strip()}")
    
    # 4. Работа с большими файлами по частям
    print("\n4. Обработка по частям (chunk processing):")
    
    # Имитация большого файла
    large_data = "Большая строка данных\n" * 10000
    large_stream = io.StringIO(large_data)
    
    chunk_size = 1024
    processed_chunks = 0
    
    while True:
        chunk = large_stream.read(chunk_size)
        if not chunk:
            break
        
        # Обработка chunk'а
        processed_chunks += 1
    
    print(f"  Обработано {processed_chunks} частей по {chunk_size} символов")
    
    large_stream.close()

stream_optimization_tips()
```