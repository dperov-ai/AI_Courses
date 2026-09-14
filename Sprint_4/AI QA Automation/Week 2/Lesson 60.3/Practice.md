# Практичні завдання до Уроку 60.3: Цикли

---

### Завдання 1: Валідація статус-кодів списку ендпоінтів
**Мета:** Написати цикл для пакетної перевірки результатів API тестів.

**Вхідні дані:**
```python
test_results = [
    {"endpoint": "/api/users", "status": 200},
    {"endpoint": "/api/orders", "status": 200},
    {"endpoint": "/api/payments", "status": 200},
]
```

**Що потрібно зробити:**
1. Проітерувати список `test_results` за допомогою циклу `for`.
2. За допомогою `assert` перевірити, що кожен `status` дорівнює `200`.

**Приклад розв'язку:**
```python
test_results = [
    {"endpoint": "/api/users", "status": 200},
    {"endpoint": "/api/orders", "status": 200},
    {"endpoint": "/api/payments", "status": 200},
]

for item in test_results:
    endpoint = item["endpoint"]
    status = item["status"]
    assert status == 200, f"Ендпоінт {endpoint} повернув помилковий статус {status}!"
    print(f"✓ {endpoint} -> {status} OK")

print("Всі ендпоінти успішно пройшли перевірку!")
```

---

### Завдання 2: Ретрай-цикл очікування завантаження елемента
**Мета:** Симулювати очікування появи кнопки на сторінці з обмеженням у 5 спроб.

**Що потрібно зробити:**
1. Створити цикл `while` з максимум 5 спробами.
2. Якщо кнопка з'явилася на 3-й спробі — перервати цикл через `break`.
3. Перевірити, що кнопка успішно знайдена.

**Приклад розв'язку:**
```python
import time

attempts = 0
max_attempts = 5
button_is_visible = False

while attempts < max_attempts:
    attempts += 1
    print(f"Спроба {attempts}: перевіряємо наявність кнопки...")
    
    # Симулюємо, що на 3-й спробі елемент з'явився в DOM:
    if attempts == 3:
        button_is_visible = True
        print("Кнопку успішно знайдено!")
        break
    time.sleep(0.5)

assert button_is_visible is True, "Кнопка не з'явилася після 5 спроб!"
```

---

### Завдання 3: Очищення списку цін за допомогою List Comprehension
**Мета:** Перетворити сирий список цін у числа та знайти суму замовлення.

**Вхідні дані:**
- `raw_cart_items = ["$15.00", "$45.50", "$10.50"]`

**Що потрібно зробити:**
1. За допомогою List Comprehension прибрати символ `$` і перетворити елементи у `float`.
2. Розрахувати загальну суму за допомогою функції `sum()`.
3. Перевірити, що сума дорівнює `71.0`.

**Приклад розв'язку:**
```python
raw_cart_items = ["$15.00", "$45.50", "$10.50"]

# Очищуємо та конвертуємо
prices = [float(item.replace("$", "")) for item in raw_cart_items]
total_sum = sum(prices)

print(f"Очищені ціни: {prices}")
print(f"Загальна сума: ${total_sum}")

assert total_sum == 71.0, f"Очікували суму 71.0, але отримали {total_sum}"
```
