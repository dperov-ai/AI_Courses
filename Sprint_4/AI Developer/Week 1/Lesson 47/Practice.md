# Практичні завдання до Уроку 47: Функції та параметри

## Мета практики
Опанувати створення модульних функцій у Python, роботу з позиційними та іменованими аргументами, параметрами за замовчуванням, значеннями, що повертаються (`return`), та типами підказок (Type Hinting).

---

### Завдання 1. Створення набору розрахункових утиліт (Базовий рівень)

#### Опис завдання:
Напишіть функцію `calculate_metrics`, яка приймає загальну кількість запитів та кількість помилок, а повертає словник із показником Error Rate (%) та Success Rate (%).

#### Інструкція:
1. Використовуйте Type Hinting для параметрів та типу повернення.
2. Передбачте перевірку ділення на 0 (якщо total_requests == 0).
3. Додайте docstring з описом призначення функції.

#### Зразок розв'язку:
```python
def calculate_metrics(total_requests: int, failed_requests: int) -> dict[str, float]:
    """Розраховує відсоток успішних та помилкових запитів.
    
    :param total_requests: Загальна кількість запитів
    :param failed_requests: Кількість помилкових запитів
    :return: Словник з відсотками error_rate та success_rate
    """
    if total_requests <= 0:
        return {"error_rate": 0.0, "success_rate": 100.0}
    
    error_rate = (failed_requests / total_requests) * 100
    success_rate = 100.0 - error_rate
    
    return {
        "error_rate": round(error_rate, 2),
        "success_rate": round(success_rate, 2)
    }

# Перевірка роботи
stats = calculate_metrics(total_requests=250, failed_requests=5)
print(f"Статистика роботи системи: {stats}")
```

---

### Завдання 2. Гнучка функція формування конфігурації з *args та **kwargs (Прикладний рівень)

#### Опис завдання:
Створіть функцію `build_connection_string`, яка приймає обов'язкові параметри (`host`, `port`) та будь-яку кількість додаткових параметрів підключення через `**kwargs` (наприклад, `timeout`, `ssl`, `user`).

#### Зразок розв'язку:
```python
def build_connection_string(host: str, port: int = 5432, **options) -> str:
    base_url = f"postgresql://{host}:{port}"
    if not options:
        return base_url
    
    query_params = [f"{key}={value}" for key, value in options.items()]
    return f"{base_url}?{'&'.join(query_params)}"

# Виклики функції з різними параметрами
url_1 = build_connection_string("localhost")
url_2 = build_connection_string("db.production.internal", port=5433, ssl="true", timeout=30)

print(f"URL 1: {url_1}")
print(f"URL 2: {url_2}")
```

---

### Завдання 3. AI-генерація docstrings та валідації типів (Робота з AI)

#### Опис завдання:
Надайте AI функцію без документації та попросіть згенерувати стандартний Google/NumPy Docstring, валідацію вхідних аргументів та юніт-тести.

#### Промпт для AI:
> «Для наданої функції на Python згенеруй повний Docstring у форматі Google Style, додай перевірку типів аргументів через raise TypeError/ValueError при некоректних даних та напиши 3 приклади перевірочних викликів з різними аргументами: [вставити код]»

---

## Чекліст самоперевірки
- [ ] Функції повертають значення через `return`, а не просто виводять у `print`.
- [ ] Використано Type Hints для аргументів та значення повернення.
- [ ] Опрацьовано випадок ділення на нуль або порожніх параметрів.
