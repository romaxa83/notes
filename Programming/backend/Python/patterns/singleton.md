#python #architecture #pattern #singleton 

#### 🔹 **Паттерн Singleton**

```python
class DatabaseManager:
    _instance = None
    
    def __init__(self, connection_string):
        if DatabaseManager._instance is not None:
            raise Exception("Используйте get_instance() для получения экземпляра")
        self.connection_string = connection_string
    
    @classmethod
    def get_instance(cls, connection_string=None):
        """Получение единственного экземпляра"""
        if cls._instance is None:
            if connection_string is None:
                raise ValueError("При первом вызове нужна строка подключения")
            cls._instance = cls(connection_string)
        return cls._instance

# Использование
db1 = DatabaseManager.get_instance("mysql://localhost")
db2 = DatabaseManager.get_instance()  # Вернет тот же экземпляр
print(db1 is db2)  # True
```

Более подробнее о паттерне здесь [[Obsidian Vault/backend/patterns/Singleton|Singleton]]