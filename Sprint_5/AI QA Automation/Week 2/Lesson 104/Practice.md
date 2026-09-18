# Практичні завдання до Уроку 104: Рефакторинг AI-generated коду

## Мета заняття
Навчитися брати сирий монолітний скрипт автотесту, згенерований LLM, та перетворювати його на високоякісний модульний код з використанням Page Object Model, фікстур та параметризації.

---

### Завдання 1. Рефакторинг процедурного тесту реєстрації у Page Object (Базовий рівень)

#### Вхідний спагеті-код від AI:
```python
def test_registration(page):
    page.goto("https://shop.example.com/register")
    page.locator("#reg-firstname").fill("Олександр")
    page.locator("#reg-lastname").fill("Коваленко")
    page.locator("#reg-email").fill("olexandr.k@test.ua")
    page.locator("#reg-password").fill("SecurePass2026!")
    page.locator("#reg-confirm-password").fill("SecurePass2026!")
    page.locator("#terms-checkbox").check()
    page.locator("button.submit-registration").click()
    page.wait_for_timeout(3000)
    assert page.locator(".welcome-banner").is_visible()
```

#### Завдання:
1. За допомогою AI створіть клас `RegisterPage` з семантичними локаторами Playwright (`get_by_role`, `get_by_label`).
2. Замініть `page.wait_for_timeout(3000)` на Web-first assertion `expect()`.
3. Організуйте фікстуру в `conftest.py`.

---

### Завдання 2. Параметризація негативних сценаріїв валідації (Прикладний рівень)

#### Завдання:
Перетворіть набір з 4 окремих копіпаст-тестів для валідації некоректного email (порожній, без `@`, без домену, зі спецсимволами) в один елегантний тест з `@pytest.mark.parametrize`.

---

### Завдання 3. Виділення Component Object для універсального хедера (Робота з AI)

#### Завдання:
Створіть `HeaderComponent`, який містить рядок пошуку, лічильник кошика та меню профілю, і підключіть його до `HomePage` та `CatalogPage`.

---

## Чекліст самоперевірки
- [ ] Жодного сирого селектора не залишилося у тестовій функції `test_*`.
- [ ] Використано виключно веб-перші асерти `expect()`.
- [ ] Реалізовано параметризований тест для перевірки граничних значень.
