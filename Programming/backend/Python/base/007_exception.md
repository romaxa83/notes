#python

Исключения в Python - это механизм обработки ошибок, который позволяет программе корректно реагировать на непредвиденные ситуации и восстанавливаться после них.

Следующие исключения служат базовыми классами для всех остальных:
- BaseException — корневой класс для всех исключений. Все встроенные исключения - производные от этого класса.
- Exception — базовый класс для всех исключений, связанных с программой. К этой категории относятся все встроенные исключения, кроме SystemExit, GeneratorExit и KeyboardInterrupt. Исключения, определяемые пользователем, должны быть унаследованы от Exception.
- ArithmeticError — базовый класс для арифметических исключений, включая OverflowError, ZeroDivisionError и FloatingPointError.
- LookupError — базовый класс для ошибок индексирования и обращения по ключу, включая IndexError и KeyError.
- EnvironmentError — базовый класс для ошибок за пределами Python. Синоним для OSError.
==Эти исключения никогда не выдаются программами явно==. Но они могут использоваться для перехвата некоторых классов ошибок
---
#### 🔹 Основы исключений
```python
print("=== Что такое исключения ===")

# Пример возникновения исключения
def demonstrate_exceptions():
    """Демонстрирует различные типы исключений"""
    
    print("1. ZeroDivisionError:")
    try:
        result = 10 / 0
    except ZeroDivisionError as e:
        print(f"   Ошибка деления на ноль: {e}")
    
    print("\n2. IndexError:")
    try:
        my_list = [1, 2, 3]
        value = my_list[10]
    except IndexError as e:
        print(f"   Ошибка индекса: {e}")
    
    print("\n3. KeyError:")
    try:
        my_dict = {"a": 1, "b": 2}
        value = my_dict["c"]
    except KeyError as e:
        print(f"   Ошибка ключа: {e}")
    
    print("\n4. TypeError:")
    try:
        result = "строка" + 42
    except TypeError as e:
        print(f"   Ошибка типа: {e}")
    
    print("\n5. ValueError:")
    try:
        number = int("не число")
    except ValueError as e:
        print(f"   Ошибка значения: {e}")
    
    print("\n6. FileNotFoundError:")
    try:
        with open("несуществующий_файл.txt", "r") as file:
            content = file.read()
    except FileNotFoundError as e:
        print(f"   Файл не найден: {e}")
    
    print("\n7. AttributeError:")
    try:
        my_string = "текст"
        my_string.несуществующий_метод()
    except AttributeError as e:
        print(f"   Ошибка атрибута: {e}")

demonstrate_exceptions()

# Исключение прерывает выполнение программы
print(f"\n=== Прерывание выполнения ===")

def without_exception_handling():
    """Функция без обработки исключений"""
    print("Начало функции")
    result = 10 / 0  # Здесь произойдет исключение
    print("Эта строка никогда не выполнится")
    return result

def with_exception_handling():
    """Функция с обработкой исключений"""
    print("Начало функции")
    try:
        result = 10 / 0
        print("Эта строка не выполнится")
        return result
    except ZeroDivisionError:
        print("Обработали ошибку деления на ноль")
        return None
    finally:
        print("Этот блок выполнится всегда")

print("Функция с обработкой исключений:")
result = with_exception_handling()
print(f"Результат: {result}")

# Без обработки исключений программа остановилась бы
print("\nБез обработки исключения программа остановилась бы здесь:")
try:
    without_exception_handling()
except ZeroDivisionError:
    print("Перехватили исключение на верхнем уровне")

# Иерархия исключений
print(f"\n=== Иерархия исключений ===")

def show_exception_hierarchy():
    """Показывает иерархию исключений"""
    
    # BaseException - корневой класс всех исключений
    # ├── SystemExit
    # ├── KeyboardInterrupt
    # ├── GeneratorExit
    # └── Exception  ← Большинство исключений наследуется отсюда
    #     ├── ArithmeticError
    #     │   ├── ZeroDivisionError
    #     │   ├── OverflowError
    #     │   └── FloatingPointError
    #     ├── LookupError
    #     │   ├── IndexError
    #     │   └── KeyError
    #     ├── OSError
    #     │   └── FileNotFoundError
    #     ├── ValueError
    #     ├── TypeError
    #     └── и многие другие...
    
    exceptions_to_test = [
        (lambda: 1/0, "ZeroDivisionError"),
        (lambda: [1][5], "IndexError"), 
        (lambda: {"a": 1}["b"], "KeyError"),
        (lambda: int("abc"), "ValueError"),
        (lambda: "a" + 1, "TypeError")
    ]
    
    for func, expected_error in exceptions_to_test:
        try:
            func()
        except Exception as e:
            print(f"{expected_error:20} -> {type(e).__name__}: {e}")
            print(f"                     Базовые классы: {[cls.__name__ for cls in type(e).__mro__[1:4]]}")

show_exception_hierarchy()

# Множественная обработка исключений
print(f"\n=== Множественная обработка исключений ===")

def process_user_input(user_input):
    """Обрабатывает пользовательский ввод с различными типами ошибок"""
    try:
        # Попытка преобразовать в число
        number = float(user_input)
        
        # Попытка выполнить операцию
        if number == 0:
            result = 1 / number  # ZeroDivisionError
        elif number < 0:
            result = number ** 0.5  # Может вызвать проблемы с комплексными числами
        else:
            result = 100 / number
        
        return f"Результат: {result:.2f}"
        
    except ValueError:
        return "Ошибка: Не удалось преобразовать в число"
    except ZeroDivisionError:
        return "Ошибка: Деление на ноль недопустимо"
    except (TypeError, OverflowError) as e:
        return f"Ошибка вычисления: {e}"
    except Exception as e:
        return f"Неожиданная ошибка: {type(e).__name__}: {e}"

# Тестируем с различными входными данными
test_inputs = ["42", "0", "-4", "abc", "", "1e308"]

for test_input in test_inputs:
    result = process_user_input(test_input)
    print(f"Ввод: '{test_input:>6}' -> {result}")
```

##### 🔸 Try-except-else-finally
```python
print("=== Блоки try-except-else-finally ===")

def demonstrate_try_blocks():
    """Демонстрирует работу всех блоков try-except-else-finally"""
    
    def safe_operation(operation_name, operation_func):
        """Безопасно выполняет операцию с полным циклом обработки"""
        print(f"\n--- Выполняем операцию: {operation_name} ---")
        
        try:
            print("  TRY: Начинаем выполнение операции")
            result = operation_func()
            print(f"  TRY: Операция выполнена успешно: {result}")
            return result
            
        except ValueError as ve:
            print(f"  EXCEPT ValueError: {ve}")
            return None
            
        except ZeroDivisionError as ze:
            print(f"  EXCEPT ZeroDivisionError: {ze}")
            return float('inf')
            
        except Exception as e:
            print(f"  EXCEPT общая ошибка: {type(e).__name__}: {e}")
            return "error"
            
        else:
            print("  ELSE: Операция завершена без исключений")
            # Блок else выполняется ТОЛЬКО если в try не было исключений
            
        finally:
            print("  FINALLY: Завершение операции (выполняется всегда)")
            # Блок finally выполняется ВСЕГДА
    
    # Тест 1: Успешная операция
    safe_operation("Успешное деление", lambda: 10 / 2)
    
    # Тест 2: Деление на ноль
    safe_operation("Деление на ноль", lambda: 10 / 0)
    
    # Тест 3: Преобразование строки в число
    safe_operation("Неверное преобразование", lambda: int("abc"))
    
    # Тест 4: Операция без ошибок для демонстрации else
    safe_operation("Простое вычисление", lambda: 2 ** 3)

demonstrate_try_blocks()

# Практический пример: работа с файлами
print(f"\n=== Практический пример: работа с файлами ===")

def safe_file_operations():
    """Демонстрирует безопасную работу с файлами"""
    
    def read_file_safe(filename):
        """Безопасно читает файл"""
        file_handle = None
        try:
            print(f"Попытка открыть файл: {filename}")
            file_handle = open(filename, 'r', encoding='utf-8')
            
            print("Файл открыт успешно")
            content = file_handle.read()
            
            if not content.strip():
                raise ValueError("Файл пуст")
                
            return content
            
        except FileNotFoundError:
            print("Ошибка: Файл не найден")
            return None
            
        except PermissionError:
            print("Ошибка: Недостаточно прав для чтения файла")
            return None
            
        except ValueError as ve:
            print(f"Ошибка содержимого: {ve}")
            return None
            
        except Exception as e:
            print(f"Неожиданная ошибка: {e}")
            return None
            
        else:
            print("Файл прочитан успешно")
            
        finally:
            if file_handle and not file_handle.closed:
                file_handle.close()
                print("Файл закрыт")
    
    # Создаем тестовый файл
    test_filename = "test_file.txt"
    try:
        with open(test_filename, 'w', encoding='utf-8') as f:
            f.write("Это тестовый файл\nСо второй строкой")
        print(f"Создан тестовый файл: {test_filename}")
    except Exception as e:
        print(f"Не удалось создать тестовый файл: {e}")
        return
    
    # Тестируем чтение файлов
    print("\n1. Чтение существующего файла:")
    content = read_file_safe(test_filename)
    if content:
        print(f"Содержимое:\n{content}")
    
    print("\n2. Чтение несуществующего файла:")
    content = read_file_safe("несуществующий.txt")
    
    # Создаем пустой файл
    empty_filename = "empty_file.txt"
    try:
        with open(empty_filename, 'w') as f:
            pass
        
        print(f"\n3. Чтение пустого файла:")
        content = read_file_safe(empty_filename)
        
        # Удаляем тестовые файлы
        import os
        os.remove(test_filename)
        os.remove(empty_filename)
        print("Тестовые файлы удалены")
        
    except Exception as e:
        print(f"Ошибка при работе с пустым файлом: {e}")

safe_file_operations()
```

#### 🔹 Стандартные исключения Python
```python
print("=== Стандартные исключения Python ===")

def demonstrate_standard_exceptions():
    """Демонстрирует основные стандартные исключения Python"""
    
    # 1. ArithmeticError и его подклассы
    print("1. Арифметические ошибки:")
    
    arithmetic_errors = [
        ("ZeroDivisionError", lambda: 1 / 0),
        ("OverflowError", lambda: 2.0 ** 1000000),  # Может не сработать на всех системах
        ("FloatingPointError", lambda: float('inf') - float('inf')),  # Редко встречается
    ]
    
    for error_name, error_func in arithmetic_errors:
        try:
            result = error_func()
            print(f"  {error_name:20}: Неожиданно выполнилось успешно: {result}")
        except ArithmeticError as e:
            print(f"  {error_name:20}: {type(e).__name__}: {e}")
        except Exception as e:
            print(f"  {error_name:20}: Другая ошибка: {type(e).__name__}: {e}")
    
    # 2. LookupError и его подклассы
    print(f"\n2. Ошибки поиска:")
    
    lookup_errors = [
        ("IndexError", lambda: [1, 2, 3][10]),
        ("KeyError", lambda: {"a": 1}["b"]),
    ]
    
    for error_name, error_func in lookup_errors:
        try:
            result = error_func()
        except LookupError as e:
            print(f"  {error_name:15}: {type(e).__name__}: {e}")
    
    # 3. OSError и его подклассы
    print(f"\n3. Ошибки операционной системы:")
    
    os_errors = [
        ("FileNotFoundError", lambda: open("несуществующий_файл.txt")),
        ("PermissionError", lambda: open("/root/protected_file", "w")),  # Может не сработать
        ("IsADirectoryError", lambda: open("/tmp", "r")),  # Попытка открыть директорию как файл
    ]
    
    for error_name, error_func in os_errors:
        try:
            result = error_func()
            result.close()  # Закрываем файл если открылся
        except OSError as e:
            print(f"  {error_name:20}: {type(e).__name__}: {e}")
        except Exception as e:
            print(f"  {error_name:20}: Другая ошибка: {type(e).__name__}")
    
    # 4. Ошибки типов и значений
    print(f"\n4. Ошибки типов и значений:")
    
    value_type_errors = [
        ("TypeError", lambda: "строка" + 42),
        ("ValueError", lambda: int("не число")),
        ("AttributeError", lambda: "строка".несуществующий_атрибут),
        ("NameError", lambda: несуществующая_переменная),
    ]
    
    for error_name, error_func in value_type_errors:
        try:
            result = error_func()
        except (TypeError, ValueError, AttributeError, NameError) as e:
            print(f"  {error_name:15}: {type(e).__name__}: {e}")
    
    # 5. Ошибки импорта
    print(f"\n5. Ошибки импорта:")
    
    import_errors = [
        ("ImportError", lambda: __import__("несуществующий_модуль")),
        ("ModuleNotFoundError", lambda: __import__("абсолютно_несуществующий_модуль")),
    ]
    
    for error_name, error_func in import_errors:
        try:
            result = error_func()
        except (ImportError, ModuleNotFoundError) as e:
            print(f"  {error_name:20}: {type(e).__name__}: {e}")
    
    # 6. Системные исключения
    print(f"\n6. Системные исключения:")
    print("  SystemExit: Вызывается функцией sys.exit()")
    print("  KeyboardInterrupt: Вызывается при Ctrl+C")
    print("  GeneratorExit: Вызывается при закрытии генератора")
    
    # Демонстрация SystemExit (безопасно)
    import sys
    try:
        sys.exit("Тестовый выход")
    except SystemExit as e:
        print(f"  SystemExit перехвачен: {e}")
    
    # 7. Ошибки синтаксиса и отступов
    print(f"\n7. Ошибки синтаксиса (возникают на этапе компиляции):")
    print("  SyntaxError: Неправильный синтаксис Python")
    print("  IndentationError: Неправильные отступы")
    print("  TabError: Смешивание табов и пробелов")
    
    # Эти ошибки нельзя перехватить обычным способом, 
    # так как они возникают при парсинге кода

demonstrate_standard_exceptions()

# Детальная информация об исключениях
print(f"\n=== Детальная информация об исключениях ===")

def exception_details():
    """Показывает, как получить детальную информацию об исключении"""
    
    import traceback
    import sys
    
    def problematic_function(x, y):
        """Функция, которая может вызвать различные исключения"""
        if not isinstance(x, (int, float)):
            raise TypeError(f"x должно быть числом, получено {type(x)}")
        
        if not isinstance(y, (int, float)):
            raise TypeError(f"y должно быть числом, получено {type(y)}")
        
        if y == 0:
            raise ZeroDivisionError("Деление на ноль недопустимо")
        
        if x < 0:
            raise ValueError(f"x должно быть положительным, получено {x}")
        
        return x / y
    
    test_cases = [
        (10, 2),      # Успешный случай
        (10, 0),      # ZeroDivisionError
        ("10", 2),    # TypeError
        (-5, 2),      # ValueError
    ]
    
    for i, (x, y) in enumerate(test_cases, 1):
        print(f"\n--- Тест {i}: problematic_function({x}, {y}) ---")
        
        try:
            result = problematic_function(x, y)
            print(f"Результат: {result}")
            
        except Exception as e:
            # Основная информация об исключении
            print(f"Тип исключения: {type(e).__name__}")
            print(f"Сообщение: {e}")
            print(f"Аргументы: {e.args}")
            
            # Информация о текущем исключении
            exc_type, exc_value, exc_traceback = sys.exc_info()
            print(f"sys.exc_info() тип: {exc_type.__name__}")
            
            # Трассировка стека
            print("Трассировка стека:")
            traceback.print_exc(limit=3)  # Показываем только последние 3 кадра
            
            # Форматированная трассировка
            print("Форматированная трассировка:")
            tb_lines = traceback.format_exception(exc_type, exc_value, exc_traceback)
            for line in tb_lines:
                print(f"  {line.rstrip()}")

exception_details()

# Цепочки исключений
print(f"\n=== Цепочки исключений ===")

def exception_chaining():
    """Демонстрирует цепочки исключений (exception chaining)"""
    
    def low_level_operation():
        """Операция низкого уровня"""
        return 10 / 0  # Вызовет ZeroDivisionError
    
    def mid_level_operation():
        """Операция среднего уровня"""
        try:
            return low_level_operation()
        except ZeroDivisionError as e:
            # Создаем новое исключение, сохраняя информацию о предыдущем
            raise RuntimeError("Ошибка в операции среднего уровня") from e
    
    def high_level_operation():
        """Операция высокого уровня"""
        try:
            return mid_level_operation()
        except RuntimeError as e:
            # Можем перехватить и создать еще одно исключение в цепочке
            raise ValueError("Критическая ошибка в системе") from e
    
    # Тестируем цепочку исключений
    print("Демонстрация цепочки исключений:")
    try:
        result = high_level_operation()
    except Exception as e:
        print(f"Финальное исключение: {type(e).__name__}: {e}")
        
        # Проходим по цепочке исключений
        current = e
        level = 0
        while current is not None:
            print(f"  Уровень {level}: {type(current).__name__}: {current}")
            current = current.__cause__  # Предыдущее исключение в цепочке
            level += 1
    
    # Неявная цепочка исключений
    print(f"\n--- Неявная цепочка исключений ---")
    
    def implicit_chaining():
        """Демонстрирует неявную цепочку исключений"""
        try:
            return 1 / 0
        except:
            # Если в блоке except возникает другое исключение,
            # Python автоматически создает цепочку
            undefined_variable  # NameError
    
    try:
        implicit_chaining()
    except Exception as e:
        print(f"Основное исключение: {type(e).__name__}: {e}")
        if e.__context__:
            print(f"Контекст: {type(e.__context__).__name__}: {e.__context__}")
    
    # Подавление цепочки исключений
    print(f"\n--- Подавление цепочки исключений ---")
    
    def suppress_chaining():
        """Показывает, как подавить цепочку исключений"""
        try:
            return 1 / 0
        except ZeroDivisionError:
            # "raise ... from None" подавляет цепочку исключений
            raise ValueError("Новая ошибка без контекста") from None
    
    try:
        suppress_chaining()
    except Exception as e:
        print(f"Исключение без цепочки: {type(e).__name__}: {e}")
        print(f"Контекст: {e.__context__}")  # None
        print(f"Причина: {e.__cause__}")     # None

exception_chaining()

# Группировка исключений (Python 3.11+)
print(f"\n=== Группировка исключений (ExceptionGroup) ===")

def exception_groups():
    """Демонстрирует ExceptionGroup (доступно в Python 3.11+)"""
    
    # Проверяем доступность ExceptionGroup
    try:
        # Пытаемся импортировать ExceptionGroup
        # В старых версиях Python это вызовет NameError
        ExceptionGroup
        
        print("ExceptionGroup доступен в этой версии Python")
        
        # Создаем группу исключений
        errors = [
            ValueError("Первая ошибка"),
            TypeError("Вторая ошибка"),
            RuntimeError("Третья ошибка")
        ]
        
        group = ExceptionGroup("Группа ошибок обработки", errors)
        
        # Вызываем группу исключений
        try:
            raise group
        except* ValueError as eg:
            print(f"Перехвачены ValueError: {len(eg.exceptions)} ошибки")
            for exc in eg.exceptions:
                print(f"  - {exc}")
        except* TypeError as eg:
            print(f"Перехвачены TypeError: {len(eg.exceptions)} ошибки")
            for exc in eg.exceptions:
                print(f"  - {exc}")
        except* RuntimeError as eg:
            print(f"Перехвачены RuntimeError: {len(eg.exceptions)} ошибки")
            for exc in eg.exceptions:
                print(f"  - {exc}")
                
    except NameError:
        print("ExceptionGroup недоступен в этой версии Python (требуется 3.11+)")
        print("Альтернативный подход - собственная реализация:")
        
        class SimpleExceptionGroup(Exception):
            """Простая реализация группы исключений"""
            
            def __init__(self, message, exceptions):
                super().__init__(message)
                self.exceptions = exceptions
            
            def __str__(self):
                base = super().__str__()
                exc_str = "\n".join(f"  - {type(exc).__name__}: {exc}" for exc in self.exceptions)
                return f"{base}\nСодержит исключения:\n{exc_str}"
        
        # Пример использования
        errors = [
            ValueError("Первая ошибка"),
            TypeError("Вторая ошибка"),
        ]
        
        try:
            raise SimpleExceptionGroup("Группа ошибок", errors)
        except SimpleExceptionGroup as eg:
            print(f"Перехвачена группа исключений: {eg}")

exception_groups()
```

#### 🔹 Создание собственных исключений

#### 🔹 Паттерны обработки исключений
```python
print("=== Паттерны обработки исключений ===")

# 1. Паттерн "Поймать и обработать"
def catch_and_handle_pattern():
    """Демонстрирует паттерн перехвата и обработки"""
    
    def safe_divide(a, b):
        """Безопасное деление с обработкой ошибки"""
        try:
            return a / b
        except ZeroDivisionError:
            print("Предупреждение: попытка деления на ноль")
            return float('inf')
        except TypeError:
            print("Ошибка: аргументы должны быть числами")
            return None
    
    test_cases = [(10, 2), (10, 0), ("10", 2)]
    
    print("--- Паттерн 'Поймать и обработать' ---")
    for a, b in test_cases:
        result = safe_divide(a, b)
        print(f"safe_divide({a}, {b}) = {result}")

# 2. Паттерн "Поймать и повторно выбросить"
def catch_and_rethrow_pattern():
    """Демонстрирует паттерн перехвата и повторного выброса"""
    
    def log_and_rethrow_operation(x, y):
        """Операция с логированием и повторным выбросом исключения"""
        try:
            if y == 0:
                raise ZeroDivisionError("Деление на ноль")
            return x / y
        except Exception as e:
            # Логируем ошибку
            print(f"[ЛОГ] Ошибка в операции: {type(e).__name__}: {e}")
            # Повторно выбрасываем исключение
            raise
    
    print(f"\n--- Паттерн 'Поймать и повторно выбросить' ---")
    try:
        result = log_and_rethrow_operation(10, 0)
        print(f"Результат: {result}")
    except ZeroDivisionError as e:
        print(f"Перехвачено на верхнем уровне: {e}")

# 3. Паттерн "Поймать и преобразовать"
def catch_and_transform_pattern():
    """Демонстрирует паттерн перехвата и преобразования исключения"""
    
    class UserServiceError(Exception):
        """Исключение сервиса пользователей"""
        pass
    
    def get_user_by_id(user_id):
        """Получает пользователя по ID"""
        users_db = {1: "Анна", 2: "Борис"}
        
        try:
            return users_db[user_id]
        except KeyError:
            # Преобразуем KeyError в более понятное исключение
            raise UserServiceError(f"Пользователь с ID {user_id} не найден") from None
        except Exception as e:
            # Преобразуем другие исключения
            raise UserServiceError(f"Ошибка получения пользователя: {e}") from e
    
    print(f"\n--- Паттерн 'Поймать и преобразовать' ---")
    try:
        user = get_user_by_id(999)
        print(f"Пользователь: {user}")
    except UserServiceError as e:
        print(f"Ошибка сервиса: {e}")

# 4. Паттерн "Failsafe" (безопасный отказ)
def failsafe_pattern():
    """Демонстрирует паттерн безопасного отказа"""
    
    def get_config_value(key, default=None):
        """Получает значение конфигурации с безопасным отказом"""
        config = {"debug": True, "timeout": 30}  # Имитация конфигурации
        
        try:
            value = config[key]
            # Имитируем возможную ошибку обработки
            if key == "invalid_key":
                raise ValueError("Неверный формат значения")
            return value
        except (KeyError, ValueError) as e:
            print(f"Предупреждение: {e}. Используется значение по умолчанию: {default}")
            return default
        except Exception as e:
            print(f"Неожиданная ошибка конфигурации: {e}. Используется значение по умолчанию: {default}")
            return default
    
    print(f"\n--- Паттерн 'Failsafe' ---")
    keys_to_test = [("debug", False), ("missing_key", "default"), ("timeout", 60)]
    
    for key, default in keys_to_test:
        value = get_config_value(key, default)
        print(f"Конфигурация '{key}': {value}")

# 5. Паттерн "Circuit Breaker"
def circuit_breaker_pattern():
    """Демонстрирует паттерн автоматического выключателя"""
    
    class CircuitBreaker:
        """Автоматический выключатель для защиты от каскадных ошибок"""
        
        def __init__(self, failure_threshold=3, timeout=10):
            self.failure_threshold = failure_threshold
            self.timeout = timeout
            self.failure_count = 0
            self.last_failure_time = None
            self.state = "CLOSED"  # CLOSED, OPEN, HALF_OPEN
        
        def call(self, func, *args, **kwargs):
            """Вызывает функцию через автоматический выключатель"""
            import time
            
            current_time = time.time()
            
            # Проверяем состояние выключателя
            if self.state == "OPEN":
                if current_time - self.last_failure_time > self.timeout:
                    self.state = "HALF_OPEN"
                    print("Circuit Breaker: Переход в состояние HALF_OPEN")
                else:
                    raise Exception("Circuit Breaker OPEN: сервис временно недоступен")
            
            try:
                result = func(*args, **kwargs)
                
                # Успешный вызов - сбрасываем счетчик ошибок
                if self.state == "HALF_OPEN":
                    self.state = "CLOSED"
                    self.failure_count = 0
                    print("Circuit Breaker: Переход в состояние CLOSED")
                
                return result
                
            except Exception as e:
                self.failure_count += 1
                self.last_failure_time = current_time
                
                print(f"Circuit Breaker: Ошибка {self.failure_count}/{self.failure_threshold}")
                
                if self.failure_count >= self.failure_threshold:
                    self.state = "OPEN"
                    print("Circuit Breaker: Переход в состояние OPEN")
                
                raise e
    
    # Ненадежная функция для демонстрации
    def unreliable_service():
        import random
        if random.random() < 0.8:  # 80% вероятность ошибки
            raise ConnectionError("Сервис недоступен")
        return "Сервис работает"
    
    print(f"\n--- Паттерн 'Circuit Breaker' ---")
    
    cb = CircuitBreaker(failure_threshold=3, timeout=2)
    
    # Тестируем автоматический выключатель
    for i in range(8):
        try:
            result = cb.call(unreliable_service)
            print(f"Попытка {i+1}: {result}")
        except Exception as e:
            print(f"Попытка {i+1}: Ошибка - {e}")
        
        import time
        time.sleep(0.5)  # Небольшая задержка между попытками

# 6. Паттерн "Retry with Backoff"
def retry_with_backoff_pattern():
    """Демонстрирует паттерн повторных попыток с увеличивающейся задержкой"""
    
    def retry_with_exponential_backoff(func, max_retries=3, base_delay=1.0, max_delay=60.0):
        """Выполняет функцию с повторными попытками и экспоненциальной задержкой"""
        import time
        import random
        
        for attempt in range(max_retries + 1):
            try:
                return func()
            except Exception as e:
                if attempt == max_retries:
                    print(f"Все попытки исчерпаны. Последняя ошибка: {e}")
                    raise
                
                # Вычисляем задержку (экспоненциальная + джиттер)
                delay = min(base_delay * (2 ** attempt), max_delay)
                jitter = random.uniform(0, delay * 0.1)  # 10% джиттер
                total_delay = delay + jitter
                
                print(f"Попытка {attempt + 1} неудачна: {e}")
                print(f"Повтор через {total_delay:.2f} сек...")
                time.sleep(total_delay)
    
    # Функция, которая может случайно завершиться успешно
    def flaky_network_call():
        import random
        if random.random() < 0.7:  # 70% вероятность ошибки
            raise ConnectionError("Сетевая ошибка")
        return "Данные получены успешно"
    
    print(f"\n--- Паттерн 'Retry with Backoff' ---")
    
    try:
        result = retry_with_exponential_backoff(flaky_network_call, max_retries=4, base_delay=0.5)
        print(f"Успех: {result}")
    except Exception as e:
        print(f"Финальная неудача: {e}")

# Выполняем демонстрации всех паттернов
catch_and_handle_pattern()
catch_and_rethrow_pattern()
catch_and_transform_pattern()
failsafe_pattern()
circuit_breaker_pattern()
retry_with_backoff_pattern()

# Комбинированные паттерны
print(f"\n=== Комбинированные паттерны обработки ===")

def combined_error_handling():
    """Демонстрирует комбинирование паттернов обработки ошибок"""
    
    class RobustService:
        """Сервис с комплексной обработкой ошибок"""
        
        def __init__(self):
            self.circuit_breaker = None  # Инициализируем при необходимости
            self.retry_count = {}
        
        def robust_operation(self, operation_id, data):
            """Надежная операция с несколькими уровнями защиты"""
            
            # 1. Валидация входных данных (failsafe)
            try:
                self._validate_input(operation_id, data)
            except ValueError as e:
                # Трансформируем в пользовательскую ошибку
                raise UserServiceError(f"Некорректные данные: {e}") from None
            
            # 2. Попытка выполнения с повторами
            max_retries = 3
            base_delay = 0.1
            
            for attempt in range(max_retries + 1):
                try:
                    # 3. Логирование попыток
                    print(f"[СЕРВИС] Операция {operation_id}, попытка {attempt + 1}")
                    
                    # 4. Основная операция
                    result = self._perform_operation(data)
                    
                    # 5. Успех - сбрасываем счетчики
                    if operation_id in self.retry_count:
                        del self.retry_count[operation_id]
                    
                    return result
                    
                except ConnectionError as e:
                    # Сетевые ошибки можно повторить
                    if attempt < max_retries:
                        delay = base_delay * (2 ** attempt)
                        print(f"[СЕРВИС] Сетевая ошибка, повтор через {delay} сек: {e}")
                        import time
                        time.sleep(delay)
                        continue
                    else:
                        raise UserServiceError("Сервис временно недоступен") from e
                
                except ValueError as e:
                    # Ошибки данных не повторяем
                    raise UserServiceError(f"Ошибка обработки данных: {e}") from e
                
                except Exception as e:
                    # Неожиданные ошибки логируем и трансформируем
                    print(f"[ОШИБКА] Неожиданная ошибка в операции {operation_id}: {e}")
                    raise UserServiceError("Внутренняя ошибка сервиса") from e
        
        def _validate_input(self, operation_id, data):
            """Валидация входных данных"""
            if not operation_id:
                raise ValueError("ID операции не может быть пустым")
            
            if not isinstance(data, dict):
                raise ValueError("Данные должны быть словарем")
            
            if "value" not in data:
                raise ValueError("Отсутствует обязательное поле 'value'")
        
        def _perform_operation(self, data):
            """Выполнение основной операции"""
            import random
            
            # Имитируем различные типы ошибок
            rand = random.random()
            
            if rand < 0.2:  # 20% - сетевая ошибка
                raise ConnectionError("Не удалось подключиться к серверу")
            elif rand < 0.3:  # 10% - ошибка данных
                raise ValueError("Неверный формат данных")
            elif rand < 0.4:  # 10% - неожиданная ошибка
                raise RuntimeError("Внутренняя ошибка сервиса")
            
            # 60% - успех
            return {"result": data["value"] * 2, "status": "success"}
    
    # Тестируем надежный сервис
    service = RobustService()
    
    test_operations = [
        ("OP1", {"value": 10}),           # Нормальная операция
        ("", {"value": 20}),              # Пустой ID
        ("OP2", "не словарь"),            # Неверный тип данных
        ("OP3", {"other": "field"}),      # Отсутствует обязательное поле
        ("OP4", {"value": 30}),           # Операция с возможными ошибками
    ]
    
    for op_id, data in test_operations:
        print(f"\n--- Операция: {op_id}, Данные: {data} ---")
        try:
            result = service.robust_operation(op_id, data)
            print(f"✓ Успех: {result}")
        except UserServiceError as e:
            print(f"✗ Ошибка пользователя: {e}")
        except Exception as e:
            print(f"✗ Системная ошибка: {type(e).__name__}: {e}")

# Создаем пользовательское исключение для демонстрации
class UserServiceError(Exception):
    pass

combined_error_handling()

# Контекстные менеджеры для обработки исключений
print(f"\n=== Контекстные менеджеры для обработки ошибок ===")

from contextlib import contextmanager
import logging

@contextmanager
def error_handling_context(operation_name, fallback_value=None, reraise=False):
    """Контекстный менеджер для обработки ошибок"""
    
    print(f"Начинаем операцию: {operation_name}")
    
    try:
        yield
        print(f"Операция '{operation_name}' выполнена успешно")
    
    except Exception as e:
        print(f"Ошибка в операции '{operation_name}': {type(e).__name__}: {e}")
        
        if reraise:
            print("Передаем исключение дальше")
            raise
        else:
            print(f"Используем fallback значение: {fallback_value}")
            return fallback_value
    
    finally:
        print(f"Завершение операции: {operation_name}")

# Демонстрация контекстного менеджера
def demonstrate_error_context():
    """Демонстрирует контекстный менеджер для ошибок"""
    
    print("--- Успешная операция ---")
    with error_handling_context("Чтение файла"):
        content = "Содержимое файла"
        print(f"Прочитано: {content}")
    
    print(f"\n--- Операция с ошибкой (с fallback) ---")
    with error_handling_context("Подключение к БД", fallback_value="Кеш"):
        raise ConnectionError("БД недоступна")
        data = "Данные из БД"  # Не выполнится
    
    print(f"\n--- Операция с ошибкой (с reraise) ---")
    try:
        with error_handling_context("Критическая операция", reraise=True):
            raise ValueError("Критическая ошибка")
    except ValueError as e:
        print(f"Перехвачено на верхнем уровне: {e}")

demonstrate_error_context()
```

#### 🔹 Производительность и исключения
```python
print("=== Производительность и исключения ===")

import time
import timeit
from contextlib import contextmanager

# 1. Влияние исключений на производительность
def demonstrate_exception_performance():
    """Демонстрирует влияние исключений на производительность"""
    
    print("--- Влияние исключений на производительность ---")
    
    # Функция без исключений
    def without_exceptions(data):
        """Обработка без исключений"""
        results = []
        for item in data:
            if isinstance(item, (int, float)) and item != 0:
                results.append(100 / item)
        return results
    
    # Функция с исключениями
    def with_exceptions(data):
        """Обработка с использованием исключений"""
        results = []
        for item in data:
            try:
                results.append(100 / item)
            except (TypeError, ZeroDivisionError):
                continue
        return results
    
    # Данные для тестирования
    # Большинство элементов валидны (нормальный случай)
    normal_data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] * 1000
    
    # Много невалидных элементов (плохой случай для исключений)
    bad_data = [1, 0, "str", None, 2, 0, "str", None] * 1000
    
    datasets = [
        ("Нормальные данные", normal_data),
        ("Данные с ошибками", bad_data)
    ]
    
    for dataset_name, data in datasets:
        print(f"\n{dataset_name} ({len(data)} элементов):")
        
        # Тест без исключений
        time_without = timeit.timeit(
            lambda: without_exceptions(data),
            number=100
        )
        
        # Тест с исключениями
        time_with = timeit.timeit(
            lambda: with_exceptions(data),
            number=100
        )
        
        print(f"  Без исключений: {time_without:.4f} сек")
        print(f"  С исключениями: {time_with:.4f} сек")
        print(f"  Разница: {time_with/time_without:.2f}x медленнее")

demonstrate_exception_performance()

# 2. Оптимизация обработки исключений
def demonstrate_exception_optimization():
    """Демонстрирует техники оптимизации обработки исключений"""
    
    print(f"\n--- Оптимизация обработки исключений ---")
    
    # Кеширование для избежания повторных исключений
    class CachedValidator:
        """Валидатор с кешированием для избежания повторных проверок"""
        
        def __init__(self):
            self.valid_cache = set()
            self.invalid_cache = set()
        
        def is_valid_email(self, email):
            """Проверка email с кешированием"""
            # Проверяем кеши
            if email in self.valid_cache:
                return True
            if email in self.invalid_cache:
                return False
            
            # Выполняем проверку
            try:
                if not isinstance(email, str):
                    raise TypeError("Email должен быть строкой")
                
                if "@" not in email or "." not in email:
                    raise ValueError("Неверный формат email")
                
                parts = email.split("@")
                if len(parts) != 2:
                    raise ValueError("Email должен содержать один символ @")
                
                local, domain = parts
                if not local or not domain:
                    raise ValueError("Пустая локальная часть или домен")
                
                # Валидный email
                self.valid_cache.add(email)
                return True
                
            except (TypeError, ValueError):
                # Невалидный email
                self.invalid_cache.add(email)
                return False
    
    # Без кеширования
    def validate_emails_no_cache(emails):
        """Валидация без кеширования"""
        validator = CachedValidator()
        results = []
        
        for email in emails:
            # Очищаем кеши для честного сравнения
            validator.valid_cache.clear()
            validator.invalid_cache.clear()
            results.append(validator.is_valid_email(email))
        
        return results
    
    # С кешированием
    def validate_emails_with_cache(emails):
        """Валидация с кешированием"""
        validator = CachedValidator()
        results = []
        
        for email in emails:
            results.append(validator.is_valid_email(email))
        
        return results
    
    # Тестовые данные с повторениями
    test_emails = [
        "valid@example.com",
        "invalid-email",
        "another@test.com",
        "valid@example.com",  # Повторение
        "bad@",
        "invalid-email",      # Повторение
        "valid@example.com",  # Еще одно повторение
    ] * 500  # Увеличиваем для заметной разницы
    
    print(f"Тестирование с {len(test_emails)} email адресами:")
    
    # Тест без кеширования
    time_no_cache = timeit.timeit(
        lambda: validate_emails_no_cache(test_emails),
        number=10
    )
    
    # Тест с кешированием
    time_with_cache = timeit.timeit(
        lambda: validate_emails_with_cache(test_emails),
        number=10
    )
    
    print(f"Без кеширования: {time_no_cache:.4f} сек")
    print(f"С кешированием: {time_with_cache:.4f} сек")
    print(f"Ускорение: {time_no_cache/time_with_cache:.2f}x")

demonstrate_exception_optimization()

# 3. Пулинг исключений (Exception Pooling)
def demonstrate_exception_pooling():
    """Демонстрирует технику пулинга исключений"""
    
    print(f"\n--- Пулинг исключений ---")
    
    class ExceptionPool:
        """Пул предсозданных исключений для повышения производительности"""
        
        def __init__(self):
            # Создаем пулы для часто используемых исключений
            self.value_errors = []
            self.type_errors = []
            self.key_errors = []
            
            # Предварительно создаем исключения
            for i in range(10):
                self.value_errors.append(ValueError())
                self.type_errors.append(TypeError())
                self.key_errors.append(KeyError())
        
        def get_value_error(self, message):
            """Получает ValueError из пула"""
            if self.value_errors:
                error = self.value_errors.pop()
                error.args = (message,)
                return error
            return ValueError(message)
        
        def get_type_error(self, message):
            """Получает TypeError из пула"""
            if self.type_errors:
                error = self.type_errors.pop()
                error.args = (message,)
                return error
            return TypeError(message)
        
        def return_exception(self, exception):
            """Возвращает исключение в пул"""
            if isinstance(exception, ValueError) and len(self.value_errors) < 10:
                exception.args = ()
                self.value_errors.append(exception)
            elif isinstance(exception, TypeError) and len(self.type_errors) < 10:
                exception.args = ()
                self.type_errors.append(exception)
    
    # Без пулинга
    def process_without_pool(data):
        """Обработка без пулинга исключений"""
        results = []
        for item in data:
            try:
                if not isinstance(item, (int, float)):
                    raise TypeError(f"Ожидается число, получено {type(item)}")
                
                if item < 0:
                    raise ValueError(f"Ожидается положительное число, получено {item}")
                
                results.append(item ** 0.5)
            except (TypeError, ValueError):
                continue
        return results
    
    # С пулингом
    def process_with_pool(data, pool):
        """Обработка с пулингом исключений"""
        results = []
        for item in data:
            error_to_return = None
            try:
                if not isinstance(item, (int, float)):
                    error_to_return = pool.get_type_error(f"Ожидается число, получено {type(item)}")
                    raise error_to_return
                
                if item < 0:
                    error_to_return = pool.get_value_error(f"Ожидается положительное число, получено {item}")
                    raise error_to_return
                
                results.append(item ** 0.5)
            except (TypeError, ValueError):
                if error_to_return:
                    pool.return_exception(error_to_return)
                continue
        return results
    
    # Тестовые данные с большим количеством ошибок
    test_data = [1, -2, "str", 4, -5, None, 7, "text", -9, 10] * 1000
    
    print(f"Обработка {len(test_data)} элементов:")
    
    # Создаем пул
    exception_pool = ExceptionPool()
    
    # Тест без пулинга
    time_without_pool = timeit.timeit(
        lambda: process_without_pool(test_data),
        number=100
    )
    
    # Тест с пулингом
    time_with_pool = timeit.timeit(
        lambda: process_with_pool(test_data, exception_pool),
        number=100
    )
    
    print(f"Без пулинга: {time_without_pool:.4f} сек")
    print(f"С пулингом: {time_with_pool:.4f} сек")
    print(f"Ускорение: {time_without_pool/time_with_pool:.2f}x")

demonstrate_exception_pooling()

# 4. Lazy Exception Creation
def demonstrate_lazy_exceptions():
    """Демонстрирует ленивое создание исключений"""
    
    print(f"\n--- Ленивое создание исключений ---")
    
    class LazyException:
        """Ленивое исключение, которое создается только при необходимости"""
        
        def __init__(self, exception_class, message_factory):
            self.exception_class = exception_class
            self.message_factory = message_factory
            self._exception = None
        
        def get_exception(self):
            """Получает исключение, создавая его при необходимости"""
            if self._exception is None:
                message = self.message_factory() if callable(self.message_factory) else self.message_factory
                self._exception = self.exception_class(message)
            return self._exception
        
        def raise_if_needed(self, condition):
            """Выбрасывает исключение, если условие истинно"""
            if condition:
                raise self.get_exception()
    
    # Обычный подход
    def validate_regular(data):
        """Обычная валидация с созданием всех исключений"""
        results = []
        
        for item in data:
            # Создаем исключения даже если они не понадобятся
            type_error = TypeError(f"Элемент должен быть числом, получен {type(item)}")
            value_error = ValueError(f"Элемент должен быть положительным, получен {item}")
            
            try:
                if not isinstance(item, (int, float)):
                    raise type_error
                
                if item <= 0:
                    raise value_error
                
                results.append(item)
            except (TypeError, ValueError):
                continue
        
        return results
    
    # Ленивый подход
    def validate_lazy(data):
        """Ленивая валидация с созданием исключений по требованию"""
        results = []
        
        for item in data:
            # Создаем ленивые исключения
            type_error = LazyException(
                TypeError,
                lambda: f"Элемент должен быть числом, получен {type(item)}"
            )
            value_error = LazyException(
                ValueError,
                lambda: f"Элемент должен быть положительным, получен {item}"
            )
            
            try:
                type_error.raise_if_needed(not isinstance(item, (int, float)))
                value_error.raise_if_needed(item <= 0)
                results.append(item)
            except (TypeError, ValueError):
                continue
        
        return results
    
    # Тестовые данные (много валидных элементов)
    test_data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] * 1000
    
    print(f"Валидация {len(test_data)} элементов:")
    
    # Тест обычного подхода
    time_regular = timeit.timeit(
        lambda: validate_regular(test_data),
        number=100
    )
    
    # Тест ленивого подхода
    time_lazy = timeit.timeit(
        lambda: validate_lazy(test_data),
        number=100
    )
    
    print(f"Обычный подход: {time_regular:.4f} сек")
    print(f"Ленивый подход: {time_lazy:.4f} сек")
    print(f"Ускорение: {time_regular/time_lazy:.2f}x")

demonstrate_lazy_exceptions()

# 5. Профилирование исключений
def demonstrate_exception_profiling():
    """Демонстрирует профилирование производительности исключений"""
    
    print(f"\n--- Профилирование исключений ---")
    
    import cProfile
    import io
    import pstats
    
    @contextmanager
    def profile_code():
        """Контекстный менеджер для профилирования кода"""
        profiler = cProfile.Profile()
        profiler.enable()
        try:
            yield profiler
        finally:
            profiler.disable()
    
    def analyze_profile(profiler, description):
        """Анализирует результаты профилирования"""
        s = io.StringIO()
        stats = pstats.Stats(profiler, stream=s)
        stats.sort_stats('cumulative')
        stats.print_stats(10)  # Топ 10 функций
        
        print(f"\n{description}:")
        print(s.getvalue())
    
    # Функция с частыми исключениями
    def exception_heavy_function():
        """Функция с большим количеством исключений"""
        results = []
        for i in range(1000):
            try:
                if i % 3 == 0:
                    raise ValueError("Каждый третий элемент")
                results.append(i)
            except ValueError:
                continue
        return results
    
    # Функция без исключений
    def exception_light_function():
        """Функция без исключений"""
        results = []
        for i in range(1000):
            if i % 3 != 0:  # Условие вместо исключения
                results.append(i)
        return results
    
    # Профилируем функцию с исключениями
    with profile_code() as profiler_heavy:
        for _ in range(10):
            exception_heavy_function()
    
    # Профилируем функцию без исключений
    with profile_code() as profiler_light:
        for _ in range(10):
            exception_light_function()
    
    analyze_profile(profiler_heavy, "Функция с исключениями")
    analyze_profile(profiler_light, "Функция без исключений")

# Запускаем только если есть достаточно времени
# demonstrate_exception_profiling()  # Раскомментируйте для детального профилирования

# 6. Рекомендации по производительности
def performance_recommendations():
    """Выводит рекомендации по производительности при работе с исключениями"""
    
    print(f"\n--- Рекомендации по производительности ---")
    
    recommendations = [
        "1. Используйте исключения для исключительных ситуаций, а не для управления потоком",
        "2. Предпочитайте проверки условий исключениям, если ошибки часты",
        "3. Кешируйте результаты валидации для повторно используемых данных",
        "4. Создавайте исключения только когда они действительно нужны",
        "5. Избегайте глубоких стеков вызовов с исключениями",
        "6. Используйте специфичные типы исключений вместо общих",
        "7. Профилируйте код с исключениями для выявления узких мест",
        "8. Рассмотрите возможность использования кодов ошибок для критичных к производительности участков",
        "9. Минимизируйте информацию в сообщениях исключений в продакшене",
        "10. Используйте контекстные менеджеры вместо try/finally где возможно"
    ]
    
    print("Основные рекомендации:")
    for rec in recommendations:
        print(f"  {rec}")
    
    print(f"\nПравило большого пальца:")
    print("  - Если ошибка происходит < 1% случаев: используйте исключения")
    print("  - Если ошибка происходит > 10% случаев: используйте проверки условий")
    print("  - В промежуточных случаях: профилируйте и выбирайте оптимальный подход")

performance_recommendations()

print(f"\n=== Завершение демонстрации производительности исключений ===")
```