# Практичні завдання до Уроку 100: AI у QA Automation

## Мета практики
Опанувати генерацію Page Object класів для Playwright за HTML-фрагментом, створення стійких селекторів за ARIA-ролями та налаштування фікстур Pytest за допомогою AI.

---

### Завдання 1. Генерація Page Object класу на Playwright / Python (Базовий рівень)

#### Вхідний HTML-фрагмент форми входу:
```html
<form id="login-form">
  <input type="email" data-testid="email-input" placeholder="Введіть email" />
  <input type="password" data-testid="password-input" placeholder="Введіть пароль" />
  <button type="submit" role="button" aria-label="Увійти в кабінет">Увійти</button>
  <div class="error-message" role="alert" style="display:none;">Невірний логін або пароль</div>
</form>
```

#### Інструкція:
За допомогою AI згенеруйте клас `LoginPage` на Playwright (Python async/sync), що інкапсулює локатори за `data-testid` / `get_by_role` та методи:
- `navigate()`
- `login(email, password)`
- `get_error_text()`

#### Зразок класу:
```python
from playwright.sync_api import Page, Locator

class LoginPage:
    def __init__(self, page: Page):
        self.page = page
        self.email_input: Locator = page.get_by_test_id("email-input")
        self.password_input: Locator = page.get_by_test_id("password-input")
        self.submit_btn: Locator = page.get_by_role("button", name="Увійти в кабінет")
        self.error_banner: Locator = page.get_by_role("alert")

    def navigate(self, url: str = "https://app.test.com/login"):
        self.page.goto(url)

    def login(self, email: str, password: str):
        self.email_input.fill(email)
        self.password_input.fill(password)
        self.submit_btn.click()

    def get_error_message(self) -> str:
        return self.error_banner.inner_text()
```

---

### Завдання 2. Генерація автотесту з Allure-кроками (Прикладний рівень)

#### Опис завдання:
Попросіть AI згенерувати повноцінний тест `test_login_invalid_credentials` на базі `LoginPage` із використанням фікстури Pytest та розміткою кроків `@allure.step`.

---

### Завдання 3. AI-оптимізація селекторів (Робота з AI)

#### Опис завдання:
Подайте до AI фрагмент складної динамічної таблиці (React Grid) та отримайте надійний селектор для кліку по кнопці «Редагувати» у рядку з користувачем «Іван Франко».

---

## Чекліст самоперевірки
- [ ] Створено повноцінний Page Object клас без хардкоду XPath.
- [ ] Використано стійкі семантичні селектори Playwright.
- [ ] Тест структуровано з використанням Allure steps та фікстур.
