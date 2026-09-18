# Практичні завдання до Уроку 103: Пояснення існуючого коду за допомогою AI

## Мета заняття
Опанувати навички використання сучасних LLM для швидкої деконструкції, аналізу логіки та виявлення прихованих недоліків у заплутаних модулях автоматизації тестування.

---

### Завдання 1. Аналіз заплутаного модуля API-клієнта (Базовий рівень)

#### Вхідний код:
```python
import requests
import time

class LegacyApiClient:
    def __init__(self, base_url, api_key):
        self.base_url = base_url
        self.headers = {"X-API-KEY": api_key, "Content-Type": "application/json"}

    def execute_with_retry(self, method, endpoint, data=None, max_retries=3, backoff_factor=1.5):
        url = f"{self.base_url.rstrip('/')}/{endpoint.lstrip('/')}"
        attempt = 0
        while attempt < max_retries:
            try:
                response = requests.request(method, url, headers=self.headers, json=data, timeout=10)
                if response.status_code in [200, 201, 204]:
                    return response.json() if response.content else {}
                elif response.status_code in [429, 502, 503, 504]:
                    attempt += 1
                    sleep_time = backoff_factor ** attempt
                    time.sleep(sleep_time)
                else:
                    response.raise_for_status()
            except requests.exceptions.RequestException as e:
                attempt += 1
                if attempt >= max_retries:
                    raise RuntimeError(f"API request failed after {max_retries} attempts: {e}")
                time.sleep(backoff_factor ** attempt)
        raise TimeoutError("Max retries exceeded")
```

#### Завдання:
1. Подайте цей код до Claude або ChatGPT з промптом: «Поясни покроково логіку механізму Retry, експоненційного відкату (Exponential Backoff) та обробки статус-кодів. Створи Mermaid діаграму послідовності».
2. Збережіть отриманий опис у вигляді markdown документа.

---

### Завдання 2. Виявлення ризиків Flakiness у UI тесті (Прикладний рівень)

#### Вхідний сценарій:
Знайдіть у коді тестування кошика інтернет-магазину приховану проблему Race Condition між появою кнопки «Оформити замовлення» та завантаженням списку товарів з бекенду.

---

### Завдання 3. Генерація Google Style Docstrings для легасі хелпера (Робота з AI)

#### Завдання:
Згенеруйте повну документацію docstring для класу `LegacyApiClient` із зазначенням типів аргументів, значень за замовчуванням та винятків (`Raises`).

---

## Чекліст самоперевірки
- [ ] Отримано покрокове пояснення механізму повторних спроб (Retry Mechanism).
- [ ] Згенеровано коректну діаграму послідовності Mermaid.
- [ ] Ідентифіковано статус-коди, що викликають перезапит (429, 502, 503, 504).
