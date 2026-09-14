# Практичні завдання до Уроку 60.4: Функції

---

### Завдання 1: Хелпер для створення заголовків авторизації (Auth Headers)
**Мета:** Написати функцію, яка формує словник HTTP-заголовків із Bearer-токеном.

**Що потрібно зробити:**
1. Написати функцію `build_headers(token: str, content_type: str = "application/json")`.
2. Функція повинна повертати словник:
   ```python
   {
       "Authorization": f"Bearer {token}",
       "Content-Type": content_type
   }
   ```
3. Протестувати функцію за допомогою `assert`.

**Приклад розв'язку:**
```python
def build_headers(token: str, content_type: str = "application/json") -> dict:
    return {
        "Authorization": f"Bearer {token}",
        "Content-Type": content_type
    }

# Тестуємо хелпер:
my_token = "secret_jwt_xyz_123"
headers = build_headers(my_token)

print("Згенеровані заголовки:", headers)
assert headers["Authorization"] == "Bearer secret_jwt_xyz_123"
assert headers["Content-Type"] == "application/json"
```

---

### Завдання 2: Кастомний асерт для перевірки діапазону часу відповіді
**Мета:** Написати функцію-валідатор `assert_response_time(actual_ms, max_allowed_ms=500)`.

**Що потрібно зробити:**
1. Якщо `actual_ms <= max_allowed_ms` — вивести повідомлення про успіх.
2. Якщо перевищено — викинути `AssertionError` із детальним описом перевищення ліміту.

**Приклад розв'язку:**
```python
def assert_response_time(actual_ms: float, max_allowed_ms: float = 500.0):
    assert actual_ms <= max_allowed_ms, (
        f"Performance Failure! Відповідь зайняла {actual_ms} мс, що перевищує ліміт SLA {max_allowed_ms} мс!"
    )
    print(f"✓ Швидкодія в нормі: {actual_ms} мс <= {max_allowed_ms} мс")

# Тестові виклики:
assert_response_time(240.5)  # Пройде успішно
```

---

### Завдання 3: Генератор унікальних тестових користувачів
**Мета:** Написати функцію `generate_fake_user(prefix="qa_user")`, яка генерує унікальні дані для реєстрації.

**Що потрібно зробити:**
1. Використати модуль `time.time()` для створення унікального ID.
2. Повернути словник з полями `email`, `username`, `password`.

**Приклад розв'язку:**
```python
import time

def generate_fake_user(prefix="test"):
    timestamp = int(time.time())
    return {
        "username": f"{prefix}_{timestamp}",
        "email": f"{prefix}_{timestamp}@example.com",
        "password": "Password123!"
    }

user = generate_fake_user("automation")
print("Створено користувача:", user)

assert user["email"].endswith("@example.com")
assert "automation" in user["username"]
```
