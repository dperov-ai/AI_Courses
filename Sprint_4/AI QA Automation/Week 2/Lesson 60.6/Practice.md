# Практичні завдання до Уроку 60.6: Класи та об’єкти

---

### Завдання 1: Створення класу API-клієнта (API Client Model)
**Мета:** Написати базовий клас для роботи з ендпоінтами сервісу користувачів.

**Що потрібно зробити:**
1. Створити клас `UserApiClient`.
2. Конструктор `__init__(self, base_url, auth_token)` повинен зберігати базовий URL та токен.
3. Метод `get_headers(self)` повинен повертати словник `{"Authorization": f"Bearer {self.auth_token}"}`.
4. Метод `get_user_url(self, user_id)` повинен повертати `f"{self.base_url}/users/{user_id}"`.

**Приклад розв'язку:**
```python
class UserApiClient:
    def __init__(self, base_url: str, auth_token: str):
        self.base_url = base_url.rstrip("/")
        self.auth_token = auth_token

    def get_headers(self) -> dict:
        return {
            "Authorization": f"Bearer {self.auth_token}",
            "Accept": "application/json"
        }

    def get_user_url(self, user_id: int) -> str:
        return f"{self.base_url}/users/{user_id}"

# Тестуємо клас:
client = UserApiClient("https://api.demostore.com", "my_token_99")
headers = client.get_headers()
url = client.get_user_url(42)

print("Headers:", headers)
print("URL:", url)

assert headers["Authorization"] == "Bearer my_token_99"
assert url == "https://api.demostore.com/users/42"
```

---

### Завдання 2: Page Object для форми пошуку
**Мета:** Створити модель компонента пошуку `SearchComponent`.

**Що потрібно зробити:**
1. Клас повинен містити локатори `search_input_selector = "#search"` та `submit_btn_selector = "#search-btn"`.
2. Метод `build_search_query(self, keyword)` повинен повертати словник із параметрами пошуку `{"q": keyword, "source": "ui"}`.

**Приклад розв'язку:**
```python
class SearchComponent:
    def __init__(self):
        self.search_input_selector = "#search"
        self.submit_btn_selector = "#search-btn"

    def build_search_query(self, keyword: str) -> dict:
        return {
            "q": keyword.strip(),
            "source": "ui"
        }

search = SearchComponent()
query = search.build_search_query("  iPhone 15 Pro  ")

print("Локатор поля вводу:", search.search_input_selector)
print("Параметри запиту:", query)

assert query["q"] == "iPhone 15 Pro"
assert query["source"] == "ui"
```
