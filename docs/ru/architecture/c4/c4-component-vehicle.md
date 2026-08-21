# C4 - Component Diagram: Vehicle Module

Спецификация компонентного уровня (C3) архитектурной модели C4 для модуля управления транспортными средствами (`Vehicle Module`) системы «Карманный гараж».

---

## 1. Назначение

Документ описывает внутреннюю структуру, декомпозицию на компоненты, распределение обязанностей и механизмы взаимодействия внутри `Vehicle Module`. 

Модуль реализует ключевой пользовательский сценарий добавления ТС в гараж:
1. Ввод 17-значного VIN-кода пользователем;
2. Получение и нормализация характеристик ТС от внешнего источника;
3. Формирование и отображение временного снимка данных (`Vehicle Preview`);
4. Подтверждение и персистентное сохранение объекта `Vehicle` в базе данных.

---

## 2. Component Diagram

```mermaid
flowchart TB
    Client["Client App / Frontend"]

    subgraph VehicleModule ["Vehicle Module (Boundary)"]
        Controller["Vehicle Controller<br/>(REST API Adapter)"]
        AppService["Vehicle Application Service<br/>(Use Case Orchestration)"]
        DomainService["Vehicle Domain Service<br/>(Business Rules & Validation)"]
        RepoInterface["Vehicle Repository Interface"]
        RepoImpl["Vehicle Repository Impl<br/>(Data Access Object)"]
        ProviderInterface["Vehicle Provider Interface"]
        AdapterImpl["Vehicle Provider Adapter<br/>(External API Client)"]
    end

    DB[("PostgreSQL Database<br/>(tables: vehicles, previews)")]
    ExternalProvider["External Vehicle Data Provider<br/>(VIN Decoder API)"]

    Client -->|"HTTPS / REST (JSON)"| Controller
    Controller -->|"DTO"| AppService
    AppService --> DomainService
    AppService --> RepoInterface
    AppService --> ProviderInterface
    
    RepoImpl -. "Implements" .-> RepoInterface
    AdapterImpl -. "Implements" .-> ProviderInterface
    
    RepoImpl -->|"SQL Queries / ORM"| DB
    AdapterImpl -->|"HTTPS / REST"| ExternalProvider

```

---

## 3. Спецификация компонентов (Components Detail)

### 3.1. Vehicle Controller

* **Назначение:** Точка входа HTTP API для модуля управления ТС.
* **Ответственность:**
* Прием и первичная десериализация HTTP-запросов;
* Валидация структуры входного JSON (DTO);
* Извлечение контекста авторизованного пользователя (`UserId` из JWT);
* Делегирование вызова в `Vehicle Application Service`;
* Маппинг доменных результатов и исключений в соответствующие HTTP-ответы (200, 201, 400, 404, 500).



### 3.2. Vehicle Application Service

* **Назначение:** Слой оркестрации пользовательских сценариев (Use Cases).
* **Ответственность:**
* Управление последовательностью исполнения сценариев: `lookupVehicleByVin()`, `createVehicle()`, `getVehicleById()`, `getUserVehicles()`, `updateVehicle()`;
* Управление границами локальных транзакций;
* Координация взаимодействия между Domain Service, Repository и Provider.



### 3.3. Vehicle Domain Service

* **Назначение:** Ядро бизнес-логики и правил домена ТС.
* **Ответственность:**
* Проверка валидности формата VIN (контрольные суммы, синтаксис ISO 3779);
* Нормализация текстовых полей VIN (приведение к верхнему регистру, удаление спецсимволов);
* Проверка доменных ограничений (например, максимальное количество ТС у одного пользователя);
* Проверка возможности сохранения ТС в базу.



### 3.4. Vehicle Repository Interface & Implementation

* **Назначение:** Абстракция слоя хранения данных (Pattern Repository).
* **Ответственность:**
* Предоставление чистых CRUD-методов для работы с сущностями `Vehicle` и `Vehicle Preview`;
* Изоляция слоя приложений от деталей реализации базы данных и ORM;
* Выполнение SQL-запросов к PostgreSQL.



### 3.5. Vehicle Provider Interface & Adapter

* **Назначение:** Изоляция внешних интеграций по декодированию VIN (Pattern Adapter).
* **Ответственность:**
* Трансляция внутренних доменных запросов во внешние HTTP-вызовы;
* Аутентификация во внешнем API (API Keys / Bearer tokens);
* Обработка сетевых ошибок, таймаутов и повторных попыток (Retry Policy);
* Маппинг внешнего неупорядоченного ответа в стандартизированный внутренний DTO.



---

## 4. Матрица ответственности компонентов

| Компонент | Тип компонента | Основная ответственность |
| --- | --- | --- |
| **Vehicle Controller** | API Gate / Interface | Прием HTTP-запросов, авторизация, сериализация DTO |
| **Vehicle Application Service** | Application Layer | Оркестрация Use Cases, управление транзакциями |
| **Vehicle Domain Service** | Domain Layer | Валидация VIN, соблюдение доменных инвариантов |
| **Vehicle Repository** | Infrastructure Layer | Абстракция работы с СУБД PostgreSQL |
| **Vehicle Provider Interface** | Contract | Внутренний контракт получения данных по VIN |
| **Vehicle Provider Adapter** | Infrastructure Layer | Клиент внешнего REST API декодирования VIN |

---

## 5. Правила архитектурных зависимостей (Dependency Rules)

Для соблюдения принципов Clean / Hexagonal Architecture внутри модуля действуют следующие строгие правила:

### Rule 1: Изоляция Controller

Контроллер передает управление на слой приложений и не содержит логики принятия решений.

```mermaid
flowchart LR
    Controller["Vehicle Controller"] -->|"Execution Call"| AppService["Application Service"]
    Controller -.->|"Forbidden Direct Call"| DB[("Database / Domain")]

```

### Rule 2: Изоляция базы данных

Слой приложений взаимодействует с базой только через абстракцию `Repository Interface`.

```mermaid
flowchart LR
    AppService["Application Service"] -->|"1. Call Interface"| RepoInt["Repository Interface"]
    RepoImpl["Repository Implementation"] -. "2. Implements" .-> RepoInt
    RepoImpl -->|"3. Execute SQL"| DB[("PostgreSQL")]
```

### Rule 3: Изоляция внешних поставщиков (Adapter Pattern)

Бизнес-логика не имеет прямого знания о структуре API сторонних провайдеров.

```mermaid
flowchart LR
    Domain["Domain Logic"] -->|"Depends on"| Interface["Vehicle Provider Interface"]
    Adapter["Provider Adapter"] -. "Implements" .-> Interface
    Adapter -->|"HTTP Call"| ExternalAPI["External VIN API"]
```

---

## 6. Sequence Diagram: Поиск ТС по VIN (Lookup Vehicle)

```mermaid
sequenceDiagram
    autonumber
    actor User as Пользователь
    participant FE as Frontend
    participant C as Vehicle Controller
    participant A as Application Service
    participant D as Domain Service
    participant P as Provider Interface
    participant Ext as External VIN API

    User->>FE: Вводит VIN-код
    FE->>C: POST /api/v1/vehicles/lookup {vin}
    C->>A: lookupVehicleByVin(vin)
    A->>D: validateAndNormalizeVin(vin)
    
    alt VIN Невалиден
        D-->>A: InvalidVinException
        A-->>C: Domain Error
        C-->>FE: HTTP 400 Bad Request
    else VIN Валиден
        D-->>A: Normalized VIN
        A->>P: getVehicleByVin(normalizedVin)
        P->>Ext: GET /external-api/v1/decode/{vin}
        Ext-->>P: RAW Response Data
        P-->>A: VehicleData DTO
        A-->>C: VehiclePreview DTO
        C-->>FE: HTTP 200 OK (Vehicle Preview)
        FE-->>User: Отображает карточку с характеристиками
    end

```

---

## 7. Sequence Diagram: Создание ТС (Create Vehicle)

```mermaid
sequenceDiagram
    autonumber
    actor User as Пользователь
    participant FE as Frontend
    participant C as Vehicle Controller
    participant A as Application Service
    participant D as Domain Service
    participant R as Vehicle Repository
    participant DB as PostgreSQL DB

    User->>FE: Нажимает «Подтвердить и добавить»
    FE->>C: POST /api/v1/vehicles {previewId}
    C->>A: createVehicle(userId, previewId)
    A->>R: findPreviewById(previewId)
    R->>DB: SELECT * FROM vehicle_previews WHERE id = previewId
    DB-->>R: Preview Entity
    R-->>A: Preview Entity
    
    A->>D: validateCanCreateVehicle(userId, preview)
    D-->>A: Validation Passed
    
    A->>R: existsByUserIdAndVin(userId, vin)
    R->>DB: SELECT COUNT(*) FROM vehicles WHERE user_id = ? AND vin = ?
    DB-->>R: Count
    
    alt ТС уже добавлено
        R-->>A: true
        A-->>C: DuplicateVehicleException
        C-->>FE: HTTP 409 Conflict
    else ТС отсутствует
        R-->>A: false
        A->>R: save(vehicleEntity)
        R->>DB: INSERT INTO vehicles (...) VALUES (...)
        DB-->>R: Saved Entity
        R-->>A: Vehicle Entity
        A-->>C: Vehicle DTO
        C-->>FE: HTTP 201 Created
        FE-->>User: ТС успешно отображается в гараже
    end

```

---

## 8. Sequence Diagram: Обработка ошибок валидации VIN

```mermaid
sequenceDiagram
    autonumber
    actor User as Пользователь
    participant C as Vehicle Controller
    participant A as Application Service
    participant D as Domain Service

    User->>C: POST /api/v1/vehicles/lookup {vin: "INVALID_VIN"}
    C->>A: lookupVehicleByVin("INVALID_VIN")
    A->>D: validateAndNormalizeVin("INVALID_VIN")
    D-->>A: Throw InvalidVinException("VIN length must be 17 characters")
    A-->>C: Catch Exception -> Map to Error Payload
    C-->>User: HTTP 400 Bad Request

```

**Формат ответа при ошибке валидации:**

```json
{
  "error": {
    "code": "INVALID_VIN_FORMAT",
    "message": "Введенный VIN-код не соответствует стандарту ISO 3779 (должен содержать 17 символов).",
    "requestId": "c1092a8e-1234-5678-90ab-cdef12345678",
    "timestamp": "2026-08-07T12:35:00Z"
  }
}

```

---

## 9. Sequence Diagram: Отказ внешнего провайдера VIN

```mermaid
sequenceDiagram
    autonumber
    participant A as Application Service
    participant Adapter as Vehicle Provider Adapter
    participant Ext as External VIN API

    A->>Adapter: getVehicleByVin(vin)
    Adapter->>Ext: GET /external-api/v1/decode/{vin}
    Ext--xAdapter: Network Timeout / HTTP 503
    
    loop Повторные попытки (Retry Policy)
        Adapter->>Ext: GET /external-api/v1/decode/{vin}
        Ext--xAdapter: HTTP 503 Service Unavailable
    end
    
    Adapter-->>A: Throw ProviderUnavailableException
    A-->>A: Map to System Error Payload

```

---

## 10. Границы и трансформация DTO

Для изоляции доменных моделей от изменений API и СУБД используется поэтапная трансформация объектов:

```mermaid
flowchart LR
    HTTP_Req["HTTP Request (JSON)"] -->|Deserialize| ReqDTO["Request DTO"]
    ReqDTO -->|Map| AppSvc["Application Layer"]
    AppSvc -->|Construct| DomainModel["Domain Model (Entity)"]
    DomainModel -->|Persist| Repo["Repository / DB Entity"]
    DomainModel -->|Map| ResDTO["Response DTO"]
    ResDTO -->|Serialize| HTTP_Res["HTTP Response (JSON)"]

```

---

## 11. Спецификация контрактов DTO (Data Transfer Objects)

### 11.1. VehicleLookupRequest

```json
{
  "vin": "XTA21070001234567"
}

```

### 11.2. VehiclePreviewResponse

```json
{
  "previewId": "8f2a1b94-3c4d-4e5f-a6b7-8c9d0e1f2a3b",
  "vin": "XTA21070001234567",
  "brand": "LADA",
  "model": "2107",
  "productionYear": 2010,
  "engineCapacity": 1.6,
  "transmission": "MANUAL",
  "expiresAt": "2026-08-07T13:30:00Z"
}

```

### 11.3. CreateVehicleRequest

```json
{
  "previewId": "8f2a1b94-3c4d-4e5f-a6b7-8c9d0e1f2a3b",
  "licensePlate": "А000АА177",
  "currentColor": "Черный"
}

```

---

## 12. Интерфейс репозитория (Repository Contract)

```typescript
interface IVehicleRepository {
  findById(id: string): Promise<VehicleEntity null |>;
  findByVin(vin: string): Promise<VehicleEntity null |>;
  findByUserId(userId: string): Promise<VehicleEntity[]>;
  existsByUserIdAndVin(userId: string, vin: string): Promise<boolean>;
  save(vehicle: VehicleEntity): Promise<VehicleEntity>;
  update(vehicle: VehicleEntity): Promise<VehicleEntity>;
  delete(id: string): Promise<void>;
  
  // Работа с временными снимками (Previews)
  savePreview(preview: VehiclePreviewEntity): Promise<VehiclePreviewEntity>;
  findPreviewById(previewId: string): Promise<VehiclePreviewEntity null |>;
}

```

---

## 13. Интерфейс поставщика данных (Provider Contract)

```typescript
interface IVehicleDataProvider {
  /**
   * Запрашивает технические характеристики ТС у внешнего провайдера
   * @param vin Нормализованный 17-значный VIN
   * @throws ProviderUnavailableException Если внешнее API недоступно
   */
  getVehicleByVin(vin: string): Promise<ExternalVehicleDataDTO>;
}

```

---

## 14. Реализация паттерна «Адаптер» (Adapter Pattern)

```mermaid
classDiagram
    class IVehicleDataProvider {
        <<interface>>
        +getVehicleByVin(vin: string) ExternalVehicleDataDTO
    }

    class VinDecoderAdapterA {
        -httpClient: HttpClient
        -apiKey: string
        +getVehicleByVin(vin: string) ExternalVehicleDataDTO
    }

    class CustomCarApiAdapterB {
        -httpClient: HttpClient
        +getVehicleByVin(vin: string) ExternalVehicleDataDTO
    }

    IVehicleDataProvider <|.. VinDecoderAdapterA : Implements
    IVehicleDataProvider <|.. CustomCarApiAdapterB : Implements

```

---

## 15. Границы транзакциональности (Transaction Boundary)

Создание автомобиля и очистка промежуточных метаданных выполняются в единой ACID-транзакции на уровне БД.

```mermaid
flowchart TD
    StartTx["BEGIN TRANSACTION"] --> Step1["Проверка существования Preview"]
    Step1 --> Step2["Вставка записи в таблицу 'vehicles'"]
    Step2 --> Step3["Создание связи в 'user_vehicles'"]
    Step3 --> Step4["Обновление статуса 'vehicle_previews' (Used)"]
    Step4 --> Commit{"Успешно?"}
    Commit -->|Да| EndTx["COMMIT TRANSACTION"]
    Commit -->|Нет / Error| RollbackTx["ROLLBACK TRANSACTION"]

```

---

## 16. Правила идемпотентности (Idempotency)

1. **Защита от дублирования:** Запрещено создание двух активных автомобилей с одинаковым парами параметров `(UserId + VIN)`.
2. **Повторные запросы:** Повторный вызов метода создания ТС с тем же `previewId` не создает новых записей в БД, а возвращает уже созданный объект `Vehicle` (или ошибку `409 Conflict`).

---

## 17. Обработка конкурентных запросов (Concurrency Considerations)

Для предотвращения состояния гонки (Race Condition) при одновременном нажатии кнопки подтверждения с нескольких устройств:

```mermaid
flowchart TD
    ReqA["Request A (Client 1)"] --> ProcessA["App Processing"]
    ReqB["Request B (Client 2)"] --> ProcessB["App Processing"]
    
    ProcessA --> DB1["INSERT INTO vehicles (user_id, vin)"]
    ProcessB --> DB2["INSERT INTO vehicles (user_id, vin)"]
    
    DB1 --> UniqueCheck{"Database Unique Index<br/>UNIQUE(user_id, vin)"}
    DB2 --> UniqueCheck
    
    UniqueCheck -->|First Executed| Success["201 Created (Vehicle Saved)"]
    UniqueCheck -->|Second Executed| DuplicateError["Constraint Violation -> 409 Conflict"]

```

---

## 18. Мониторинг и телеметрия (Observability)

Каждый компонент модуля обогащает контекст выполнения (Log Context) следующими метриками и атрибутами:

| Атрибут | Описание | Применение |
| --- | --- | --- |
| `requestId` | Сквозной UUID HTTP-запроса | Трассировка через Jaeger / Zipkin |
| `userId` | Идентификатор авторизованного пользователя | Анализ действий пользователя |
| `vin` | Маскированный VIN (e.g. `XTA2107****123456`) | Анализ корректности распознавания |
| `providerDurationMs` | Время отклика внешнего API VIN | Мониторинг SLA сторонних провайдеров |
| `dbQueryDurationMs` | Время выполнения SQL-запросов | Оптимизация индексов БД |

---

## 19. Безопасность и авторизация (Security)

```mermaid
flowchart TD
    Request["Входящий HTTP-запрос"] --> AuthCheck{"Валидность JWT токена?"}
    AuthCheck -->|Нет| 401["401 Unauthorized"]
    AuthCheck -->|Да| ExtrUser["Извлечение userId из Claims"]
    ExtrUser --> OwnerCheck{"Принадлежит ли vehicle_id пользователю userId?"}
    OwnerCheck -->|Нет| 403["403 Forbidden"]
    OwnerCheck -->|Да| Execute["Выполнение бизнес-операции"]

```

---

## 20. Стратегия тестирования (Testing Strategy)

```mermaid
flowchart TD
    E2E["E2E Tests<br/>(Полные пользовательские сценарии API)"]
    Contract["Contract Tests<br/>(Проверка схемы OpenAPI и ответов VIN API)"]
    Integration["Integration Tests<br/>(Repository + PostgreSQL + Docker Compose)"]
    Unit["Unit Tests<br/>(Domain Services, VIN Validation Rules, DTO Mappers)"]

    E2E --> Contract
    Contract --> Integration
    Integration --> Unit

```

---

## 21. Матрица трассируемости (Traceability Matrix)

| Требование / Use Case | API Endpoint | Компонент модуля | Метод |
| --- | --- | --- | --- |
| **UC-001 (Поиск ТС по VIN)** | `POST /api/v1/vehicles/lookup` | Vehicle Controller -> App Service -> Provider Adapter | `lookupVehicleByVin()` |
| **UC-001 (Подтверждение ТС)** | `POST /api/v1/vehicles` | Vehicle Controller -> App Service -> Repository | `createVehicle()` |
| **UC-002 (Просмотр гаража)** | `GET /api/v1/vehicles` | Vehicle Controller -> App Service -> Repository | `getUserVehicles()` |
| **UC-003 (Удаление ТС)** | `DELETE /api/v1/vehicles/{id}` | Vehicle Controller -> App Service -> Repository | `deleteVehicle()` |

---

## 22. Статус документа

| Параметр | Значение |
| --- | --- |
| **Уровень C4** | C3 — Component Diagram |
| **Модуль** | Vehicle Module |
| **Статус** | Approved |
| **Версия** | 1.0.0 |
| **Авторы** | System Analysis Team |
