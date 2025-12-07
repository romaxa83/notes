#python 

#### 🔹 Хеширование файлов
```python
import hashlib
from pathlib import Path

def hash_file(file_path: str, algorithm: str = 'sha256', chunk_size: int = 8192) -> str:
    """
    Вычисление хеша файла по частям для экономии памяти
    
    Args:
        file_path: Путь к файлу
        algorithm: Алгоритм хеширования
        chunk_size: Размер блока для чтения
    
    Returns:
        Хеш файла в шестнадцатеричном формате
    """
    hash_obj = hashlib.new(algorithm)
    
    try:
        with open(file_path, 'rb') as file:
            # Читаем файл по частям
            for chunk in iter(lambda: file.read(chunk_size), b''):
                hash_obj.update(chunk)
        
        return hash_obj.hexdigest()
    
    except FileNotFoundError:
        raise FileNotFoundError(f"Файл {file_path} не найден")
    except PermissionError:
        raise PermissionError(f"Нет доступа к файлу {file_path}")

def compare_files(file1_path: str, file2_path: str) -> bool:
    """Сравнение файлов по хешу"""
    try:
        hash1 = hash_file(file1_path)
        hash2 = hash_file(file2_path)
        return hash1 == hash2
    except (FileNotFoundError, PermissionError) as e:
        print(f"Ошибка при сравнении файлов: {e}")
        return False

# Пример использования
def file_hashing_example():
    """Пример хеширования файлов"""
    
    # Создаем тестовый файл
    test_file = Path("test_file.txt")
    test_content = "Это тестовый файл для демонстрации хеширования\n" * 1000
    test_file.write_text(test_content, encoding='utf-8')
    
    # Вычисляем хеши разными алгоритмами
    algorithms = ['md5', 'sha1', 'sha256', 'sha512']
    
    print(f"Хеши файла {test_file.name}:")
    for algo in algorithms:
        try:
            file_hash = hash_file(str(test_file), algo)
            print(f"{algo.upper()}: {file_hash}")
        except Exception as e:
            print(f"Ошибка при вычислении {algo}: {e}")
    
    # Создаем копию файла
    copy_file = Path("test_file_copy.txt")
    copy_file.write_text(test_content, encoding='utf-8')
    
    # Сравниваем файлы
    files_identical = compare_files(str(test_file), str(copy_file))
    print(f"\nФайлы идентичны: {files_identical}")
    
    # Изменяем копию
    modified_content = test_content + "Дополнительная строка"
    copy_file.write_text(modified_content, encoding='utf-8')
    
    files_identical = compare_files(str(test_file), str(copy_file))
    print(f"Файлы идентичны после изменения: {files_identical}")
    
    # Удаляем тестовые файлы
    test_file.unlink()
    copy_file.unlink()

# file_hashing_example()
```

#### 🔹 Контрольные суммы и целостность данных
```python
import hashlib
import json
from pathlib import Path
from typing import Dict, Any

class DataIntegrityChecker:
    """Класс для проверки целостности данных"""
    
    def __init__(self, algorithm: str = 'sha256'):
        self.algorithm = algorithm
    
    def calculate_checksum(self, data: bytes) -> str:
        """Вычисление контрольной суммы"""
        hash_obj = hashlib.new(self.algorithm)
        hash_obj.update(data)
        return hash_obj.hexdigest()
    
    def calculate_file_checksum(self, file_path: str) -> str:
        """Вычисление контрольной суммы файла"""
        hash_obj = hashlib.new(self.algorithm)
        
        with open(file_path, 'rb') as file:
            for chunk in iter(lambda: file.read(8192), b''):
                hash_obj.update(chunk)
        
        return hash_obj.hexdigest()
    
    def create_manifest(self, directory: str) -> Dict[str, str]:
        """Создание манифеста с контрольными суммами файлов"""
        manifest = {}
        dir_path = Path(directory)
        
        for file_path in dir_path.rglob('*'):
            if file_path.is_file():
                relative_path = str(file_path.relative_to(dir_path))
                checksum = self.calculate_file_checksum(str(file_path))
                manifest[relative_path] = checksum
        
        return manifest
    
    def save_manifest(self, manifest: Dict[str, str], manifest_path: str) -> None:
        """Сохранение манифеста в файл"""
        with open(manifest_path, 'w', encoding='utf-8') as file:
            json.dump(manifest, file, indent=2, ensure_ascii=False)
    
    def load_manifest(self, manifest_path: str) -> Dict[str, str]:
        """Загрузка манифеста из файла"""
        with open(manifest_path, 'r', encoding='utf-8') as file:
            return json.load(file)
    
    def verify_integrity(self, directory: str, manifest_path: str) -> Dict[str, Any]:
        """Проверка целостности файлов по манифесту"""
        stored_manifest = self.load_manifest(manifest_path)
        current_manifest = self.create_manifest(directory)
        
        result = {
            'valid': [],
            'modified': [],
            'missing': [],
            'new': []
        }
        
        # Проверяем файлы из сохраненного манифеста
        for file_path, stored_checksum in stored_manifest.items():
            if file_path in current_manifest:
                current_checksum = current_manifest[file_path]
                if stored_checksum == current_checksum:
                    result['valid'].append(file_path)
                else:
                    result['modified'].append({
                        'path': file_path,
                        'stored': stored_checksum,
                        'current': current_checksum
                    })
            else:
                result['missing'].append(file_path)
        
        # Проверяем новые файлы
        for file_path in current_manifest:
            if file_path not in stored_manifest:
                result['new'].append(file_path)
        
        return result

def integrity_check_example():
    """Демонстрация проверки целостности"""
    
    # Создаем тестовую директорию и файлы
    test_dir = Path("test_integrity")
    test_dir.mkdir(exist_ok=True)
    
    # Создаем тестовые файлы
    (test_dir / "file1.txt").write_text("Содержимое файла 1", encoding='utf-8')
    (test_dir / "file2.txt").write_text("Содержимое файла 2", encoding='utf-8')
    (test_dir / "subdir").mkdir(exist_ok=True)
    (test_dir / "subdir" / "file3.txt").write_text("Содержимое файла 3", encoding='utf-8')
    
    checker = DataIntegrityChecker()
    
    # Создаем манифест
    manifest = checker.create_manifest(str(test_dir))
    manifest_path = str(test_dir / "manifest.json")
    checker.save_manifest(manifest, manifest_path)
    
    print("Создан манифест:")
    for file_path, checksum in manifest.items():
        print(f"  {file_path}: {checksum[:16]}...")
    
    # Проверяем целостность (должно быть все OK)
    result = checker.verify_integrity(str(test_dir), manifest_path)
    print(f"\nПроверка целостности:")
    print(f"  Валидные файлы: {len(result['valid'])}")
    print(f"  Измененные файлы: {len(result['modified'])}")
    print(f"  Отсутствующие файлы: {len(result['missing'])}")
    print(f"  Новые файлы: {len(result['new'])}")
    
    # Изменяем один файл
    (test_dir / "file1.txt").write_text("ИЗМЕНЕННОЕ содержимое файла 1", encoding='utf-8')
    
    # Добавляем новый файл
    (test_dir / "new_file.txt").write_text("Новый файл", encoding='utf-8')
    
    # Удаляем один файл
    (test_dir / "file2.txt").unlink()
    
    # Проверяем целостность снова
    result = checker.verify_integrity(str(test_dir), manifest_path)
    print(f"\nПроверка после изменений:")
    print(f"  Валидные файлы: {result['valid']}")
    print(f"  Измененные файлы: {[f['path'] for f in result['modified']]}")
    print(f"  Отсутствующие файлы: {result['missing']}")
    print(f"  Новые файлы: {result['new']}")
    
    # Очистка
    import shutil
    shutil.rmtree(test_dir)

integrity_check_example()
```

#### 🔹 Основные операции с файлами
```python
import shutil
import os
from pathlib import Path

def basic_file_operations():
    """Основные операции с файлами"""
    
    print("=== Основные операции с файлами ===")
    
    # Создаем тестовые файлы и директории
    test_dir = Path("test_shutil")
    test_dir.mkdir(exist_ok=True)
    
    source_file = test_dir / "source.txt"
    source_content = "Это исходный файл\nСо второй строкой\nИ третьей строкой"
    source_file.write_text(source_content, encoding='utf-8')
    
    # 1. Копирование файлов
    print("1. Копирование файлов:")
    
    # copy() - копирует содержимое и права доступа
    dest1 = test_dir / "copy1.txt"
    shutil.copy(source_file, dest1)
    print(f"   copy(): {source_file} -> {dest1}")
    
    # copy2() - копирует содержимое, права доступа и метаданные
    dest2 = test_dir / "copy2.txt"
    shutil.copy2(source_file, dest2)
    print(f"   copy2(): {source_file} -> {dest2}")
    
    # copyfile() - копирует только содержимое
    dest3 = test_dir / "copy3.txt"
    shutil.copyfile(source_file, dest3)
    print(f"   copyfile(): {source_file} -> {dest3}")
    
    # 2. Перемещение файлов
    print("\n2. Перемещение файлов:")
    move_source = test_dir / "move_me.txt"
    move_source.write_text("Файл для перемещения", encoding='utf-8')
    
    move_dest = test_dir / "moved.txt"
    shutil.move(move_source, move_dest)
    print(f"   move(): {move_source} -> {move_dest}")
    print(f"   Исходный файл существует: {move_source.exists()}")
    print(f"   Целевой файл существует: {move_dest.exists()}")
    
    # 3. Получение информации о диске
    print("\n3. Информация о диске:")
    disk_usage = shutil.disk_usage(".")
    print(f"   Всего места: {disk_usage.total // (1024**3)} ГБ")
    print(f"   Использовано: {disk_usage.used // (1024**3)} ГБ")
    print(f"   Свободно: {disk_usage.free // (1024**3)} ГБ")

basic_file_operations()
```

#### 🔹 Работа с директориями
```python
import shutil
import tempfile
from pathlib import Path

def directory_operations():
    """Операции с директориями"""
    
    print("=== Операции с директориями ===")
    
    # Создаем временную директорию для тестов
    with tempfile.TemporaryDirectory() as temp_dir:
        temp_path = Path(temp_dir)
        print(f"Временная директория: {temp_path}")
        
        # Создаем структуру директорий
        source_dir = temp_path / "source_directory"
        source_dir.mkdir()
        
        # Заполняем директорию файлами
        (source_dir / "file1.txt").write_text("Содержимое файла 1")
        (source_dir / "file2.txt").write_text("Содержимое файла 2")
        
        subdir = source_dir / "subdirectory"
        subdir.mkdir()
        (subdir / "nested_file.txt").write_text("Вложенный файл")
        
        print(f"\nСоздана структура:")
        for item in source_dir.rglob("*"):
            if item.is_file():
                print(f"   Файл: {item.relative_to(temp_path)}")
            else:
                print(f"   Папка: {item.relative_to(temp_path)}")
        
        # 1. Полное копирование дерева директорий
        print("\n1. Копирование дерева директорий:")
        dest_dir = temp_path / "copied_directory"
        
        try:
            shutil.copytree(source_dir, dest_dir)
            print(f"   copytree(): {source_dir.name} -> {dest_dir.name}")
            
            # Проверяем результат
            copied_files = list(dest_dir.rglob("*"))
            print(f"   Скопировано элементов: {len(copied_files)}")
            
        except FileExistsError:
            print("   Целевая директория уже существует")
        
        # 2. Копирование с игнорированием файлов
        print("\n2. Копирование с фильтрацией:")
        
        def ignore_txt_files(directory, files):
            """Функция для игнорирования .txt файлов"""
            return [f for f in files if f.endswith('.txt')]
        
        filtered_dest = temp_path / "filtered_copy"
        try:
            shutil.copytree(source_dir, filtered_dest, ignore=ignore_txt_files)
            print("   Скопировано без .txt файлов")
            
            for item in filtered_dest.rglob("*"):
                print(f"   {item.relative_to(temp_path)}")
                
        except FileExistsError:
            print("   Директория уже существует")
        
        # 3. Удаление дерева директорий
        print("\n3. Удаление директории:")
        if dest_dir.exists():
            shutil.rmtree(dest_dir)
            print(f"   rmtree(): удалена {dest_dir.name}")
            print(f"   Директория существует: {dest_dir.exists()}")

directory_operations()
```

#### 🔹 Архивирование и сжатие
```python
import shutil
import tempfile
from pathlib import Path

def archive_operations():
    """Работа с архивами"""
    
    print("=== Работа с архивами ===")
    
    with tempfile.TemporaryDirectory() as temp_dir:
        temp_path = Path(temp_dir)
        
        # Создаем структуру для архивирования
        data_dir = temp_path / "data_to_archive"
        data_dir.mkdir()
        
        # Создаем тестовые файлы
        (data_dir / "document1.txt").write_text("Документ 1\n" * 100)
        (data_dir / "document2.txt").write_text("Документ 2\n" * 150)
        
        subdir = data_dir / "reports"
        subdir.mkdir()
        (subdir / "report1.txt").write_text("Отчет 1\n" * 50)
        (subdir / "report2.txt").write_text("Отчет 2\n" * 75)
        
        print("1. Доступные форматы архивов:")
        formats = shutil.get_archive_formats()
        for name, description in formats:
            print(f"   {name}: {description}")
        
        print("\n2. Создание архивов:")
        
        # ZIP архив
        zip_path = temp_path / "archive"  # расширение добавится автоматически
        shutil.make_archive(str(zip_path), 'zip', str(data_dir))
        zip_file = Path(str(zip_path) + '.zip')
        
        print(f"   ZIP архив создан: {zip_file.name}")
        print(f"   Размер: {zip_file.stat().st_size} байт")
        
        # TAR.GZ архив
        targz_path = temp_path / "archive_targz"
        shutil.make_archive(str(targz_path), 'gztar', str(data_dir))
        targz_file = Path(str(targz_path) + '.tar.gz')
        
        print(f"   TAR.GZ архив создан: {targz_file.name}")
        print(f"   Размер: {targz_file.stat().st_size} байт")
        
        print("\n3. Распаковка архивов:")
        
        # Распаковка ZIP
        extract_dir = temp_path / "extracted_zip"
        shutil.unpack_archive(str(zip_file), str(extract_dir))
        
        print(f"   ZIP распакован в: {extract_dir.name}")
        extracted_files = list(extract_dir.rglob("*"))
        print(f"   Файлов извлечено: {len([f for f in extracted_files if f.is_file()])}")
        
        # Распаковка TAR.GZ
        extract_targz_dir = temp_path / "extracted_targz"
        shutil.unpack_archive(str(targz_file), str(extract_targz_dir))
        
        print(f"   TAR.GZ распакован в: {extract_targz_dir.name}")
        
        print("\n4. Доступные форматы для распаковки:")
        unpack_formats = shutil.get_unpack_formats()
        for name, extensions, description in unpack_formats:
            print(f"   {name} ({', '.join(extensions)}): {description}")

archive_operations()
```

#### 🔹 Поиск исполняемых файлов
```python
import shutil
import os

def executable_search():
    """Поиск исполняемых файлов в PATH"""
    
    print("=== Поиск исполняемых файлов ===")
    
    # Список программ для поиска
    programs_to_find = [
        "python", "python3", "pip", "git", 
        "node", "npm", "java", "gcc", "make"
    ]
    
    print("1. Поиск программ в PATH:")
    found_programs = {}
    
    for program in programs_to_find:
        path = shutil.which(program)
        found_programs[program] = path
        
        if path:
            print(f"   ✅ {program}: {path}")
        else:
            print(f"   ❌ {program}: не найден")
    
    print("\n2. Поиск с дополнительными путями:")
    
    # Добавляем дополнительные пути для поиска
    additional_paths = [
        "/usr/local/bin",
        "/opt/bin", 
        os.path.expanduser("~/bin")
    ]
    
    for program in ["python3", "git"]:
        path = shutil.which(program, path=os.pathsep.join(additional_paths))
        if path:
            print(f"   {program} в дополнительных путях: {path}")
    
    print("\n3. Информация о PATH:")
    path_dirs = os.environ.get('PATH', '').split(os.pathsep)
    print(f"   Количество директорий в PATH: {len(path_dirs)}")
    
    existing_dirs = [d for d in path_dirs if os.path.exists(d)]
    print(f"   Существующих директорий: {len(existing_dirs)}")
    
    print("\n4. Проверка выполнимости файлов:")
    
    # Создаем тестовый скрипт
    test_script = "test_executable.py"
    with open(test_script, 'w') as f:
        f.write("#!/usr/bin/env python3\nprint('Hello from script!')\n")
    
    # Делаем файл исполняемым (Unix/Linux/macOS)
    if os.name != 'nt':  # Не Windows
        import stat
        current_mode = os.stat(test_script).st_mode
        os.chmod(test_script, current_mode | stat.S_IEXEC)
    
    # Ищем наш скрипт
    script_path = shutil.which(test_script, path=".")
    print(f"   Тестовый скрипт найден: {script_path is not None}")
    
    # Очистка
    if os.path.exists(test_script):
        os.remove(test_script)

executable_search()
```

#### 🔹 Работа с правами доступа
```python
import shutil
import stat
import os
from pathlib import Path

def permissions_demo():
    """Демонстрация работы с правами доступа"""
    
    print("=== Работа с правами доступа ===")
    
    if os.name == 'nt':  # Windows
        print("Демонстрация ограничена на Windows")
        return
    
    test_dir = Path("test_permissions")
    test_dir.mkdir(exist_ok=True)
    
    # Создаем тестовые файлы
    test_file1 = test_dir / "file1.txt"
    test_file1.write_text("Файл с правами по умолчанию")
    
    test_file2 = test_dir / "file2.txt"
    test_file2.write_text("Файл с ограниченными правами")
    
    print("1. Права доступа по умолчанию:")
    
    def print_permissions(file_path: Path):
        """Вывод прав доступа файла"""
        file_stat = file_path.stat()
        mode = file_stat.st_mode
        
        # Преобразуем права в читаемый формат
        permissions = stat.filemode(mode)
        octal = oct(mode)[-3:]
        
        print(f"   {file_path.name}: {permissions} ({octal})")
    
    print_permissions(test_file1)
    print_permissions(test_file2)
    
    print("\n2. Изменение прав доступа:")
    
    # Делаем файл доступным только для чтения
    os.chmod(test_file2, stat.S_IRUSR | stat.S_IRGRP | stat.S_IROTH)  # 444
    print("   Установлены права только на чтение:")
    print_permissions(test_file2)
    
    # Создаем исполняемый скрипт
    script_file = test_dir / "script.sh"
    script_file.write_text("#!/bin/bash\necho 'Hello from script!'")
    
    # Делаем скрипт исполняемым
    current_mode = script_file.stat().st_mode
    os.chmod(script_file, current_mode | stat.S_IEXEC)
    
    print("   Скрипт сделан исполняемым:")
    print_permissions(script_file)
    
    print("\n3. Копирование с сохранением прав:")
    
    dest_dir = test_dir / "copied"
    dest_dir.mkdir()
    
    # copy2() сохраняет права доступа
    shutil.copy2(script_file, dest_dir / "copied_script.sh")
    
    print("   Права после copy2():")
    print_permissions(dest_dir / "copied_script.sh")
    
    # copyfile() не сохраняет права
    shutil.copyfile(script_file, dest_dir / "copied_script_no_perm.sh")
    
    print("   Права после copyfile():")
    print_permissions(dest_dir / "copied_script_no_perm.sh")
    
    print("\n4. Массовое изменение прав:")
    
    def set_directory_permissions(directory: Path, file_mode: int, dir_mode: int):
        """Установка прав для всех файлов и директорий"""
        for item in directory.rglob("*"):
            try:
                if item.is_file():
                    os.chmod(item, file_mode)
                elif item.is_dir():
                    os.chmod(item, dir_mode)
            except Exception as e:
                print(f"     Ошибка изменения прав {item}: {e}")
    
    # Устанавливаем стандартные права: 644 для файлов, 755 для директорий
    file_mode = stat.S_IRUSR | stat.S_IWUSR | stat.S_IRGRP | stat.S_IROTH  # 644
    dir_mode = stat.S_IRWXU | stat.S_IRGRP | stat.S_IXGRP | stat.S_IROTH | stat.S_IXOTH  # 755
    
    set_directory_permissions(test_dir, file_mode, dir_mode)
    
    print("   Установлены стандартные права (644/755)")
    print("   Проверим несколько файлов:")
    for item in list(test_dir.rglob("*"))[:3]:
        if item.is_file():
            print_permissions(item)
    
    # Очистка
    shutil.rmtree(test_dir, ignore_errors=True)

permissions_demo()
```

#### 🔹 Лучшие практики и советы при использования `shutil`
```python
import shutil
import os
import tempfile
from pathlib import Path
import logging

def best_practices_demo():
    """Лучшие практики использования shutil"""
    
    print("=== Лучшие практики ===")
    
    # Настройка логирования
    logging.basicConfig(level=logging.INFO, 
                       format='%(asctime)s - %(levelname)s - %(message)s')
    logger = logging.getLogger(__name__)
    
    print("1. ✅ Безопасные операции с проверками:")
    
    def safe_copy(src: Path, dst: Path) -> bool:
        """Безопасное копирование с проверками"""
        try:
            # Проверяем существование источника
            if not src.exists():
                logger.error(f"Источник не существует: {src}")
                return False
            
            # Проверяем, что источник не директория (для copyfile)
            if src.is_dir():
                logger.error(f"Источник является директорией: {src}")
                return False
            
            # Создаем родительские директории
            dst.parent.mkdir(parents=True, exist_ok=True)
            
            # Проверяем место на диске
            src_size = src.stat().st_size
            dst_free = shutil.disk_usage(dst.parent).free
            
            if src_size > dst_free:
                logger.error("Недостаточно места на диске")
                return False
            
            # Выполняем копирование
            shutil.copy2(src, dst)
            logger.info(f"Успешно скопирован: {src} -> {dst}")
            return True
            
        except PermissionError:
            logger.error("Недостаточно прав доступа")
            return False
        except Exception as e:
            logger.error(f"Ошибка копирования: {e}")
            return False
    
    # Тестируем безопасное копирование
    with tempfile.TemporaryDirectory() as temp_dir:
        temp_path = Path(temp_dir)
        
        test_file = temp_path / "test.txt"
        test_file.write_text("Тестовый файл")
        
        result = safe_copy(test_file, temp_path / "copy.txt")
        print(f"   Результат безопасного копирования: {result}")
    
    print("\n2. ✅ Использование контекстных менеджеров:")
    
    def atomic_file_operation(file_path: Path, content: str):
        """Атомарная операция записи файла"""
        temp_file = file_path.with_suffix(file_path.suffix + '.tmp')
        
        try:
            # Записываем во временный файл
            temp_file.write_text(content, encoding='utf-8')
            
            # Атомарно перемещаем
            shutil.move(temp_file, file_path)
            print(f"   Атомарно записан файл: {file_path.name}")
            
        except Exception as e:
            # Убираем временный файл в случае ошибки
            if temp_file.exists():
                temp_file.unlink()
            raise e
    
    with tempfile.TemporaryDirectory() as temp_dir:
        temp_path = Path(temp_dir)
        test_file = temp_path / "atomic_test.txt"
        
        atomic_file_operation(test_file, "Содержимое файла")
    
    print("\n3. ✅ Правильная обработка больших файлов:")
    
    def copy_large_file_with_progress(src: Path, dst: Path, 
                                    chunk_size: int = 1024*1024):
        """Копирование больших файлов с прогрессом"""
        if not src.exists():
            return False
        
        file_size = src.stat().st_size
        copied_size = 0
        
        dst.parent.mkdir(parents=True, exist_ok=True)
        
        try:
            with open(src, 'rb') as fsrc, open(dst, 'wb') as fdst:
                while True:
                    chunk = fsrc.read(chunk_size)
                    if not chunk:
                        break
                    
                    fdst.write(chunk)
                    copied_size += len(chunk)
                    
                    progress = (copied_size / file_size) * 100
                    print(f"\r   Прогресс: {progress:.1f}%", end="")
                
                print()  # Новая строка после прогресса
            
            # Копируем метаданные
            shutil.copystat(src, dst)
            return True
            
        except Exception as e:
            logger.error(f"Ошибка копирования: {e}")
            # Удаляем частично скопированный файл
            if dst.exists():
                dst.unlink()
            return False
    
    print("   Симуляция копирования большого файла:")
    with tempfile.TemporaryDirectory() as temp_dir:
        temp_path = Path(temp_dir)
        
        # Создаем "большой" файл
        large_file = temp_path / "large.txt"
        large_file.write_text("Большой файл\n" * 10000)
        
        result = copy_large_file_with_progress(
            large_file, 
            temp_path / "large_copy.txt",
            chunk_size=4096  # Маленький chunk для демонстрации
        )
        print(f"   Результат: {result}")
    
    print("\n4. ✅ Правильная очистка ресурсов:")
    print("   - Всегда используйте контекстные менеджеры")
    print("   - Проверяйте успешность операций")
    print("   - Логируйте важные операции")
    print("   - Обрабатывайте исключения")
    print("   - Проверяйте права доступа и место на диске")

best_practices_demo()
```

#### 🔹 Практический пример: Утилита для анализа лог-файлов
```python
import os
import re
from datetime import datetime
from collections import Counter, defaultdict
from pathlib import Path
import json

def analyze_log_file(log_path):
    """Анализ лог-файла веб-сервера"""
    
    # Паттерн для парсинга лог-записей Apache/Nginx
    log_pattern = re.compile(
        r'(\d+\.\d+\.\d+\.\d+) - - \[(.*?)\] "(.*?)" (\d+) (\d+)'
    )
    
    stats = {
        'total_requests': 0,
        'status_codes': Counter(),
        'ips': Counter(),
        'methods': Counter(),
        'hourly_traffic': defaultdict(int),
        'file_size': 0
    }
    
    log_file = Path(log_path)
    
    if not log_file.exists():
        print(f"Файл {log_path} не найден")
        return None
    
    stats['file_size'] = log_file.stat().st_size
    
    with log_file.open('r', encoding='utf-8') as file:
        for line_num, line in enumerate(file, 1):
            match = log_pattern.match(line.strip())
            
            if match:
                ip, timestamp, request, status, size = match.groups()
                
                # Парсинг запроса
                request_parts = request.split()
                method = request_parts[0] if request_parts else 'UNKNOWN'
                
                # Парсинг времени
                try:
                    dt = datetime.strptime(timestamp, '%d/%b/%Y:%H:%M:%S %z')
                    hour = dt.hour
                except:
                    hour = 0
                
                # Сбор статистики
                stats['total_requests'] += 1
                stats['status_codes'][status] += 1
                stats['ips'][ip] += 1
                stats['methods'][method] += 1
                stats['hourly_traffic'][hour] += 1
    
    return stats

def generate_report(stats):
    """Генерация отчета по статистике"""
    
    if not stats:
        return
    
    print("=" * 50)
    print("ОТЧЕТ ПО АНАЛИЗУ ЛОГ-ФАЙЛА")
    print("=" * 50)
    
    print(f"Размер файла: {stats['file_size']:,} байт")
    print(f"Всего запросов: {stats['total_requests']:,}")
    
    print("\nТОП-5 IP адресов:")
    for ip, count in stats['ips'].most_common(5):
        print(f"  {ip}: {count:,} запросов")
    
    print("\nСтатус-коды:")
    for status, count in stats['status_codes'].most_common():
        print(f"  {status}: {count:,} запросов")
    
    print("\nHTTP методы:")
    for method, count in stats['methods'].most_common():
        print(f"  {method}: {count:,} запросов")
    
    print("\nПиковые часы (ТОП-5):")
    hourly_sorted = sorted(stats['hourly_traffic'].items(), 
                          key=lambda x: x[1], reverse=True)
    for hour, count in hourly_sorted[:5]:
        print(f"  {hour:02d}:00: {count:,} запросов")

def save_stats_to_json(stats, output_path):
    """Сохранение статистики в JSON"""
    
    # Преобразование Counter объектов в обычные словари
    json_stats = {
        'total_requests': stats['total_requests'],
        'file_size': stats['file_size'],
        'status_codes': dict(stats['status_codes']),
        'ips': dict(stats['ips']),
        'methods': dict(stats['methods']),
        'hourly_traffic': dict(stats['hourly_traffic'])
    }
    
    output_file = Path(output_path)
    output_file.write_text(
        json.dumps(json_stats, ensure_ascii=False, indent=2),
        encoding='utf-8'
    )
    
    print(f"\nСтатистика сохранена в {output_path}")

# Пример использования
if __name__ == "__main__":
    import sys
    
    if len(sys.argv) < 2:
        print("Использование: python log_analyzer.py <путь_к_лог_файлу>")
        sys.exit(1)
    
    log_file_path = sys.argv[1]
    
    # Анализ лог-файла
    statistics = analyze_log_file(log_file_path)
    
    if statistics:
        # Генерация отчета
        generate_report(statistics)
        
        # Сохранение в JSON
        json_output = f"log_analysis_{datetime.now().strftime('%Y%m%d_%H%M%S')}.json"
        save_stats_to_json(statistics, json_output)
```