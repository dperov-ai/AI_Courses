# Практичні завдання до Уроку 61.8: Робота з AI для коду автотестів

---

### Завдання 1: Симуляція тріажу збою за допомогою структурованого аналізу
**Мета:** Навчитися аналізувати помилку та писати виправлений код.

**Ситуація:**
Тест авторизації впав із повідомленням:
`AssertionError: Expected status 200 but got 401. Response body: {"error": "Token expired"}`

**Проблемний код:**
```python
def test_get_user_profile():
    token = "old_expired_static_token"
    response = make_api_call(token)
    assert response["status"] == 200
```

**Що потрібно зробити:**
1. Описати першопричину збою (Root Cause).
2. Написати виправлену версію, яка спочатку генерує свіжий токен перед викликом профілю.

**Приклад розв'язку:**
```python
# 1. Root Cause: Використання захардкодженого застарілого токена замість динамічної авторизації перед тестом.

# 2. Виправлений код:
def get_fresh_auth_token() -> str:
    # Симуляція отримання свіжого токена через логін:
    return "fresh_active_jwt_token_2026"

def test_get_user_profile_fixed():
    # Arrange
    auth_token = get_fresh_auth_token()
    
    # Act
    # Симулюємо успішну відповідь з валідним токеном:
    response = {"status": 200, "user": {"id": 1, "name": "Alex"}}
    
    # Assert
    assert response["status"] == 200, f"Очікували 200, але отримали {response['status']}"
    assert "user" in response, "Відповідь не містить даних користувача!"
    print("✓ Тест успішно виправлено та пройдено!")

test_get_user_profile_fixed()
```

---

### Завдання 2: Складання ефективного промпту для створення Page Object
**Мета:** Скласти повний запит до AI для генерації класу сторінки реєстрації.

**Що потрібно зробити:**
Скласти текст промпту, який містить:
1. Роль та технологічний стек (`Python + Playwright`).
2. HTML-структуру форми (поля: `username`, `email`, `password`, `repeat_password`, кнопка `Register`).
3. Вимогу створити окремі методи введення та бізнес-метод `register_new_user()`.

**Приклад розв'язку (Текст промпту):**
```markdown
Дій як провідний QA Automation Engineer.
Напиши чистий клас Page Object на Python для фреймворку Playwright.

Сторінка реєстрації містить наступні селектори:
- Поле введення імені: `#reg-username`
- Поле введення пошти: `#reg-email`
- Поле введення пароля: `#reg-password`
- Підтвердження пароля: `#reg-confirm-password`
- Кнопка підтвердження: `button[data-testid='submit-reg']`
- Банер успіху: `.registration-success-message`

Вимоги до класу:
1. Метод `__init__(self, page)` зберігає посилання на сторінку.
2. Методи для взаємодії з кожним полем окремо.
3. Комплексний метод `register_user(username, email, password)` з автозаповненням усіх полів та кліком.
4. Додай повну типізацію (Type Hints) та стислі Docstrings.
```
