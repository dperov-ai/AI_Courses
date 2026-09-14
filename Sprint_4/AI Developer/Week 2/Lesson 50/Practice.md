# Практичні завдання до Уроку 50: Обробка помилок

## Мета практики
Опанувати надійну обробку виняткових ситуацій за допомогою блоків `try`, `except`, `else`, `finally`, роботу зі специфічними типами винятків (`ValueError`, `KeyError`, `FileNotFoundError`) та генерацію власних помилок через `raise`.

---

### Завдання 1. Безпечний парсер конфігураційного словника (Базовий рівень)

#### Опис завдання:
Створіть функцію `parse_port_config`, яка отримує словник із конфігурацією та витягує числовий номер порту. Необхідно перехоплювати відсутність ключа (`KeyError`) та некоректне значення, яке не можна перетворити на ціле число (`ValueError`).

#### Інструкція:
1. Якщо ключ `'port'` відсутній — повертати порт за замовчуванням `8080`.
2. Якщо значення не є валідним числом — повертати значення за замовчуванням та виводити попередження.

#### Зразок розв'язку:
```python
def parse_port_config(config: dict) -> int:
    default_port = 8080
    try:
        raw_port = config["port"]
        port = int(raw_port)
        if not (1 <= port <= 65535):
            raise ValueError(f"Порт {port} поза діапазоном 1-65535")
        return port
    except KeyError:
        print(f"ℹ️ Ключ 'port' відсутній. Використано порт за замовчуванням: {default_port}")
        return default_port
    except ValueError as e:
        print(f"⚠️ Некоректне значення порту: {e}. Використано порт: {default_port}")
        return default_port

# Тестові виклики
print(parse_port_config({"port": "3000"}))    # 3000
print(parse_port_config({}))                   # 8080 (KeyError)
print(parse_port_config({"port": "invalid"})) # 8080 (ValueError)
print(parse_port_config({"port": "99999"}))   # 8080 (Діапазон)
```

---

### Завдання 2. Надійна робота з файловими операціями та блок finally (Прикладний рівень)

#### Опис завдання:
Напишіть функцію читання текстового файлу логів з гарантованим виведенням статусу завершення операції через `finally`.

#### Зразок розв'язку:
```python
def read_log_file(file_path: str) -> list[str]:
    lines = []
    try:
        print(f"Спроба відкриття файлу: {file_path}")
        with open(file_path, "r", encoding="utf-8") as file:
            lines = [line.strip() for line in file.readlines()]
        print(f"✅ Успішно прочитано {len(lines)} рядків.")
    except FileNotFoundError:
        print(f"❌ Помилка: Файл '{file_path}' не знайдено на диску.")
    except PermissionError:
        print(f"❌ Помилка: Відсутні права доступу для читання файлу '{file_path}'.")
    except Exception as general_error:
        print(f"🚨 Непередбачена помилка: {general_error}")
    finally:
        print("🔒 Завершення блоку читання файлу (cleanup stage).")
    return lines

# Перевірка на неіснуючому файлі
result = read_log_file("missing_application.log")
```

---

### Завдання 3. AI для аналізу Traceback помилок (Робота з AI)

#### Опис завдання:
Візьміть фрагмент реального Traceback повідомлення про помилку Python та попросіть AI провести Root Cause Analysis (визначити першопричину) і надати готове виправлення.

#### Промпт для AI:
> «Проаналізуй цей Traceback помилки Python. Поясни простою мовою: 1) Що саме пішло не так? 2) На якому рядку виник збій? 3) Як виправити код, щоб уникнути падіння програми в майбутньому? Ось текст помилки: [вставити Traceback]»

---

## Чекліст самоперевірки
- [ ] Оброблено конкретні типи винятків (`KeyError`, `ValueError`), уникаючи порожнього `except:`.
- [ ] Продемонстровано роботу блоку `finally` для очищення ресурсів.
- [ ] Використано власну валідацію через `raise ValueError`.
