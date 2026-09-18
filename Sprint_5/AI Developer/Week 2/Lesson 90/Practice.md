# Практичні завдання: Урок 90 — Пояснення незнайомого коду за допомогою AI

## Завдання 1: Глибокий реверс-інжиніринг алгоритму зі складністю Big-O
**Мета**: Отримати від AI багаторівневий технічний аудит заплутаного алгоритмічного фрагмента коду з розрахунком часової та просторової складності.

### Вихідний код для аналізу:
```python
def process_data_pipeline(records, lookup_table):
    results = []
    for item in records:
        matched = None
        for entry in lookup_table:
            if item.get("category_id") == entry.get("id"):
                if entry.get("is_active") and item.get("score", 0) > entry.get("threshold", 0):
                    matched = {
                        "user_id": item["user_id"],
                        "effective_score": item["score"] * entry["multiplier"],
                        "category_name": entry["name"]
                    }
                    break
        if matched:
            results.append(matched)
    return results
```

### Інструкція для студента:
1. Складіть промпт до AI з вимогою проаналізувати:
   - Бізнес-призначення функції.
   - Часову складність $O(...)$ та просторову складність.
   - Вузькі місця продуктивності при збільшенні розміру списків до $10^5$ записів.
2. Попросіть AI запропонувати оптимізований варіант алгоритму зі складністю $O(N + M)$ за допомогою хеш-таблиці.

---

## Завдання 2: Генерація візуальної діаграми послідовності (Mermaid Sequence Diagram)
**Мета**: Візуалізувати логіку взаємодії асинхронного сервісу з базою даних та чергою повідомлень.

### Вихідний код:
```python
async def handle_order_checkout(order_id, user_id, payment_gateway, event_bus, db_session):
    async with db_session.begin():
        order = await db_session.get(Order, order_id)
        if not order or order.status != "DRAFT":
            raise InvalidOrderStateError()
        
        charge_result = await payment_gateway.charge(user_id, order.total_amount)
        if not charge_result.success:
            order.status = "PAYMENT_FAILED"
            await event_bus.publish("order.failed", {"order_id": order_id})
            return False
            
        order.status = "PAID"
        await db_session.flush()
        await event_bus.publish("order.paid", {"order_id": order_id, "amount": str(order.total_amount)})
        return True
```

### Інструкція для студента:
1. Надішліть код у AI з промптом: *"Згенеруй детальну Mermaid Sequence Diagram, що відображає як позитивний сценарій (Happy Path), так і сценарій відхилення оплати"*.
2. Перевірте згенеровану діаграму в живому переглядачі Mermaid.
