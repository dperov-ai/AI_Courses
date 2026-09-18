# Практичні завдання до Уроку 87: Використання AI для створення коду

## Мета практики
Опанувати написання та структурування конфігураційного файлу `.cursorrules`, створення системних промптів для генерації коду з Type Hints та безпечне використання AI-асистентів без витоку секретів.

---

### Завдання 1. Створення файлу `.cursorrules` для проєкту (Базовий рівень)

#### Опис завдання:
Створіть файл `.cursorrules` для сучасного Python/FastAPI бекенд-проєкту, який змушує AI дотримуватися стандартів чистого коду, PEP 8, типізації `pydantic v2` та асинхронності.

#### Зразок `.cursorrules`:
```markdown
# Corporate Cursor Rules for Python Backend

## Tech Stack & Architecture:
- Language: Python 3.12+
- Framework: FastAPI (Async routes)
- ORM / DB: SQLAlchemy 2.0 (Async session) + PostgreSQL
- Validation: Pydantic v2 (Use BaseModel, ConfigDict)
- Testing: Pytest + pytest-asyncio + httpx

## Code Style & Guidelines:
1. Always use strict Type Annotations for all function arguments and return types.
2. Never use naked `except:` blocks; always catch specific exceptions (e.g., `HTTPException`, `ValueError`).
3. Follow SOLID principles and Dependency Injection (FastAPI `Depends`).
4. Avoid deprecated Pydantic v1 methods (e.g., use `model_validate()` instead of `from_orm()`).
5. Write docstrings in Google Format for all public service methods.
6. Keep files modular (< 300 lines); separate schemas, models, services, and routers.
```

---

### Завдання 2. Генерація CRUD-сервісу з повною типізацією (Прикладний рівень)

#### Опис завдання:
Складіть запит до AI для генерації асинхронного FastAPI сервісу управління товарами інтернет-магазину (Products) з валідацією через Pydantic.

#### Промпт для AI:
> «Створи повноцінний CRUD роутер на FastAPI для сутності Product (id, title, sku, price, stock_quantity, created_at). Вимоги:
> 1. Pydantic схеми: ProductCreate, ProductUpdate, ProductResponse.
> 2. Асинхронні ендпоінти: GET /products (з пагінацією limit/offset), GET /products/{id}, POST /products, PUT /products/{id}, DELETE /products/{id}.
> 3. Обробка помилки 404 Not Found, якщо товар не знайдено.
> 4. Коректні HTTP статус-коди (201 Created для POST, 204 No Content для DELETE).»

---

### Завдання 3. Аудит безпеки коду перед комітом (Робота з AI)

#### Опис завдання:
Попросіть AI провести перевірку фрагмента коду на наявність SQL Injection, хардкоду секретів та вразливостей авторизації.

---

## Чекліст самоперевірки
- [ ] Створено структурований `.cursorrules` із зазначенням версій та правил.
- [ ] Згенеровано асинхронний FastAPI сервіс із повним покриттям типів.
- [ ] Перевірено відсутність секретних ключів у файлах коду.
