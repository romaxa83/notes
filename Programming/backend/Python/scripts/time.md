#### 🔹 Измерение времени выполнения
```python
import time
import functools
from contextlib import contextmanager

def time_measurement():
    """Измерение времени выполнения"""
    
    print("=== Измерение времени выполнения ===")
    
    # 1. Простое измерение времени
    print("1. Простое измерение:")
    
    def slow_function():
        """Медленная функция для тестирования"""
        total = 0
        for i in range(1000000):
            total += i * i
        return total
    
    # Измерение с time.time()
    start_time = time.time()
    result = slow_function()
    end_time = time.time()
    
    execution_time = end_time - start_time
    print(f"   Результат: {result}")
    print(f"   Время выполнения (time.time()): {execution_time:.6f} секунд")
    
    # 2. Более точное измерение с perf_counter
    print("\n2. Точное измерение (perf_counter):")
    
    start_perf = time.perf_counter()
    result = slow_function()
    end_perf = time.perf_counter()
    
    perf_time = end_perf - start_perf
    print(f"   Время выполнения (perf_counter): {perf_time:.6f} секунд")
    print(f"   Разница в измерениях: {abs(execution_time - perf_time):.6f} секунд")
    
    # 3. Измерение процессорного времени
    print("\n3. Процессорное время:")
    
    start_process = time.process_time()
    result = slow_function()
    
    # Добавляем задержку (не должна учитываться в process_time)
    time.sleep(0.1)
    
    end_process = time.process_time()
    
    process_time_elapsed = end_process - start_process
    print(f"   Процессорное время: {process_time_elapsed:.6f} секунд")
    print("   (задержка sleep не учитывается)")
    
    # 4. Контекстный менеджер для измерения времени
    print("\n4. Контекстный менеджер:")
    
    @contextmanager
    def timer(description="Операция"):
        """Контекстный менеджер для измерения времени"""
        start = time.perf_counter()
        try:
            yield
        finally:
            end = time.perf_counter()
            print(f"   {description}: {end - start:.6f} секунд")
    
    # Использование контекстного менеджера
    with timer("Выполнение медленной функции"):
        result = slow_function()
    
    with timer("Список от 0 до 1000000"):
        big_list = list(range(1000000))
        length = len(big_list)
    
    # 5. Декоратор для измерения времени
    print("\n5. Декоратор для измерения:")
    
    def timeit(func):
        """Декоратор для измерения времени выполнения функции"""
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            start = time.perf_counter()
            result = func(*args, **kwargs)
            end = time.perf_counter()
            
            print(f"   Функция {func.__name__} выполнилась за {end - start:.6f} секунд")
            return result
        return wrapper
    
    @timeit
    def factorial(n):
        """Вычисление факториала"""
        if n <= 1:
            return 1
        return n * factorial(n - 1)
    
    @timeit
    def fibonacci(n):
        """Вычисление числа Фибоначчи (неэффективная рекурсия)"""
        if n <= 1:
            return n
        return fibonacci(n - 1) + fibonacci(n - 2)
    
    # Тестируем декоратор
    fact_result = factorial(10)
    print(f"   Факториал 10 = {fact_result}")
    
    fib_result = fibonacci(20)
    print(f"   20-е число Фибоначчи = {fib_result}")
    
    # 6. Профилирование с несколькими метриками
    print("\n6. Детальное профилирование:")
    
    class TimeProfiler:
        """Профилировщик времени выполнения"""
        
        def __init__(self, name="Операция"):
            self.name = name
            self.start_wall = None
            self.start_cpu = None
            self.start_perf = None
        
        def start(self):
            """Начало измерения"""
            self.start_wall = time.time()
            self.start_cpu = time.process_time()
            self.start_perf = time.perf_counter()
            print(f"   Начало профилирования: {self.name}")
        
        def stop(self):
            """Окончание измерения"""
            if self.start_wall is None:
                print("   Ошибка: профилирование не было начато")
                return
            
            end_wall = time.time()
            end_cpu = time.process_time()
            end_perf = time.perf_counter()
            
            wall_time = end_wall - self.start_wall
            cpu_time = end_cpu - self.start_cpu
            perf_time = end_perf - self.start_perf
            
            print(f"   Профилирование завершено: {self.name}")
            print(f"     Wall time (общее время): {wall_time:.6f}s")
            print(f"     CPU time (процессорное): {cpu_time:.6f}s")
            print(f"     Perf time (точное):      {perf_time:.6f}s")
            
            if wall_time > 0:
                cpu_usage = (cpu_time / wall_time) * 100
                print(f"     CPU usage: {cpu_usage:.1f}%")
        
        def __enter__(self):
            self.start()
            return self
        
        def __exit__(self, exc_type, exc_val, exc_tb):
            self.stop()
    
    # Тестируем профилировщик
    with TimeProfiler("Комплексная операция"):
        # CPU-интенсивная задача
        result = slow_function()
        
        # I/O задержка
        time.sleep(0.05)
        
        # Еще немного CPU работы
        squares = [i**2 for i in range(10000)]

time_measurement()
```

#### 🔹 Лучшие практики и оптимизация
```python
import time
import threading
from contextlib import contextmanager

def best_practices():
    """Лучшие практики работы с time"""
    
    print("=== Лучшие практики ===")
    
    # 1. Выбор правильной функции времени
    print("1. Выбор функции времени:")
    
    time_functions_guide = {
        'time.time()': {
            'use_case': 'Общее время, timestamps для БД',
            'precision': 'Системная точность (~1мс)',
            'affected_by': 'Системные изменения времени',
            'example': 'Логирование событий'
        },
        'time.perf_counter()': {
            'use_case': 'Измерение производительности',
            'precision': 'Высокая (наносекунды)',
            'affected_by': 'Ничем (монотонные часы)',
            'example': 'Бенчмарки, профилирование'
        },
        'time.process_time()': {
            'use_case': 'CPU время процесса',
            'precision': 'Высокая',
            'affected_by': 'Только CPU время',
            'example': 'Измерение вычислительной нагрузки'
        },
        'time.monotonic()': {
            'use_case': 'Относительные интервалы',
            'precision': 'Высокая',
            'affected_by': 'Ничем (монотонные)',
            'example': 'Таймауты, планировщики'
        }
    }
    
    for func_name, info in time_functions_guide.items():
        print(f"   {func_name}:")
        for key, value in info.items():
            print(f"     {key}: {value}")
        print()
    
    # 2. Производительность операций
    print("2. Сравнение производительности:")
    
    def benchmark_time_functions():
        """Бенчмарк функций времени"""
        iterations = 1000000
        
        functions_to_test = [
            ('time.time()', lambda: time.time()),
            ('time.perf_counter()', lambda: time.perf_counter()),
            ('time.process_time()', lambda: time.process_time()),
            ('time.monotonic()', lambda: time.monotonic()),
            ('time.localtime()', lambda: time.localtime()),
            ('time.gmtime()', lambda: time.gmtime()),
        ]
        
        results = []
        
        for name, func in functions_to_test:
            start = time.perf_counter()
            for _ in range(iterations):
                func()
            end = time.perf_counter()
            
            duration = end - start
            calls_per_sec = iterations / duration
            results.append((name, duration, calls_per_sec))
        
        print(f"   Производительность ({iterations:,} вызовов):")
        for name, duration, cps in sorted(results, key=lambda x: x[1]):
            print(f"     {name:<20}: {duration:.4f}s ({cps:,.0f} вызовов/сек)")
    
    benchmark_time_functions()
    
    # 3. Потокобезопасность
    print("\n3. Потокобезопасность:")
    
    class ThreadSafeTimer:
        """Потокобезопасный таймер"""
        
        def __init__(self):
            self._lock = threading.Lock()
            self._start_times = {}
            self._measurements = {}
        
        def start(self, timer_name):
            """Начало измерения"""
            with self._lock:
                self._start_times[timer_name] = time.perf_counter()
        
        def stop(self, timer_name):
            """Окончание измерения"""
            end_time = time.perf_counter()
            
            with self._lock:
                if timer_name not in self._start_times:
                    raise ValueError(f"Таймер '{timer_name}' не был запущен")
                
                duration = end_time - self._start_times[timer_name]
                
                if timer_name not in self._measurements:
                    self._measurements[timer_name] = []
                
                self._measurements[timer_name].append(duration)
                del self._start_times[timer_name]
                
                return duration
        
        def get_stats(self, timer_name):
            """Статистика по таймеру"""
            with self._lock:
                if timer_name not in self._measurements:
                    return None
                
                measurements = self._measurements[timer_name]
                
                return {
                    'count': len(measurements),
                    'total': sum(measurements),
                    'average': sum(measurements) / len(measurements),
                    'min': min(measurements),
                    'max': max(measurements)
                }
        
        @contextmanager
        def measure(self, timer_name):
            """Контекстный менеджер для измерения"""
            self.start(timer_name)
            try:
                yield
            finally:
                duration = self.stop(timer_name)
    
    # Тестируем потокобезопасный таймер
    safe_timer = ThreadSafeTimer()
    
    def worker_function(worker_id):
        """Рабочая функция для потоков"""
        for i in range(5):
            timer_name = f"worker_{worker_id}_task_{i}"
            
            with safe_timer.measure(timer_name):
                # Симуляция работы
                time.sleep(0.01 + (worker_id * 0.005))
                
                # CPU работа
                result = sum(j*j for j in range(1000))
    
    # Запускаем несколько потоков
    threads = []
    for i in range(3):
        thread = threading.Thread(target=worker_function, args=(i,))
        threads.append(thread)
        thread.start()
    
    # Ждем завершения
    for thread in threads:
        thread.join()
    
    print("   Статистика потокобезопасных измерений:")
    for timer_name in safe_timer._measurements:
        stats = safe_timer.get_stats(timer_name)
        if stats:
            print(f"     {timer_name}: среднее={stats['average']:.6f}s, "
                  f"мин={stats['min']:.6f}s, макс={stats['max']:.6f}s")
    
    # 4. Оптимизация форматирования
    print("\n4. Оптимизация форматирования:")
    
    def compare_formatting_methods():
        """Сравнение методов форматирования времени"""
        iterations = 100000
        current_time = time.localtime()
        
        methods = [
            ('time.strftime()', lambda: time.strftime("%Y-%m-%d %H:%M:%S", current_time)),
            ('Manual formatting', lambda: f"{current_time.tm_year}-{current_time.tm_mon:02d}-{current_time.tm_mday:02d} {current_time.tm_hour:02d}:{current_time.tm_min:02d}:{current_time.tm_sec:02d}"),
            ('time.asctime()', lambda: time.asctime(current_time)),
            ('time.ctime() with timestamp', lambda: time.ctime(time.mktime(current_time)))
        ]
        
        results = []
        
        for name, method in methods:
            start = time.perf_counter()
            for _ in range(iterations):
                method()
            end = time.perf_counter()
            
            duration = end - start
            results.append((name, duration, iterations / duration))
        
        print(f"   Производительность форматирования ({iterations:,} операций):")
        for name, duration, ops_per_sec in sorted(results, key=lambda x: x[1]):
            print(f"     {name:<25}: {duration:.4f}s ({ops_per_sec:,.0f} ops/sec)")
    
    compare_formatting_methods()
    
    # 5. Рекомендации по использованию
    print("\n5. Рекомендации:")
    
    recommendations = [
        "✅ Используйте time.perf_counter() для измерения производительности",
        "✅ Используйте time.monotonic() для таймаутов и интервалов", 
        "✅ Используйте time.time() для timestamps и логирования",
        "✅ Кэшируйте результаты time.localtime() если вызываете часто",
        "✅ Для высокочастотных операций избегайте time.strftime()",
        "✅ В многопоточных приложениях используйте блокировки для общих таймеров",
        "❌ Не используйте time.sleep() в основном потоке GUI приложений",
        "❌ Не полагайтесь на точность time.sleep() для критических операций",
        "❌ Избегайте частых вызовов time.strftime() в циклах",
        "❌ Не используйте time.time() для измерения коротких интервалов"
    ]
    
    for recommendation in recommendations:
        print(f"   {recommendation}")
    
    # 6. Паттерны использования
    print("\n6. Полезные паттерны:")
    
    # Паттерн 1: Кэшированное форматирование
    class CachedTimeFormatter:
        """Форматтер времени с кэшированием"""
        
        def __init__(self, cache_duration=1.0):
            self.cache_duration = cache_duration
            self._cache = {}
            self._cache_times = {}
        
        def format_time(self, format_string, timestamp=None):
            """Форматирование с кэшированием"""
            if timestamp is None:
                timestamp = time.time()
            
            # Округляем до секунды для кэширования
            cache_key = (format_string, int(timestamp))
            current_time = time.perf_counter()
            
            if (cache_key in self._cache and 
                current_time - self._cache_times[cache_key] < self.cache_duration):
                return self._cache[cache_key]
            
            # Форматируем и кэшируем
            formatted = time.strftime(format_string, time.localtime(timestamp))
            self._cache[cache_key] = formatted
            self._cache_times[cache_key] = current_time
            
            return formatted
    
    # Тестируем кэшированный форматтер
    formatter = CachedTimeFormatter()
    
    print("   Тестирование кэшированного форматтера:")
    start = time.perf_counter()
    for _ in range(10000):
        formatted = formatter.format_time("%Y-%m-%d %H:%M:%S")
    end = time.perf_counter()
    
    print(f"     10,000 вызовов с кэшем: {end - start:.6f}s")
    
    # Сравним с обычным форматированием
    start = time.perf_counter()
    for _ in range(10000):
        formatted = time.strftime("%Y-%m-%d %H:%M:%S")
    end = time.perf_counter()
    
    print(f"     10,000 вызовов без кэша: {end - start:.6f}s")
    
    # Паттерн 2: Адаптивный sleep
    def adaptive_sleep(target_duration, work_function, *args, **kwargs):
        """Адаптивная задержка с учетом времени выполнения работы"""
        start_time = time.perf_counter()
        
        # Выполняем работу
        result = work_function(*args, **kwargs)
        
        # Вычисляем оставшееся время
        work_duration = time.perf_counter() - start_time
        sleep_duration = max(0, target_duration - work_duration)
        
        if sleep_duration > 0:
            time.sleep(sleep_duration)
        
        total_duration = time.perf_counter() - start_time
        
        return result, total_duration, work_duration
    
    # Тестируем адаптивный sleep
    def variable_work():
        """Работа с переменным временем выполнения"""
        duration = 0.05 + (0.05 * (time.time() % 1))  # От 0.05 до 0.1 секунд
        time.sleep(duration)
        return duration
    
    print("\n   Тестирование адаптивного sleep (цель: 0.2с на итерацию):")
    for i in range(3):
        work_result, total_time, work_time = adaptive_sleep(0.2, variable_work)
        print(f"     Итерация {i+1}: работа={work_time:.3f}s, общее время={total_time:.3f}s")

best_practices()
```