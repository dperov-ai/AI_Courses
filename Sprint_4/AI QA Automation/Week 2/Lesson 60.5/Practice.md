# Практичні завдання до Уроку 60.5: Колекції та структури даних

---

### Завдання 1: Парсинг та валідація JSON відповіді від API замовлень
**Мета:** Витягти вкладені дані з відповіді бекенду та перевірити їх.

**Вхідні дані:**
```python
order_payload = {
    "order_id": "ORD-9912",
    "customer": {
        "id": 401,
        "email": "customer@test.com"
    },
    "items": [
        {"name": "Клавіатура", "price": 45.0, "qty": 1},
        {"name": "Мишка", "price": 25.0, "qty": 2}
    ],
    "is_paid": True
}
```

**Що потрібно зробити:**
1. Перевірити, що замовлення оплачено (`is_paid == True`).
2. Витягти email клієнта.
3. Розрахувати загальну суму товарів з урахуванням кількості (`price * qty`).

**Приклад розв'язку:**
```python
order_payload = {
    "order_id": "ORD-9912",
    "customer": {
        "id": 401,
        "email": "customer@test.com"
    },
    "items": [
        {"name": "Клавіатура", "price": 45.0, "qty": 1},
        {"name": "Мишка", "price": 25.0, "qty": 2}
    ],
    "is_paid": True
}

# 1. Перевірка статусу оплати
assert order_payload["is_paid"] is True, "Замовлення має бути оплаченим!"

# 2. Email клієнта
customer_email = order_payload["customer"]["email"]
assert "@" in customer_email, "Email клієнта невалідний!"

# 3. Підрахунок суми
total_amount = sum(item["price"] * item["qty"] for item in order_payload["items"])
print(f"Загальна вартість: ${total_amount}")
assert total_amount == 95.0, f"Очікували суму $95.0, але отримали ${total_amount}"
```

---

### Завдання 2: Перевірка відсутності дублікатів у списку знайдених товарів
**Мета:** Написати перевірку унікальності ID в результатах пошуку.

**Вхідні дані:**
- `found_product_ids = [501, 502, 503, 504, 505]`

**Що потрібно зробити:**
1. Перетворити список `found_product_ids` у множину `set`.
2. За допомогою `assert` перевірити, що довжина списку дорівнює довжині множини.

**Приклад розв'язку:**
```python
found_product_ids = [501, 502, 503, 504, 505]

unique_ids = set(found_product_ids)

assert len(found_product_ids) == len(unique_ids), (
    f"Знайдено дублікати! Загальна кількість {len(found_product_ids)}, але унікальних лише {len(unique_ids)}"
)
print("✓ Всі ID товарів у пошуковій видачі є унікальними.")
```
