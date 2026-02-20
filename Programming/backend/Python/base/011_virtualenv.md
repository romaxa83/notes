#python

#### 🔹 Создание виртуального окружения
```bash
# Создание виртуального окружения  
python -m venv myproject_env  
  
# Создание с конкретной версией Python  
python3.11 -m venv myproject_env  
  
# Создание в папке проекта  
cd myproject  
python -m venv venv  
  
# Создание с системными пакетами (не рекомендуется)  
python -m venv --system-site-packages myenv
```

#### 🔹 Активация и деактивация
```bash
# macOS/Linux  
source venv/bin/activate  
  
# Windows  
venv\Scripts\activate  
  
# После активации в командной строке появится префикс  
(venv) $ python --version  
(venv) $ pip list  
  
# Деактивация  
(venv) $ deactivate
```
---
#### 🔹 Управление зависимостями

##### 🔸 Работа с requirements.txt
```bash
# Установка зависимостей из файла  
pip install -r requirements.txt  
  
# Создание requirements.txt из текущего окружения  
pip freeze > requirements.txt  
  
# Обновление всех пакетов  
pip install -r requirements.txt --upgrade  
  
# Установка только основных зависимостей (без dev)  
pip install -r requirements.txt --no-deps
```
---
#### 🔹 Лучшие практики
##### 🔸 Структура проекта с виртуальным окружением
```
myproject/
├── venv/                   # Виртуальное окружение (не в git!)
├── src/                    # Исходный код
│   ├── myproject/
│   │   ├── __init__.py
│   │   └── main.py
│   └── tests/
├── docs/                   # Документация
├── requirements/           # Файлы зависимостей
│   ├── base.txt
│   ├── development.txt
│   └── production.txt
├── .env                    # Переменные окружения (не в git!)
├── .gitignore
├── README.md
└── setup.py
```

##### 🔸 Файл .gitignore для Python проектов
```
# Виртуальные окружения
venv/
env/
ENV/
.venv/
.env/

# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# Переменные окружения
.env
.env.local
.env.development
.env.production

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# Базы данных
*.db
*.sqlite3

# Логи
*.log
```

##### 🔸 Скрипт для настройки окружения
```python
#!/usr/bin/env python3
"""
Скрипт для автоматической настройки окружения разработки
"""

import os
import sys
import subprocess
from pathlib import Path

def run_command(command, description):
    """Выполнение команды с описанием"""
    print(f"🔄 {description}")
    try:
        result = subprocess.run(
            command, 
            shell=True, 
            check=True, 
            capture_output=True, 
            text=True
        )
        print(f"✅ {description} - выполнено")
        return result.stdout
    except subprocess.CalledProcessError as e:
        print(f"❌ {description} - ошибка: {e.stderr}")
        return None

def setup_development_environment():
    """Настройка окружения разработки"""
    
    project_root = Path(__file__).parent
    venv_path = project_root / 'venv'
    
    print("🚀 Настройка окружения разработки")
    print(f"📁 Проект: {project_root}")
    
    # Проверка версии Python
    python_version = sys.version_info
    if python_version < (3, 8):
        print("❌ Требуется Python 3.8 или выше")
        sys.exit(1)
    
    print(f"✅ Python {python_version.major}.{python_version.minor}")
    
    # Создание виртуального окружения
    if not venv_path.exists():
        run_command(
            f"python -m venv {venv_path}",
            "Создание виртуального окружения"
        )
    else:
        print("ℹ️ Виртуальное окружение уже существует")
    
    # Определение команды активации
    if os.name == 'nt':  # Windows
        pip_cmd = str(venv_path / 'Scripts' / 'pip')
        python_cmd = str(venv_path / 'Scripts' / 'python')
    else:  # Unix-like
        pip_cmd = str(venv_path / 'bin' / 'pip')
        python_cmd = str(venv_path / 'bin' / 'python')
    
    # Обновление pip
    run_command(
        f"{python_cmd} -m pip install --upgrade pip",
        "Обновление pip"
    )
    
    # Установка зависимостей
    requirements_file = project_root / 'requirements' / 'development.txt'
    if requirements_file.exists():
        run_command(
            f"{pip_cmd} install -r {requirements_file}",
            "Установка зависимостей разработки"
        )
    
    # Создание .env файла
    env_file = project_root / '.env'
    if not env_file.exists():
        env_template = """# Настройки разработки
DEBUG=True
SECRET_KEY=your-secret-key-here
DATABASE_URL=sqlite:///db.sqlite3

# API ключи
API_KEY=your-api-key-here
"""
        env_file.write_text(env_template)
        print("✅ Создан файл .env")
    
    print("\n🎉 Окружение разработки настроено!")
    print("\n📝 Следующие шаги:")
    
    if os.name == 'nt':
        print("   1. Активируйте окружение: venv\\Scripts\\activate")
    else:
        print("   1. Активируйте окружение: source venv/bin/activate")
    
    print("   2. Запустите тесты: python -m pytest")
    print("   3. Начните разработку!")

if __name__ == "__main__":
    setup_development_environment()
```

##### 🔸Скрипты для работы с окружением
```bash
#!/bin/bash
# Скрипт для активации окружения и настройки переменных

# Цвета для вывода
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

echo -e "${GREEN}🚀 Активация окружения разработки${NC}"

# Проверка существования виртуального окружения
if [ ! -d "venv" ]; then
    echo -e "${RED}❌ Виртуальное окружение не найдено${NC}"
    echo -e "${YELLOW}Создайте его командой: python -m venv venv${NC}"
    exit 1
fi

# Активация окружения
source venv/bin/activate

# Загрузка переменных окружения
if [ -f ".env" ]; then
    export $(cat .env | grep -v '^#' | xargs)
    echo -e "${GREEN}✅ Переменные окружения загружены${NC}"
fi

# Проверка зависимостей
echo -e "${GREEN}📦 Проверка зависимостей${NC}"
pip check

echo -e "${GREEN}✅ Окружение активировано${NC}"
echo -e "${YELLOW}Для деактивации используйте: deactivate${NC}"

# Запуск оболочки в активированном окружении
exec $SHELL
```