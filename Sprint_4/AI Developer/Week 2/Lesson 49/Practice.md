# Практичні завдання до Уроку 49: Класи та об’єкти

## Мета практики
Опанувати базові принципи об'єктно-орієнтованого програмування (ООП) на Python: створення класів, конструктора `__init__`, методів екземпляра, інкапсуляції та взаємодії між об'єктами.

---

### Завдання 1. Створення класу банківського рахунку (BankAccount) (Базовий рівень)

#### Опис завдання:
Створіть клас `BankAccount`, що інкапсулює баланс, номер рахунку та ім'я власника. Реалізуйте методи поповнення (`deposit`), зняття коштів (`withdraw`) та отримання виписки.

#### Інструкція:
1. Захистіть баланс від від'ємних значень.
2. Метод `withdraw` повинен перевіряти наявність достатньої суми коштів.
3. Реалізуйте метод `__str__` для зручного відображення інформації про рахунок.

#### Зразок розв'язку:
```python
class BankAccount:
    def __init__(self, account_number: str, owner: str, initial_balance: float = 0.0):
        self.account_number = account_number
        self.owner = owner
        self._balance = max(0.0, initial_balance)

    def deposit(self, amount: float) -> bool:
        if amount <= 0:
            print("⚠️ Сума поповнення має бути більшою за 0.")
            return False
        self._balance += amount
        print(f"✅ Рахунок {self.account_number} поповнено на {amount:.2f} грн. Баланс: {self._balance:.2f} грн.")
        return True

    def withdraw(self, amount: float) -> bool:
        if amount <= 0:
            print("⚠️ Сума зняття має бути більшою за 0.")
            return False
        if amount > self._balance:
            print(f"❌ Недостатньо коштів на рахунку {self.account_number}. Доступно: {self._balance:.2f} грн.")
            return False
        self._balance -= amount
        print(f"💸 Знято {amount:.2f} грн. Залишок: {self._balance:.2f} грн.")
        return True

    def get_balance(self) -> float:
        return self._balance

    def __str__(self) -> str:
        return f"Рахунок #{self.account_number} | Власник: {self.owner} | Баланс: {self._balance:.2f} грн"

# Тестування класу
acc = BankAccount("UA12345678", "Олексій Коваленко", 1000.0)
acc.deposit(500.0)
acc.withdraw(2000.0)  # Спроба зняти більше
acc.withdraw(300.0)   # Успішне зняття
print(acc)
```

---

### Завдання 2. Модель каталогу товарів інтернет-магазину (Прикладний рівень)

#### Опис завдання:
Створіть два класи: `Product` (товар з назвою, ціною, артикулом) та `ShoppingCart` (кошик покупок, що містить список товарів та вміє рахувати сумарну вартість).

#### Зразок розв'язку:
```python
class Product:
    def __init__(self, sku: str, title: str, price: float):
        self.sku = sku
        self.title = title
        self.price = price

class ShoppingCart:
    def __init__(self):
        self.items: list[Product] = []

    def add_product(self, product: Product) -> None:
        self.items.append(product)
        print(f"Додано в кошик: {product.title} ({product.price:.2f} грн)")

    def calculate_total(self) -> float:
        return sum(item.price for item in self.items)

    def print_receipt(self) -> None:
        print("--- Чек кошика ---")
        for p in self.items:
            print(f"- {p.title} [{p.sku}]: {p.price:.2f} грн")
        print(f"Всього до сплати: {self.calculate_total():.2f} грн")

# Використання
cart = ShoppingCart()
cart.add_product(Product("SKU-01", "Ноутбук", 32000.0))
cart.add_product(Product("SKU-02", "Мишка бездротова", 650.0))
cart.print_receipt()
```

---

### Завдання 3. AI-генератор структури класів та рефакторинг ООП (Робота з AI)

#### Опис завдання:
Сформулюйте для AI завдання спроєктувати систему класів для сервісу бронювання готелів (Room, Booking, User) із дотриманням принципів SOLID.

#### Промпт для AI:
> «Спроєктуй на Python систему класів для бронювання номерів готелю: Room (номер), Guest (гість), BookingService (сервіс бронювання). Додай методи перевірки доступності номерів на дати та розрахунку вартості. Напиши лаконічний і зрозумілий код.»

---

## Чекліст самоперевірки
- [ ] Створено класи з коректним конструктором `__init__`.
- [ ] Реалізовано перевірку стану та валідацію в методах (`withdraw`).
- [ ] Опрацьовано композицію об'єктів (кошик містить товари).
