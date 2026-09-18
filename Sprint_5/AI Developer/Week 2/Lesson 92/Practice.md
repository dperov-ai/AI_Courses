# Практичні завдання до Уроку 92: Рефакторинг та покращення коду

## Мета практики
Опанувати усунення вкладеності за допомогою Guard Clauses, заміну магічних чисел на Enum та декомпозицію монолітних функцій за допомогою AI.

---

### Завдання 1. Рефакторинг «Піраміди вкладеності» через Guard Clauses (Базовий рівень)

#### Заплутаний вхідний код:
```python
def process_user_registration(user_data):
    if user_data is not None:
        if "email" in user_data:
            if "@" in user_data["email"]:
                if "age" in user_data:
                    if user_data["age"] >= 18:
                        # Основна дія
                        return {"status": "SUCCESS", "message": "User registered"}
                    else:
                        return {"status": "ERROR", "message": "User is underage"}
                else:
                    return {"status": "ERROR", "message": "Age is missing"}
            else:
                return {"status": "ERROR", "message": "Invalid email"}
        else:
            return {"status": "ERROR", "message": "Email is missing"}
    else:
        return {"status": "ERROR", "message": "No data provided"}
```

#### Інструкція:
Попросіть AI переписати цю функцію з використанням **Guard Clauses** та типізації, знизивши вкладеність до 1 рівня.

#### Зразок чистого рефакторингу:
```python
def process_user_registration(user_data: dict | None) -> dict[str, str]:
    """Чиста реєстрація користувача з використанням патерну Guard Clauses."""
    if not user_data:
        return {"status": "ERROR", "message": "No data provided"}
        
    email = user_data.get("email")
    if not email:
        return {"status": "ERROR", "message": "Email is missing"}
        
    if "@" not in email:
        return {"status": "ERROR", "message": "Invalid email"}
        
    age = user_data.get("age")
    if age is None:
        return {"status": "ERROR", "message": "Age is missing"}
        
    if age < 18:
        return {"status": "ERROR", "message": "User is underage"}
        
    # Основний успішний потік без зайвих відступів
    return {"status": "SUCCESS", "message": "User registered"}
```

---

### Завдання 2. Заміна Magic Strings на Enum та Pydantic-модель (Прикладний рівень)

#### Опис завдання:
Перетворіть нетипізований словник зі статусами замовлень (`"pending"`, `"paid"`, `"shipped"`, `"refunded"`) у типізований Python `Enum` та валідуйте через Pydantic.

---

### Завдання 3. AI-декомпозиція 100-рядкового монолітного методу (Робота з AI)

#### Опис завдання:
Подайте до AI великий метод обробки фінансової транзакції та попросіть декомнизувати його на 3 чисті функції: `validate_funds()`, `apply_discounts()`, `commit_transaction()`.

---

## Чекліст самоперевірки
- [ ] Повністю ліквідовано вкладені блоки `if-else`.
- [ ] Магічні рядки замінено на строгий `Enum`.
- [ ] Всі тести залишаються працездатними після рефакторингу.
