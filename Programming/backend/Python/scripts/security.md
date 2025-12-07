#python 

#### 🔹 Рекомендации по безопасности при хешировании

```python
import hashlib
import secrets

def security_recommendations():
    """Рекомендации по безопасному использованию хеширования"""
    
    print("=== Рекомендации по безопасности ===")
    
    # ❌ НЕ используйте MD5 и SHA-1 для безопасности
    print("❌ Небезопасно:")
    weak_hash = hashlib.md5(b"password").hexdigest()
    print(f"MD5('password'): {weak_hash}")
    
    # ✅ Используйте SHA-256 или SHA-3 для хеширования данных
    print("\n✅ Безопасно:")
    secure_hash = hashlib.sha256(b"password").hexdigest()
    print(f"SHA-256('password'): {secure_hash}")
    
    # ✅ Всегда используйте соль для паролей
    password = "password"
    salt = secrets.token_bytes(32)
    salted_hash = hashlib.sha256(salt + password.encode()).hexdigest()
    print(f"SHA-256('password' + salt): {salted_hash}")
    
    # ✅ Используйте PBKDF2 для паролей
    pbkdf2_hash = hashlib.pbkdf2_hmac('sha256', password.encode(), salt, 100000)
    print(f"PBKDF2('password'): {pbkdf2_hash.hex()}")
    
    print("\nОсновные принципы:")
    print("1. Никогда не храните пароли в открытом виде")
    print("2. Используйте криптографически стойкие соли")
    print("3. Применяйте медленные функции (PBKDF2, bcrypt, scrypt)")
    print("4. Регулярно обновляйте количество итераций")
    print("5. Используйте актуальные алгоритмы (SHA-256, SHA-3)")

security_recommendations()
```