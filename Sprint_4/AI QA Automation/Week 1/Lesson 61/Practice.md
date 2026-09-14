# Практичні завдання до Уроку 61: Структура автоматизованого тесту

## Мета практики
Опанувати стандартну архітектуру автоматизованого тесту за патерном **AAA (Arrange - Act - Assert)** або **Given - When - Then**, навчитися писати незалежні та детерміновані автотести, правильно організовувати фікстури ініціалізації та очищення даних (Teardown).

---

### Завдання 1. Реалізація першого тесту за патерном AAA на Python (Базовий рівень)

#### Опис завдання:
Напишіть тестову функцію на Python, яка тестує функціонал розрахунку балансу клієнта після здійснення покупки, чітко розмежувавши три фази: `Arrange`, `Act`, `Assert`.

#### Зразок розв'язку:
```python
def process_purchase(initial_balance: float, item_price: float) -> float:
    if item_price > initial_balance:
        raise ValueError("Недостатньо коштів на рахунку")
    return initial_balance - item_price

def test_purchase_success():
    # 1. ARRANGE (Підготовка початкового стану та тестових даних)
    starting_balance = 500.0
    item_cost = 150.0
    expected_balance = 350.0

    # 2. ACT (Виконання цільової дії, що тестується)
    actual_balance = process_purchase(starting_balance, item_cost)

    # 3. ASSERT (Перевірка очікуваного результату)
    assert actual_balance == expected_balance, f"Очікували {expected_balance}, але отримали {actual_balance}"
    print("✅ Тест test_purchase_success успішно пройдено!")

def test_purchase_insufficient_funds():
    # ARRANGE
    starting_balance = 100.0
    item_cost = 200.0

    # ACT & ASSERT (Перевірка генерації винятку)
    try:
        process_purchase(starting_balance, item_cost)
        assert False, "Очікували ValueError, але функція виконалася без помилок"
    except ValueError as e:
        assert str(e) == "Недостатньо коштів на рахунку"
        print("✅ Тест test_purchase_insufficient_funds успішно перехопив очікуваний виняток!")

# Запуск тестів
test_purchase_success()
test_purchase_insufficient_funds()
```

---

### Завдання 2. Організація Setup та Teardown через контекстний менеджер або фікстуру (Прикладний рівень)

#### Опис завдання:
Напишіть тест, який створює тимчасовий тестовий файл, записує в нього дані, перевіряє їх коректність та обов'язково видаляє файл після завершення тесту (навіть якщо перевірка впаде).

#### Зразок розв'язку:
```python
import os

def write_and_read_config(file_path: str, content: str) -> str:
    with open(file_path, "w", encoding="utf-8") as f:
        f.write(content)
    with open(file_path, "r", encoding="utf-8") as f:
        return f.read()

def test_temporary_file_lifecycle():
    temp_filename = "test_environment.tmp"
    
    # SETUP
    if os.path.exists(temp_filename):
        os.remove(temp_filename)
        
    try:
        # ARRANGE & ACT
        payload = "ENV=TESTING;PORT=8000"
        result = write_and_read_config(temp_filename, payload)
        
        # ASSERT
        assert result == payload, "Вміст файлу не збігається з переданим"
        print("✅ Тест файлових операцій пройдено!")
    finally:
        # TEARDOWN (Обов'язкове очищення)
        if os.path.exists(temp_filename):
            os.remove(temp_filename)
            print("🧹 Teardown: Тимчасовий тестовий файл успішно видалено.")

test_temporary_file_lifecycle()
```

---

### Завдання 3. AI-аудит якості та чистоти автотесту (Робота з AI)

#### Опис завдання:
Надайте AI тест із типовими «запахами» (Test Smells): відсутність фази Arrange, численні непов'язані асерти, хардкод очікувань, відсутність Teardown. Отримайте від AI перероблений чистий тест.

#### Промпт для AI:
> «Проведи рев'ю цього автотесту на наявність Test Smells: 1) Чи дотримано патерн AAA? 2) Чи є тест атомарним і незалежним? 3) Як покращити асерти та обробку очищення даних? Напиши виправлену версію за стандартами Pytest: [вставити код тесту]»

---

## Чекліст самоперевірки
- [ ] Тести структуровані за блоками Arrange / Act / Assert.
- [ ] Реалізовано перевірку як позитивних результатів, так і очікуваних винятків.
- [ ] Забезпечено гарантоване очищення тестових ресурсів у блоці `finally`.
