#python #testing

#### 🔹 Основные команды

```bash
# Все тесты  
pytest  
  
# С отчётом о покрытии  
pytest --cov=src tests/  
  
# Конкретный файл  
pytest tests/test_authors.py  
  
# Конкретный класс тестов  
pytest tests/test_authors.py::TestCreateAuthor  
  
# С выводом print'ов  
pytest -s tests/test_authors.py
```
