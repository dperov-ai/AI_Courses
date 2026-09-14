# Практичні завдання до Уроку 68: Побудова пошукової стратегії

## Мета практики
Опанувати професійні техніки побудови пошукових запитів: використання булевих операторів (`AND`, `OR`, `NOT`), Google Dorks (`site:`, `filetype:`, `intitle:`), формування синонімічних таблиць та підбір академічних баз даних (Google Scholar, arXiv, ACM, IEEE).

---

### Завдання 1. Побудова матриці ключових слів та булевого запиту (Базовий рівень)

#### Опис завдання:
Для теми «Вразливості безпеки та атаки на Retrieval-Augmented Generation (RAG) системи» складіть таблицю синонімів та підсумковий пошуковий булевий запит.

#### Зразок матриці:
| Концепт 1: Технологія | Концепт 2: Вразливість / Атака | Концепт 3: Наслідок |
| :--- | :--- | :--- |
| `"Retrieval-Augmented Generation"` OR `RAG` OR `"vector database"` | `"prompt injection"` OR `"data poisoning"` OR `"jailbreak"` | `"data leak"` OR `"hallucination"` OR `"unauthorized access"` |

#### Підсумковий булевий рядок для Scholar / Scopus:
`("Retrieval-Augmented Generation" OR "RAG") AND ("prompt injection" OR "data poisoning" OR "jailbreak") AND ("vulnerability" OR "security")`

---

### Завдання 2. Застосування Google Dorks для глибокого технічного пошуку (Прикладний рівень)

#### Опис завдання:
Складіть 4 пошукові запити з розширеними операторами Google для знаходження офіційних звітів, технічних whitepapers у PDF та специфікацій.

#### Зразок Dorks:
1. Пошук офіційних PDF-звітів консалтингових агенцій (Gartner, McKinsey):  
   `site:mckinsey.com OR site:gartner.com "Generative AI" report filetype:pdf 2025..2026`
2. Пошук інженерних специфікацій на GitHub:  
   `site:github.com intitle:"awesome-rag" OR intitle:"awesome-llm-security"`
3. Пошук академічних препринтів на arXiv:  
   `site:arxiv.org "vector search" "latency optimization" filetype:pdf`
4. Пошук офіційної документації без блогів:  
   `site:docs.* OR site:learn.* "Prompt Flow" architecture`

---

### Завдання 3. AI як оптимізатор пошукових запитів (Робота з AI)

#### Опис завдання:
Попросіть AI перетворити абстрактний опис проблеми на 5 оптимізованих пошукових запитів для Google Scholar, arXiv та Semantic Scholar.

#### Промпт для AI:
> «Мені потрібно знайти найновіші рецензовані дослідження (2024-2026) про оптимізацію контекстного вікна у великих мовних моделях (Context Window Compression / Long Context). Сформулюй 5 вузьких пошукових запитів із точними ключовими термінами англійською мовою для Google Scholar та arXiv.»

---

## Чекліст самоперевірки
- [ ] Складено синонімічну матрицю концептів.
- [ ] Використано комбінації булевих операторів та лапок для точного збігу.
- [ ] Сформовано робочі Google Dorks (`filetype:pdf`, `site:`).
