# Практичні завдання до Уроку 60.7: Обробка винятків

---

### Завдання 1: Безпечний парсер числових значень
**Мета:** Написати функцію `safe_parse_int(raw_text, default_value=0)`, яка захищає автотест від падіння на брудних даних.

**Що потрібно зробити:**
1. Спробувати перетворити `raw_text` у `int`.
2. У разі `ValueError` або `TypeError` — перехопити помилку та повернути `default_value`.

**Приклад розв'язку:**
```python
def safe_parse_int(raw_text, default_value=0) -> int:
    try:
        return int(raw_text)
    except (ValueError, TypeError):
        print(f"Попередження: не вдалося перетворити '{raw_text}' у число. Використано дефолт {default_value}")
        return default_value

# Тестуємо:
assert safe_parse_int("200") == 200
assert safe_parse_int("invalid_number", default_value=-1) == -1
assert safe_parse_int(None, default_value=0) == 0
print("✓ Безпечний парсер успішно пройшов усі тести!")
```

---

### Завдання 2: Гарантоване закриття тестової сесії
**Мета:** Реалізувати блок `try-finally` для симуляції відкриття та обов'язкового закриття браузера.

**Що потрібно зробити:**
1. Створити прапорець `session_is_open = True`.
2. У блоці `try` симулювати виконання тесту, який падає з `AssertionError`.
3. У блоці `finally` скинути прапорець `session_is_open = False`.

**Приклад розв'язку:**
```python
session_is_open = True

try:
    print("Виконання кроків тесту в браузері...")
    # Симуляція падіння перевірки:
    raise AssertionError("Кнопка 'Купити' не відповіла на клік!")
except AssertionError as err:
    print(f"Тест зафіксував дефект: {err}")
finally:
    session_is_open = False
    print("Сесію закрито, ресурси звільнено.")

assert session_is_open is False, "Сесія браузера залишилася відкритою після збою!"
```
