Вот полностью проработанный, академичный документ уровня Middle/Senior System Analyst для файла `docs/ru/architecture/c4/c4-container.md`.

Все текстовые ASCII-схемы переведены в **Mermaid-диаграммы**, убраны эмодзи и неформальные выражения, полностью сохранена и расширена вся исходная бизнес-логика, архитектурные ограничения и описания модулей.

---

### Файл: `docs/ru/architecture/c4/c4-container.md`

```markdown
# C4 — Container Diagram (Level 2)

Спецификация контейнерного уровня (C2) архитектурной модели C4 для платформы «Карманный гараж».

---

## 1. Назначение

Документ определяет архитектуру системы на уровне контейнеров (выделенных развертываемых приложений, сервисов и хранилищ данных), распределение функциональных обязанностей между ними и протоколы их сетевого взаимодействия.

В соответствии с методологией C4, под «контейнером» понимается самостоятельно развертываемый исполняемый модуль (executable) или хранилище данных, в рамках которого исполняется код или обрабатываются структуры данных.

---

## 2. Container Diagram

```mermaid
flowchart TB
    User["Владелец автомобиля<br/>(Пользователь)"]
    Partner["Автосервис / СТО<br/>(Партнёр)"]

    subgraph SystemBoundary ["Системный контур «Карманный гараж»"]
        Frontend["Web / Mobile Frontend<br/>(Single Page App / Mobile App)"]
        Backend["Backend API Core<br/>(Modular Monolith Application)"]
        PostgreSQL[("PostgreSQL DB<br/>(Реляционная БД)")]
        Storage[("S3 Object Storage<br/>(Файловое хранилище)")]
    end

    subgraph ExternalSystems ["Внешнее окружение (External APIs)"]
        VehicleProvider["Vehicle Data Provider<br/>(Декодер VIN)"]
        NotificationProvider["Notification Gateway<br/>(Push / SMS / Email)"]
    end

    User -->|"HTTPS / User UI"| Frontend
    Frontend -->|"HTTPS / REST API (JSON)"| Backend
    Backend -->|"SQL (TCP / Port 5432)"| PostgreSQL
    Backend -->|"S3 Protocol (HTTPS)"| Storage
    Backend -->|"HTTPS / REST API"| VehicleProvider
    Backend -->|"HTTPS / REST API"| NotificationProvider
    Partner -->|"HTTPS / REST API / Web"| Backend

```

---

## 3. Спецификация контейнеров (Containers Specification)

### 3.1. Web / Mobile Frontend

* **Назначение:** Клиентское приложение, обеспечивающее пользовательский интерфейс взаимодействия с системой.
* **Основной функционал:**
* Аутентификация и управление профилем;
* Ведение виртуального гаража ТС;
* Отображение финансовой аналитики и расходов;
* Просмотр сервисной книжки и истории ТО;
* Поиск, фильтрация СТО на карте и в каталоге;
* Оформление и контроль статуса заявок на сервисное обслуживание;
* Отображение пользовательских уведомлений.


* **Технологический стек (Предварительно):** React / Next.js (Web) или React Native / Cross-platform framework (Mobile).

### 3.2. Backend API Core

* **Назначение:** Ядро системы, исполняющее всю бизнес-логику, оркестрацию данных и обработку транзакций.
* **Основной функционал:**
* Обработка API-запросов и маршрутизация;
* Аутентификация, авторизация и ротация JWT-токенов;
* Серверная валидация входящих данных;
* Исполнение бизнес-правил доменных областей;
* Управление ACID-транзакциями в БД;
* Интеграция с внешними сервисами через слои адаптеров;
* Логирование, аудит и формирование системных ошибок.


* **Архитектура:** Модульный монолит (Modular Monolith).

### 3.3. PostgreSQL Database

* **Назначение:** Единая реляционная СУБД для надежного хранения структурированных данных.
* **Хранимые сущности:**
* Учетные записи пользователей, роли, сессии (`User`);
* Автомобили и профили ТС (`Vehicle`);
* Кэшированные метаданные предпросмотра ТС (`Vehicle Preview`);
* Транзакции финансовых расходов (`Expense`);
* Сервисные записи и история обслуживания (`Maintenance Record`);
* Реестр СТО, прайс-листы и гео-данные (`Service`);
* Заявки на бронирование (`Booking`).



### 3.4. S3 Object Storage

* **Назначение:** Распределенное хранилище неструктурированных бинарных файлов.
* **Хранимые объекты:** Фотографии ТС, сканы чеков, заказ-наряды, акты выполненных работ, аватары пользователей.
* **Правило работы:** В базе данных PostgreSQL сохраняются исключительно строковые URI и UUID метаданные файлов, сами бинарные файлы передаются и хранятся в S3.

### 3.5. Vehicle Data Provider (External)

* **Назначение:** Внешний поставщик справочных автомобильных данных.
* **Предмет интеграции:** Получение технических характеристик ТС по 17-значному VIN-коду.

### 3.6. Notification Provider (External)

* **Назначение:** Внешний агрегатор транспортной доставки сообщений.
* **Каналы связи:** Push-уведомления (приоритет в MVP), SMS-сообщения, Email-рассылки, Telegram Bot API.

### 3.7. Автосервис / СТО (External Actor)

* **Назначение:** Внешний бизнес-пользователь системы.
* **Функционал:** Получение уведомлений о новых бронированиях, подтверждение/отклонение времени приема, актуализация прайс-листа и перечня оказываемых услуг.

---

## 4. Логическая декомпозиция монолита (Backend Modules)

Исходный код Backend API логически инкапсулирован в изолированные модули в рамках единой кодовой базы:

```mermaid
graph TD
    subgraph BackendApp ["Backend API (Modular Monolith)"]
        Auth[Auth Module]
        Vehicle[Vehicle Module]
        Expense[Expense Module]
        Maintenance[Maintenance Module]
        Service[Service Module]
        Booking[Booking Module]
        Notification[Notification Module]
        Integration[Integration Module]
    end

```

---

## 5. Ответственность бизнес-модулей (Module Responsibilities)

* **Auth Module:** Регистрация, аутентификация (JWT/OAuth2), ротация токенов, управление сессиями, проверка прав доступа (RBAC/ABAC).
* **Vehicle Module:** Управление объектами ТС, обработка VIN-запросов, формирование превью автомобилей, валидация технических параметров.
* **Expense Module:** Фиксация финансовой активности, категориальный учет расходов, генерация аналитических отчетов и статистики TCO.
* **Maintenance Module:** Ведение регламентного и внепланового ТО, фиксирование пробега, детализация выполненных работ и замененных автозапчастей.
* **Service Module:** Ведение каталога автосервисов, гео-поиск, фильтрация по категориям работ, агрегация рейтингов и прайс-листов.
* **Booking Module:** Управление жизненным циклом заявки на ремонт, контроль временных слотов, отмена и перенос визитов.
* **Notification Module:** Формирование событийных уведомлений, очередь отправки, повторные попытки (retry) и отслеживание статусов доставки.
* **Integration Module:** Слой изоляции и адаптеров для взаимодействия с внешними API (Vehicle Data Provider, Notification Provider, Cartography).

---

## 6. Диаграмма межмодульных связей (Backend Module Diagram)

```mermaid
flowchart TD
    API["REST API Layer / Controllers"]

    subgraph CoreModules ["Бизнес-модули"]
        Auth["Auth"]
        Vehicle["Vehicle"]
        Expense["Expense"]
        Maintenance["Maintenance"]
        Service["Service"]
        Booking["Booking"]
        Notification["Notification"]
    end

    Integration["Integration Module"]

    API --> Auth
    API --> Vehicle
    API --> Expense
    API --> Maintenance
    API --> Service
    API --> Booking

    Expense --> Vehicle
    Maintenance --> Vehicle
    Maintenance --> Service
    Booking --> Vehicle
    Booking --> Service
    Booking --> Notification

    Vehicle --> Integration
    Notification --> Integration
    Service --> Integration

```

---

## 7. Правила межмодульного взаимодействия (Dependency Rules)

Для предотвращения образования связности («спагетти-кода») установлены следующие архитектурные регламенты:

1. **Изоляция данных:** Модуль не имеет права выполнять прямые SQL-запросы к таблицам БД, принадлежащим другому модулю.
2. **Публичный контракт:** Взаимодействие между модулями осуществляется строго через интерфейсы сервисного слоя (Application Services) или с помощью внутренних доменных событий (Domain Events).
3. **Изоляция контроллеров:** Слой контроллеров не содержит бизнес-логики.

```mermaid
flowchart TD
    Controller["API Controller Layer"] --> AppService["Application Service Layer"]
    AppService --> Domain["Domain Logic / Model"]
    Domain --> Repository["Repository Layer"]
    Repository --> Database[("PostgreSQL Database")]

```

---

## 8. Сценарий взаимодействия: Добавление ТС (Sequence Diagram)

```mermaid
sequenceDiagram
    autonumber
    actor User as Пользователь
    participant FE as Frontend
    participant API as Backend API
    participant Veh as Vehicle Module
    participant Int as Integration Module
    participant Ext as Vehicle Data Provider
    participant DB as PostgreSQL DB

    User->>FE: Ввод 17-значного VIN
    FE->>API: POST /api/v1/vehicles/lookup {vin}
    API->>Veh: lookupVehicle(vin)
    Veh->>Int: fetchByVin(vin)
    Int->>Ext: GET /vin-decoder/api/v1/{vin}
    Ext-->>Int: RAW Vehicle Specs JSON
    Int-->>Veh: Normalized Vehicle DTO
    Veh-->>API: Vehicle Preview DTO
    API-->>FE: HTTP 200 OK (Vehicle Preview Data)
    FE-->>User: Отображение карточки ТС для проверки

    User->>FE: Подтверждение («Сохранить в гараж»)
    FE->>API: POST /api/v1/vehicles {preview_id, user_data}
    API->>Veh: createVehicle(dto)
    Veh->>DB: INSERT INTO vehicles (...)
    DB-->>Veh: Record Created (vehicle_id)
    Veh-->>API: Vehicle Entity
    API-->>FE: HTTP 201 Created (Vehicle Object)
    FE-->>User: Автомобиль успешно добавлен в гараж

```

---

## 9. Потоки данных (Data Flows)

### 9.1. Внутренний обработочный поток

```mermaid
flowchart LR
    User["Пользователь"] --> FE["Frontend"]
    FE --> API["Backend API"]
    API --> Module["Business Module"]
    Module --> Repo["Repository"]
    Repo --> DB[("PostgreSQL")]

```

### 9.2. Поток внешних интеграций

```mermaid
flowchart LR
    Module["Business Module"] --> Int["Integration Layer"]
    Int --> ExtAPI["External Provider API"]
    ExtAPI --> Int
    Int --> Module

```

---

## 10. Границы API и изоляция данных (API Boundary)

Прямой доступ клиентских приложений к базе данных полностью запрещен. Доступ осуществляется исключительно через шлюз REST API с обязательной проверкой авторизации и валидацией данных.

```mermaid
flowchart LR
    subgraph Restricted ["Запрещено"]
        ClientBad["Frontend"] -.->|Direct Connection| DBBad[("PostgreSQL")]
    end

    subgraph Allowed ["Разрешено"]
        ClientGood["Frontend"] -->|HTTPS / REST API| Backend["Backend API Core"]
        Backend -->|Validated SQL| DBGood[("PostgreSQL")]
    end

```

---

## 11. Граница внешних интеграций (External Integration Boundary)

Для исключения жесткой зависимости от API внешних вендоров используется паттерн «Адаптер» (Adapter Pattern). Доменная логика взаимодействует только с абстрактным интерфейсом.

```mermaid
flowchart TD
    subgraph Core ["Domain Core"]
        Module["Vehicle Module"] --> Interface["IVehicleDataProvider (Interface)"]
    end

    subgraph IntegrationLayer ["Integration Adapter Layer"]
        Interface --> Adapter["VehicleProviderAdapter"]
        Adapter --> ExternalAPI["External VIN API (Http Client)"]
    end

```

---

## 12. Обработка сбоев и отказоустойчивость (Failure Handling)

При недоступности внешних интеграционных сервисов система сохраняет работоспособность основного контура.

```mermaid
flowchart TD
    Request["Запрос к внешнему API"] --> Execution{"Выполнение HTTP-запроса"}
    Execution -->|Успех| Success["Обработка ответа"]
    Execution -->|Таймаут / Ошибка| Retry{"Повторные попытки (Retry Count <= 3)"}
    Retry -->|Неудача| Fallback["Исполнение Fallback-сценария"]
    Fallback --> Log["Запись ошибки в лог"]
    Fallback --> Response["Возврат клиенту сообщения об ошибке"]

```

**Формат ответа клиенту при сбое внешнего сервиса:**

```json
{
  "error": {
    "code": "EXTERNAL_PROVIDER_UNAVAILABLE",
    "message": "Сервис расшифровки VIN временно недоступен. Введите данные вручную.",
    "requestId": "a8f3c912-4567-890a-bcde-f0123456789a",
    "timestamp": "2026-08-07T12:30:00Z"
  }
}

```

---

## 13. Требования к доступности (Availability Considerations)

1. **Автономность ядра:** Сбой внешних сервисов (VIN-декодера или Push-шлюза) не должен блокировать просмотр гаража, историю расходов или создание локальных записей ТО.
2. **Circuit Breaker:** Использование тайм-аутов и прерывателей цепи (Circuit Breaker) для предотвращения зависания потоков сервера при долгом отклике сторонних API.

---

## 14. Контур безопасности (Security Boundaries)

```mermaid
flowchart TD
    subgraph PublicZone ["Публичная зона (Untrusted Zone)"]
        Internet["Internet Client (Web / Mobile)"]
    end

    subgraph DMZ ["Сетевой периметр (DMZ / Ingress)"]
        FW["HTTPS / TLS Termination (Port 443)"]
    end

    subgraph PrivateZone ["Защищенный внутренний контур (Private Zone)"]
        BE["Backend App (Auth, Validation, Business Rules)"]
        DB[("PostgreSQL DB (No Public IP)")]
        Storage[("S3 Storage (Private Access)")]
    end

    Internet --> FW
    FW --> BE
    BE --> DB
    BE --> Storage

```

---

## 15. Стратегия масштабирования (Scaling Strategy)

Backend-приложение проектируется в концепции **Stateless** (без сохранения состояния сессии на сервере), что позволяет производить горизонтальное масштабирование путем увеличения количества экземпляров за балансировщиком нагрузки.

```mermaid
flowchart TD
    Client["Клиентский трафик"] --> LB["Load Balancer (Nginx / HAProxy)"]

    subgraph ApplicationCluster ["Кластер приложений"]
        BE1["Backend Instance #1"]
        BE2["Backend Instance #2"]
        BEN["Backend Instance #N"]
    end

    LB --> BE1
    LB --> BE2
    LB --> BEN

    BE1 --> DB[("PostgreSQL (Master)")]
    BE2 --> DB
    BEN --> DB

```

---

## 16. Спецификация технологического стека (Technology Stack)

| Слой системы | Технология / Стандарт | Обоснование выбора |
| --- | --- | --- |
| **Frontend** | React / Next.js (Web), React Native (Mobile) | Высокая скорость разработки, кроссплатформенность |
| **Backend API** | Node.js (TypeScript) / Go / Java | Строгая типизация, высокая производительность, экосистема |
| **Database** | PostgreSQL 15+ | Надежность (ACID), поддержка JSONB и PostGIS (геолокация) |
| **API Protocol** | REST / JSON | Стандарт де-факто, простота интеграции и документирования |
| **Object Storage** | MinIO / S3-compatible | Масштабируемость, совместимость с AWS S3 SDK |
| **Authentication** | JWT (RS256) / OAuth2 | Stateless-аутентификация, удобная масштабируемость |
| **Documentation** | OpenAPI 3.0 (Swagger) | Автоматическая генерация клиентских SDK и контрактов |
| **Diagrams** | Mermaid.js | Кодовая декларация диаграмм в Markdown (Git-native) |
| **Containerization** | Docker, Docker Compose | Изоляция окружения, воспроизводимость сборок |
| **CI/CD** | GitHub Actions | Автоматизация проверок, линтинга и деплоя |

---

## 17. Архитектурные компромиссы (Architecture Trade-offs)

### Модульный монолит vs Микросервисы

* **Выбранное решение:** Модульный монолит (Modular Monolith).
* **Причина выбора:** Скорость валидации продуктовых гипотез MVP, отсутствие высокой нагрузки на старте, минимальная стоимость инфраструктуры и поддержки.
* **Преимущества:**
* Высокая скорость разработки и поставки фичей;
* Простой процесс локальной разработки и деплоя;
* Отсутствие сетевых задержек (network latency) при межмодульном взаимодействии;
* Простая организация транзакций в рамках одной БД.


* **Компромиссы / Риски:**
* Масштабирование происходит только целиком для всего приложения;
* Требуется жесткий контроль архитектурных границ кода для предотвращения высокой связности.



---

## 18. Целевая эволюция архитектуры (Future Evolution)

При достижении пороговых нагрузок или изменении структуры команд монолит может быть декомпозирован на независимые микросервисы без переписывания бизнес-логики:

```mermaid
graph TD
    subgraph TargetMicroservices ["Целевая микросервисная архитектура"]
        AuthSvc["Auth Service"]
        VehicleSvc["Vehicle Service"]
        BookingSvc["Booking Service"]
        NotifSvc["Notification Service"]
    end

```

---

## 19. Статус документа

| Параметр | Значение |
| --- | --- |
| **Уровень C4** | C2 — Container Diagram |
| **Статус** | Approved |
| **Версия** | 1.0.0 |
| **Авторы** | System Analysis Team |

```
