# Практичні завдання до Уроку 102: Генерація коду за тестовим сценарієм

## Мета практики
Опанувати трансляцію текстових сценаріїв у чистий код Playwright/Pytest, тестування роботи з модальними вікнами, вкладками та Drag & Drop.

---

### Завдання 1. Трансляція Gherkin-сценарію у Playwright тест (Базовий рівень)

#### Вхідний сценарій:
```gherkin
Scenario: Успішне додавання товару в кошик та перевірка лічильника
  Given Користувач знаходиться на сторінці каталогу товарів
  When Натискає кнопку "Додати в кошик" біля першого товару
  Then Лічильник товарів у шапці сайту показує цифру "1"
  And З'являється спливаюче сповіщення "Товар успішно додано"
```

#### Зразок коду автотесту:
```python
import pytest
from playwright.sync_api import Page, expect

def test_add_product_to_cart_success(page: Page):
    # GIVEN
    page.goto("https://shop.test.com/catalog")
    
    # WHEN
    first_product_card = page.locator(".product-card").first
    add_btn = first_product_card.get_by_role("button", name="Додати в кошик")
    add_btn.click()
    
    # THEN
    cart_badge = page.get_by_test_id("cart-badge-count")
    expect(cart_badge).to_have_text("1")
    
    toast_notification = page.get_by_role("status")
    expect(toast_notification).to_be_visible()
    expect(toast_notification).to_contain_text("Товар успішно додано")
```

---

### Завдання 2. Генерація тесту перемикання між вкладками браузера (Прикладний рівень)

#### Опис завдання:
Попросіть AI згенерувати тест, який клікає по посиланню «Оферта (відкриється в новій вкладці)», перехоплює нову сторінку через `context.expect_page()`, перевіряє заголовок H1 на новій вкладці та закриває її.

---

### Завдання 3. AI-генерація тесту перетягування елементів (Drag & Drop) (Робота з AI)

#### Опис завдання:
Згенеруйте автотест перетягування картки завдання з колонки «To Do» в колонку «Done» на інтерактивній дошці Trello-like інтерфейсу.

---

## Чекліст самоперевірки
- [ ] Gherkin сценарій повністю перетворено в працюючий автотест.
- [ ] Застосовано Web-first assertions `expect()`.
- [ ] Опрацьовано перехоплення нової вкладки або Drag & Drop операції.
