# Урок 188: JSON та структуровані дані

## Вступ: Автоматизована валідація схем даних у конвеєрі CI/CD

При ручному тестуванні інженер може візуально оцінити пару полів у відповіді сервера. Проте у виробничому середовищі, де системи обробляють мільйони транзакцій, будь-яка непомічена зміна структури JSON — наприклад, перейменування поля `userId` на `user_id` або зміна типу поля з рядка на число — миттєво зламає роботу мобільних додатків та залежних сервісів.

У сучасній автоматизації тестування золотим стандартом є **Schema-Driven Testing (Тестування на основі схем)**. Замість написання сотень ручних асертів для кожного окремого поля (`expect(body.id).toBeDefined()`, `expect(body.email).toBeString()`), інженер компілює формальну схему JSON Schema і за одну операцію верифікує 100% структури всієї відповіді за допомогою високопродуктивного валідатора (наприклад, бібліотеки Ajv).

У цьому уроці ми вивчимо інтеграцію валідатора Ajv у тестовий фреймворк Playwright, роботу з форматами даних (UUID, email, date-time), типізацію DTO в TypeScript та виявлення прихованих змін контрактів (Contract Regression).

---

## 1. Інтеграція валідатора Ajv у Playwright тест-сьют

Ajv (Another JSON Schema Validator) — це найшвидший промисловий валідатор JSON Schema для середовища Node.js/TypeScript, що повністю підтримує стандарти Draft-07 та Draft 2020-12:

```typescript
import { test, expect } from '@playwright/test';
import Ajv from 'ajv';
import addFormats from 'ajv-formats';

// 1. Ініціалізація Ajv з обов'язковим увімкненням strict-режиму
const ajv = new Ajv({
  allErrors: true, // Показувати всі знайдені помилки, а не зупинятися на першій
  strict: true     // Забороняє невідомі ключові слова у схемах
});
addFormats(ajv); // Додає підтримку форматів 'email', 'uuid', 'date-time'

// 2. Визначення схеми контракту DTO
export const UserResponseSchema = {
  type: "object",
  required: ["id", "email", "role", "createdAt"],
  properties: {
    id: { type: "string", format: "uuid" },
    email: { type: "string", format: "email" },
    role: { type: "string", enum: ["ADMIN", "USER", "GUEST"] },
    balance: { type: "number", minimum: 0 },
    createdAt: { type: "string", format: "date-time" }
  },
  additionalProperties: false // Забороняє незадокументовані поля (захист від Mass Assignment)
};

test('GET /api/v1/users/me відповідає повній схемі контракту', async ({ request }) => {
  const response = await request.get('/api/v1/users/me', {
    headers: { 'Authorization': 'Bearer test_token' }
  });
  expect(response.status()).toBe(200);

  const responseBody = await response.json();

  // 3. Компіляція та валідація схеми
  const validate = ajv.compile(UserResponseSchema);
  const isValid = validate(responseBody);

  // Якщо схема порушена, виводимо деталізований список невідповідностей
  if (!isValid) {
    console.error('Помилки валідації схеми:', JSON.stringify(validate.errors, null, 2));
  }
  expect(isValid).toBe(true);
});
```

---

## 2. Глибока типізація DTO: Синхронізація TypeScript та JSON Schema

У сучасних проектах автоматизації дублювання описів типів у TypeScript-інтерфейсах та схемах JSON є антипатерном. Використовуються бібліотеки генерації типів:
- Бібліотека `json-schema-to-ts` дозволяє автоматично виводити строгий TypeScript тип із JSON-схеми:
  ```typescript
  import { FromSchema } from 'json-schema-to-ts';
  export type UserResponseDTO = FromSchema<typeof UserResponseSchema>;
  ```
- Або використання бібліотеки **Zod**, яка одночасно є і TypeScript типом, і runtime-валідатором:
  ```typescript
  import { z } from 'zod';
  export const UserSchema = z.object({
    id: z.string().uuid(),
    email: z.string().email(),
    role: z.enum(['ADMIN', 'USER'])
  });
  export type UserDTO = z.infer<typeof UserSchema>;
  ```

---

## 3. Виявлення регресії контрактів (Contract Regression Detection)

Завдяки параметру `additionalProperties: false`, автотести миттєво сигналізують команді, якщо розробники бекенду випадково:
1. Змінили назву поля (наприклад, `createdAt` зникло, а з'явилося `creation_date`).
2. Додали внутрішнє поле, яке не повинно бути доступним публічно (наприклад, повернули `passwordHash` або внутрішній токен сесії).
3. Змінили формат дати з ISO 8601 на мілісекунди Unix Timestamp.


## Практичний інженерний розбір: Реалізація автономного API тест-пайплайну на Playwright

Створимо повноцінний автономний модуль тестування API на базі Playwright `APIRequestContext`, що включає динамічну автентифікацію, ізольовані фабрики тестових даних та валідацію через Ajv:

### 1. Реалізація клієнта API та ізольованого тесту (TypeScript)
```typescript
import { test, expect, request } from '@playwright/test';
import Ajv from 'ajv';
import addFormats from 'ajv-formats';

const ajv = new Ajv({ allErrors: true });
addFormats(ajv);

const userResponseSchema = {
  type: "object",
  required: ["id", "username", "email", "role", "createdAt"],
  properties: {
    id: { type: "string", format: "uuid" },
    username: { type: "string", minLength: 3 },
    email: { type: "string", format: "email" },
    role: { type: "string", enum: ["ADMIN", "USER", "OPERATOR"] },
    createdAt: { type: "string", format: "date-time" }
  },
  additionalProperties: false
};

test.describe('API E2E Сьюти: Керування користувачами та безпека ролей', () => {
  let apiContext: any;
  let authToken: string;

  test.beforeAll(async () => {
    // 1. Ініціалізація виділеного контексту з базовим URL
    apiContext = await request.newContext({
      baseURL: 'https://api.staging-cloud.io',
      extraHTTPHeaders: {
        'Accept': 'application/json',
        'Content-Type': 'application/json'
      }
    });

    // 2. Отримання сервісного токена автентифікації
    const authResponse = await apiContext.post('/api/v1/auth/login', {
      data: {
        client_id: process.env.TEST_CLIENT_ID || 'service_test_runner',
        client_secret: process.env.TEST_CLIENT_SECRET || 'secret_mock_key'
      }
    });
    expect(authResponse.status()).toBe(200);
    const authData = await authResponse.json();
    authToken = authData.access_token;
  });

  test('Створення нового користувача з валідацією повної JSON-схеми та контрактів', async () => {
    const uniqueEmail = `qa_auto_${Date.now()}@example.com`;
    const payload = {
      username: 'alex_auto_qa',
      email: uniqueEmail,
      role: 'OPERATOR'
    };

    const response = await apiContext.post('/api/v1/users', {
      headers: { 'Authorization': `Bearer ${authToken}` },
      data: payload
    });

    // Перевірка статус-коду
    expect(response.status()).toBe(201);
    
    const body = await response.json();
    
    // Перевірка JSON Schema валідатором Ajv
    const validate = ajv.compile(userResponseSchema);
    const valid = validate(body);
    if (!valid) {
      console.error('Помилки валідації схеми:', validate.errors);
    }
    expect(valid).toBe(true);

    // Бізнес-асерти
    expect(body.email).toBe(uniqueEmail);
    expect(body.role).toBe('OPERATOR');
  });

  test('Негативний сценарій: Спроба створення дублікату повертає 409 Conflict', async () => {
    const duplicatePayload = {
      username: 'duplicate_user',
      email: 'existing_account@example.com',
      role: 'USER'
    };

    const response = await apiContext.post('/api/v1/users', {
      headers: { 'Authorization': `Bearer ${authToken}` },
      data: duplicatePayload
    });

    expect(response.status()).toBe(409);
    const errorBody = await response.json();
    expect(errorBody.title).toContain('Conflict');
  });
});
```


## Архітектура сучасного фреймворку автоматизації тестування API (API Automation Architecture)

Побудова стабільного, підтримуваного та швидкого конвеєра автоматизованого тестування API є критичною передумовою неперервної інтеграції (CI/CD):

```
[Тестовий шар (Test Suite: Spec Files)]
                    │
                    ▼
[Шар контролерів API (API Client / Services Wrapper)]
                    │
                    ▼
[Шар генерації та управління тестовими даними (Faker / Factories / Fixtures)]
                    │
                    ▼
[Шар управління автентифікацією (Token Manager / Auto-refresh Session)]
                    │
                    ▼
[Транспортний клієнт (Playwright APIRequestContext / Supertest / Axios)]
                    │
                    ▼
[Шар валідації та асертів (Ajv JSON Schema / Expect Assertions)]
                    │
                    ▼
[Звітність та моніторинг (Allure Report / CI/CD Pipeline Artifacts)]
```

### Комплексна інженерна матриця побудови API автотестів

| Компонент фреймворку | Призначення та функції | Кращі інженерні патерни | Інструменти реалізації |
| :--- | :--- | :--- | :--- |
| **API Client Wrapper** | Інкапсуляція HTTP-запитів до конкретних ендпоінтів у методи класів | Service Object Pattern, Fluent Interface | TypeScript, Playwright API, Axios |
| **Auth & Session Reuse** | Одноразове отримання токена та його повторне використання в сьютах | Shared Fixtures, Global Setup, збереження стану в пам'яті | Playwright Fixtures, Node.js Memory Cache |
| **Схемна валідація** | Автоматична перевірка структури та типів усього JSON-відповіді | Компіляція схем за стандартом Draft-07/2020-12 | Ajv (Another JSON Schema Validator), Zod |
| **Параметризація даних** | Запуск одного тесту на великому масиві граничних даних | Data-Driven Testing (DDT), ізольовані фабрики даних | `@playwright/test` `for...of`, `@faker-js/faker` |
| **Логування та трейсинг** | Повна фіксація запитів, відповідей, хедерів та таймінгів при падінні | Інтеграція Correlation ID у заголовки, Allure Steps | Allure Framework, Winston, Pino |

### Архітектурні правила надійних API автотестів:
1. **Ізоляція тестового стану**: Кожен тест повинен самостійно створювати необхідні дані (pre-requisites) через API і видаляти їх після завершення (teardown), не залежачи від порядку виконання.
2. **Детерміновані перевірки**: Уникати перевірки статичних ID чи дат; замість цього перевіряти бізнес-відповідність (`response.createdAt` є валідною ISO-датою, `response.email === request.email`).
3. **Fail-Fast на статус-кодах**: Якщо запит повертає 500 замість 200, перевірка тіла повинна негайно зупинятися з виведенням повної помилки сервера в лог.


## Архітектурні стандарти та Quality Gates конвеєра API автоматизації

Автоматизовані тести API у виробничому середовищі мають відповідати суворим критеріям стабільності та підтримуваності:
1. **Принцип ізоляції тестового контексту (Independent Execution)**:
   - Тести не повинні залежати від послідовності запуску. Неприпустимо розраховувати на те, що тест `UPDATE` запуститься строго після тесту `CREATE`. Кожен сьют самостійно генерує потрібну сутність або використовує ізольовані фікстури.
2. **Швидкість та паралелізація (Performance Quality Gate)**:
   - Тестовий набір API для критичного функціоналу (Smoke API Suite) повинен виконуватися менше ніж за 90 секунд у CI пайплайні.
   - Час виконання одиночного запиту в локальній тестовій мережі повинен валідуватися асертом: `expect(responseTime).toBeLessThan(500)`.
3. **Flaky-Test Prevention**:
   - Повна відмова від статичних пауз (`sleep(3000)`). Замість цього використовуються асинхронні перевірки опитування (Polling with Timeout) для очікування завершення фонових задач.


## Інженерний AI-плейбук: Генерація автономних тест-сьютів на базі Playwright

Автоматизація написання тестів за допомогою AI-асистентів:
1. **Генерація структурованого Page/Service Object**:
   ```text
   Роль: Lead Automation Engineer.
   Завдання: Створи TypeScript клас API-клієнта для мікросервісу замовлень з використанням Playwright APIRequestContext.
   Вимоги: Реалізуй методи getOrder(id), createOrder(data), cancelOrder(id). Реалізуй автоматичне додавання Bearer токена до кожного виклику.
   ```


## Інфраструктура автоматизованого тестування API у хмарному CI/CD

Масштабований конвеєр виконання тестів у конвеєрі GitHub Actions:

```
[Git Push / Pull Request] ──> [GitHub Actions Runner]
                                       │
                                       ▼
                       [Spin up Docker Compose Backend]
                                       │
                                       ▼
                       [Playwright API Test Container]
                                       │
                       ┌───────────────┴───────────────┐
                       ▼                               ▼
             [Positive Test Suite]           [Negative / Security Suite]
                       │                               │
                       └───────────────┬───────────────┘
                                       │
                                       ▼
                           [Allure Report Generation]
                                       │
                                       ▼
                           [Publish Artifacts & Slack Alert]
```


## Безпека в автоматизації: Управління секретами та конфіденційними даними

Організація безпечного середовища виконання тестів:
- **Zero Hardcoded Secrets**: Сувора заборона збереження паролів, API-ключів або JWT токенів у вихідному коді тест-репозиторію. Всі конфіденційні параметри передаються через змінні оточення (Environment Variables) або завантажуються із захищених сховищ (HashiCorp Vault, GitHub Secrets).
- **Очищення тестових артефактів**: Автоматичне видалення згенерованих тестових облікових записів та тимчасових файлів після завершення прогону сьюту.


## Індустріальний досвід: Масштабування тестових фреймворків у Spotify та Uber

Організація тестових раннерів на тисячі тестів:
- **Паралельне виконання у хмарі**: Запуск сьютів у десятках контейнерів паралельно з шардуванням на базі тривалості тестів (Test Execution Time Balancing), що скорочує регресію з годин до 5 хвилин.


## Інструментарій інженера з автоматизації тестування (Automation Toolkit)

Стек бібліотек та утиліт для автоматизації:
- **Фреймворки виконання**: Playwright Test, Supertest, Vitest, Jest, pytest, RestAssured.
- **Валідація контрактів**: Ajv, Chai-json-schema, Pact.
- **Звітність**: Allure Report, ReportPortal.


## Підсумковий чеклист автоматизованого тестового сьюту (Automation Checklist)

Критерії стабільності автоматизованих тестів:
- [ ] Відсутність Flaky-тестів (0 падінь у 5 послідовних прогонах у CI).
- [ ] Тести повністю автономні та очищують за собою створені тестові дані.
- [ ] Всі перевірки тіла відповіді підкріплені валідацією JSON Schema.
- [ ] Звіти Allure коректно публікуються в артефактах збірки CI/CD.


## Експертний аналіз: Чому 400 тестів у CI перестали ловити баги через спільний мутуючий стан

Історія оптимізації автоматизованих сьютів:
1. **Симптом**: Регресійний прогін займав 45 хвилин і падав рандомно на 10-15 тестах (Flaky tests). Інженери почали ігнорувати результати перевірок.
2. **Причина**: Тести використовували одного спільного користувача `admin@test.com` для всіх паралельних потоків. Один тест змінював пароль користувача, через що інші 10 тестів негайно падали з помилкою 401 Unauthorized.
3. **Рішення**: Перехід на динамічну генерацію ізольованих користувачів через фабрики (Fixture Factory) та ізоляцію контекстів на рівні кожного тесту. Час прогону скоротився до 4 хвилин, а стабільність досягла 100%.


## Метрики конвеєра автоматизації тестування API

Оцінка зрілості фреймворку автоматизації:
- **Automation Pass Rate**: Відсоток успішних тестів у нічних та пре-релізних прогонах (цільове: > 98%).
- **Flakiness Index**: Відсоток тестів, які змінюють свій статус без зміни коду (цільове: < 1%).
- **Execution Speed**: Середній час виконання одного API автотесту (цільове: < 250мс).


## Діалоговий воркфлоу Automation інженера: Рефакторинг тестів на Playwright

Оптимізація тестового коду за допомогою AI:
1. **Крок 1**:
   > *Automation Lead*: «Перетвори цей довгий монолітний тест на 150 рядків у чистий Service Object патерн, винісши повторювані виклики авторизації у фікстури Playwright».
   > *AI*: Створює модуль `BookingService.ts` та чистий `spec` файл, де тест займає 15 рядків.


## Системний синтез та майбутнє автоматизації тестування

Сучасний SDET створює екосистеми неперервної перевірки якості:
- **Self-Healing API Tests**: Використання AI для динамічного оновлення схем та адаптації тестів при зміні версій API.
- **Service Virtualization & Test Containers**: Повна ізоляція тестових середовищ за допомогою динамічних Docker-контейнерів у хмарі.


## Поглиблений інженерний гайд: Архітектура моніторингу та детекції регресій у CI/CD

Автоматизоване тестування API має бути безшовно інтегровано у систему моніторингу якості коду:
1. **Quality Gates у Pull Request**:
   - Жоден PR не може бути злитий у гілку `main`, якщо Smoke API Suite впав хоча б на одному тесті.
   - Рівень покриття ендпоінтів тестами (API Coverage) не може знижуватися відносно попередньої збірки.
2. **Синтетичні моніторингові тести на Production (Canary Probing)**:
   Запуск легкої ізольованої пачки автоматизованих тестів кожні 60 секунд на живому продакшні для перевірки доступності та латентності критичних шляхів (Health Check, Auth Check).


## Діагностика та усунення проблем в автотестах API (Automation Troubleshooting)

Усунення нестабільності тестових сьютів:
- **Проблема асинхронних гонок (Async Race Conditions у тестах)**:
  - Тест намагається перевірити статус замовлення відразу після виклику API, коли бекенд ще обробляє подію у черзі Kafka.
  - *Вирішення*: Використання розумного опитування (Smart Polling / Awaitility) замість жорстких пауз `sleep`.
- **Конфлікти паралельних потоків**:
  - При запуску 10 потоків Playwright тести перезаписують стан спільних тестових сутностей.
  - *Вирішення*: Генерація окремого ізольованого Tenant ID для кожного воркера Playwright.


## Виробничий дизайн фреймворку автоматизації: Патерни проектування тестового коду

Для збереження чистоти та підтримки тестів при зростанні їх кількості до тисяч застосовуються інженерні патерни:
- **Builder / Factory Pattern для тестових даних**:
  Створення гнучких будівельників payload: `const order = new OrderBuilder().withItems(3).withDiscount(20).build();`, що дозволяє створювати складні тестові об'єкти в 1 рядок без дублювання великих JSON-шаблонів.
- **Custom Matchers / Assertions**:
  Створення доменних асертів (`expect(response).toBeValidUserDTO()`), що покращує читабельність коду та формує зрозумілі логи при падінні тестів у CI/CD.


## Симуляція архітектури автоматизованого тестування у CI/CD (Pipeline Orchestration)

Професійна автоматизація тестування API вимагає не просто запуску скриптів, а побудови стійкого конвеєра неперервної інтеграції:

### 1. Паралелізація та шардування тестових наборів (Test Sharding)
При наявності тисяч API-тестів послідовний прогін може займати години. Оптимальна архітектура у GitHub Actions / GitLab CI передбачає шардування:
```yaml
strategy:
  matrix:
    shardIndex: [1, 2, 3, 4]
    totalShards: [4]
steps:
  - run: npx playwright test --shard=${{ matrix.shardIndex }}/${{ matrix.totalShards }}
```
Це скорочує час зворотної інженерної петлі (Feedback Loop) для розробників до 3-5 хвилин на кожен Pull Request.

### 2. Управління ізольованими тестовими оточеннями (Ephemeral Environments)
Автоматизовані тести повинні запускатися проти динамічно створених екземплярів бекенду (Preview Environments на базі Docker/Kubernetes), де кожен прогін має власну чисту тестову базу даних, наповнену синтетичними фікстурами. Це усуває взаємний вплив паралельних тестів та ефект «забруднених даних».


## Матриця діагностики нестабільних автоматизованих тестів (Flaky Tests Mitigation)

| Проблема автоматизації | Корнева причина нестабільності | Як локалізувати | Інженерне усунення |
| :--- | :--- | :--- | :--- |
| **Тест падає випадковим чином раз на 10 запусків** | Гонка станів (Race Condition) або затримка реплікації бази даних | Запуск тесту в циклі 100 разів локально: `for i in {1..100}; do npx playwright test; done` | Відмова від жорстких `sleep(3000)`, перехід на динамічні опитування стану (Polling / Polling Assertions) |
| **Тести падають тільки в CI, але проходять локально** | Різниця в часових поясах (Timezone) або мережева ізоляція ранера | Перевірка змінної оточення `TZ=UTC` у контейнері CI | Явна фіксація таймзони UTC у конфігурації фреймворку тестування |
| **Каскадне падіння всього сьюта після однієї помилки** | Відсутність очищення створених даних (Tear Down) | Перевірка блоків `afterEach` / `afterAll` | Гарантоване видалення створених сутностей через API у блоці `finally` |


## Підсумковий інженерний огляд та практичні рекомендації

Опанування теми **«JSON та структуровані дані»** є ключовим будівельним блоком у професійному становленні спеціаліста за напрямком **AI QA Automation**. Системне застосування розглянутих методологій дозволяє:
1. **Забезпечити максимальну надійність та стабільність рішень**, спираючись на перевірені індустріальні патерни, міжнародні стандарти та строгі специфікації.
2. **Усунути людський фактор та сліпі зони**, використовуючи матриці контролю якості, автоматизовані тести та перехресну валідацію схем і процесів.
3. **Ефективно інтегрувати сучасні AI-інструменти** у щоденний виробничий конвеєр, виступаючи в ролі кваліфікованого інженера-архітектора, який ставить точні технічні завдання та критично верифікує результат.
