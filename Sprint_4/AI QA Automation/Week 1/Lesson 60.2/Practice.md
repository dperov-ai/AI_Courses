# Практичні завдання до Уроку 60.2: Умовні конструкції

---

### Завдання 1: Класифікатор статус-кодів HTTP
**Мета:** Написати умовний блок для визначення категорії відповіді веб-сервера.

**Вхідні дані:**
- `status_code = 201`

**Що потрібно зробити:**
1. Якщо статус від 200 до 299 — вивести `"Success Response"`.
2. Якщо статус від 400 до 499 — вивести `"Client Error"`.
3. Якщо статус від 500 до 599 — вивести `"Server Error"`.
4. В інших випадках — вивести `"Unknown Category"`.

**Приклад розв'язку:**
```python
status_code = 201

if 200 <= status_code <= 299:
    category = "Success Response"
elif 400 <= status_code <= 499:
    category = "Client Error"
elif 500 <= status_code <= 599:
    category = "Server Error"
else:
    category = "Unknown Category"

print(f"Статус {status_code}: {category}")
assert category == "Success Response", "Очікували успішну категорію!"
```

---

### Завдання 2: Валідатор прав доступу користувача
**Мета:** Написати перевірку можливості редагування статті в CMS.

**Вхідні дані:**
- `user_role = "editor"`
- `is_article_published = False`

**Що потрібно зробити:**
- Дозволити редагування (`can_edit = True`), якщо:
  1. Користувач є `"admin"`, АБО
  2. Користувач є `"editor"` І стаття ще НЕ опублікована (`is_article_published == False`).

**Приклад розв'язку:**
```python
user_role = "editor"
is_article_published = False

if user_role == "admin" or (user_role == "editor" and not is_article_published):
    can_edit = True
else:
    can_edit = False

print(f"Дозвіл на редагування: {can_edit}")
assert can_edit is True, "Редактор повинен мати доступ до неопублікованої статті!"
```

---

### Завдання 3: Перевірка наявності помилки в тексті відповіді
**Мета:** Перевірити текст відповіді від API авторизації.

**Вхідні дані:**
- `api_response = "Error: Invalid credentials provided. Please try again."`

**Що потрібно зробити:**
1. За допомогою оператора `in` перевірити, чи містить `api_response` фразу `"Invalid credentials"`.
2. Якщо містить — створити змінну `test_passed = True`.
3. Додати фінальний `assert test_passed is True`.

**Приклад розв'язку:**
```python
api_response = "Error: Invalid credentials provided. Please try again."

if "Invalid credentials" in api_response:
    test_passed = True
    print("Текст помилки відповідає специфікації.")
else:
    test_passed = False
    print("Помилка не знайдена!")

assert test_passed is True, "У відповіді відсутнє очікуване повідомлення про невірні облікові дані!"
```
