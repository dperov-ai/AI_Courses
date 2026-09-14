# Практичні завдання до Уроку 60.1: Змінні та типи даних

Ці вправи допоможуть закріпити базові навички роботи зі змінними для автоматизатора тестування.

---

### Завдання 1: Побудова динамічного тестового URL
**Мета:** Створити скрипт, який формує повний URL для запиту до API на основі базової адреси, версії API та ID ресурсу.

**Вхідні дані:**
- `base_url = "https://api.demostore.com"`
- `api_version = "v2"`
- `product_id = 789`

**Що потрібно зробити:**
1. За допомогою f-рядка згенерувати повний endpoint у форматі: `https://api.demostore.com/v2/products/789/details`.
2. Вивести результат у консоль.
3. Додати `assert`, який перевіряє, що сформований URL починається з `"https://"`.

**Приклад розв'язку:**
```python
base_url = "https://api.demostore.com"
api_version = "v2"
product_id = 789

full_url = f"{base_url}/{api_version}/products/{product_id}/details"
print(f"Сформований URL: {full_url}")

assert full_url.startswith("https://"), "URL повинен використовувати захищений протокол HTTPS!"
```

---

### Завдання 2: Валідація ціни після конвертації типів
**Мета:** Симулювати зчитування ціни з веб-елемента у вигляді тексту та перевірити її числове значення.

**Вхідні дані:**
- `raw_price_text = "$129.99"`
- `expected_max_price = 150.00`

**Що потрібно зробити:**
1. Видалити символ `$` з рядка за допомогою методу `.replace("$", "")`.
2. Перетворити отриманий рядок у число типу `float`.
3. За допомогою `assert` перевірити, що отримана ціна менша за `expected_max_price`.

**Приклад розв'язку:**
```python
raw_price_text = "$129.99"
expected_max_price = 150.00

# 1. Очищуємо від знака долара
clean_price_str = raw_price_text.replace("$", "")

# 2. Конвертуємо у float
actual_price = float(clean_price_str)

# 3. Перевіряємо
assert actual_price < expected_max_price, f"Ціна {actual_price} перевищує ліміт {expected_max_price}!"
print(f"Тест пройдено: ціна {actual_price} є валідною.")
```

---

### Завдання 3: Перевірка статусу користувача через булеві змінні
**Мета:** Написати перевірку прав доступу користувача перед запуском тесту.

**Вхідні дані:**
- `is_authenticated = True`
- `is_email_verified = True`
- `is_banned = False`

**Що потрібно зробити:**
1. Створити змінну `can_access_dashboard`, яка дорівнює `True`, якщо користувач авторизований, його email підтверджений і він НЕ заблокований.
2. Додати `assert can_access_dashboard is True`.

**Приклад розв'язку:**
```python
is_authenticated = True
is_email_verified = True
is_banned = False

can_access_dashboard = is_authenticated and is_email_verified and (not is_banned)

assert can_access_dashboard is True, "Користувач не повинен мати доступу до дашборду!"
print("Успіх: користувач має коректний доступ.")
```
