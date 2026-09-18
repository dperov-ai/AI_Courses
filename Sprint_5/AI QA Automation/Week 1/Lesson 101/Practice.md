# Практичні завдання до Уроку 101: Контекст для генерації automation-коду

## Мета практики
Опанувати формування повного контекстного пакета для AI, написання конфігураційного файлу автоматизації та генерацію тестів на основі Swagger/OpenAPI специфікації.

---

### Завдання 1. Створення файлу контексту автоматизації `.cursorrules` (Базовий рівень)

#### Опис завдання:
Створіть файл `.cursorrules` спеціально для автоматизатора на базі `Python + Playwright + Pytest + Allure`.

#### Зразок `.cursorrules`:
```markdown
# QA Automation Framework Guidelines (Python + Playwright)

## Core Stack:
- Python 3.12+, Pytest 8.x, Playwright 1.45+ (Sync API), Allure-pytest
- Pattern: Page Object Model (POM)

## Rules for Code Generation:
1. Always inherit from `BasePage` located in `pages/base_page.py`.
2. Locators MUST be defined inside `__init__` using `page.get_by_test_id()` or `page.get_by_role()`.
3. NEVER use absolute XPath or CSS classes containing random hashes.
4. NEVER use `time.sleep()`. Use Playwright auto-waiting and `expect(locator).to_be_visible()`.
5. Wrap key user actions in `@allure.step("...")` blocks.
6. Use pytest fixtures from `conftest.py` (`browser_page`, `authenticated_user`).
```

---

### Завдання 2. Генерація API автотесту за Swagger схемою (Прикладний рівень)

#### Вхідний фрагмент Swagger:
- **POST** `/api/v1/orders`
- **Headers**: `Authorization: Bearer <token>`, `Content-Type: application/json`
- **Request Body**: `{"item_id": int, "quantity": int, "promo_code": str | None}`
- **Responses**: `201 Created` (Order created), `400 Bad Request` (Invalid promo), `422 Unprocessable` (Quantity <= 0).

#### Інструкція:
Попросіть AI згенерувати набір із 3 параметризованих тестів на `httpx` / `pytest` з перевіркою валідації статус-кодів та схем.

---

### Завдання 3. Організація Teardown фікстури для очищення тестових даних (Робота з AI)

#### Опис завдання:
Попросіть AI згенерувати Pytest фікстуру `created_order`, яка створює тестове замовлення через API перед тестом, передає `order_id` у тест, а після завершення тесту (`yield`) викликає `DELETE /api/v1/orders/{id}`.

---

## Чекліст самоперевірки
- [ ] Створено структурований `.cursorrules` для QA Automation.
- [ ] Згенеровано параметризовані API тести за схемою Swagger.
- [ ] Реалізовано надійне очищення тестових сутностей у `yield` блоці фікстури.
