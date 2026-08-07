# UC-001 — Добавление автомобиля по VIN

> Use Case описывает основной сценарий добавления автомобиля пользователем в Digital Garage с использованием VIN.

---

# 1. Use Case Information

| Parameter | Value |
|---|---|
| Use Case ID | UC-001 |
| Name | Add Vehicle by VIN |
| Russian Name | Добавление автомобиля по VIN |
| Priority | P0 |
| Status | Draft |
| Primary Actor | Vehicle Owner |
| Supporting Actors | Vehicle Data Provider |
| System | Карманный гараж |
| Related BR | BR-01, BR-02 |
| Project Stage | Requirements Analysis |

---

# 2. Goal

Предоставить пользователю возможность добавить автомобиль в Digital Garage посредством ввода VIN.

После успешного завершения сценария система должна создать сущность Vehicle и связать её с пользовательским аккаунтом.

---

# 3. Business Value

Сценарий является фундаментальным для продукта, поскольку большинство последующих функций должны работать в контексте конкретного автомобиля.

После добавления автомобиля становятся доступны:

- история обслуживания;
- расходы;
- напоминания;
- поиск запчастей;
- поиск СТО;
- рекомендации;
- документы.

---

# 4. Actors

## Primary Actor

### Vehicle Owner

Пользователь, который хочет добавить автомобиль в свой Digital Garage.

---

## Supporting Actor

### Vehicle Data Provider

Внешняя система, предоставляющая данные об автомобиле по VIN.

Пример данных:

- manufacturer;
- model;
- generation;
- production year;
- engine;
- modification.

---

# 5. Trigger

Пользователь выбирает действие:

> **Добавить автомобиль**

и выбирает способ идентификации:

> **VIN**

---

# 6. Preconditions

Перед началом сценария:

1. Пользователь зарегистрирован.
2. Пользователь авторизован.
3. Digital Garage доступен.
4. Система доступна.
5. Интеграция с Vehicle Data Provider активна.

---

# 7. Postconditions

## Success

После успешного выполнения:

1. автомобиль создан;
2. автомобиль связан с пользователем;
3. данные автомобиля сохранены;
4. автомобиль отображается в Digital Garage;
5. создан уникальный идентификатор Vehicle.

---

## Failure

При невозможности создать автомобиль:

- данные не должны быть сохранены частично;
- пользователь получает понятное сообщение;
- система сохраняет техническую информацию об ошибке в логах.

---

# 8. Main Success Scenario

### Step 1

Пользователь открывает Digital Garage.

### Step 2

Пользователь нажимает:

> Добавить автомобиль

### Step 3

Система отображает способы добавления автомобиля.

Например:

- VIN;
- государственный номер;
- ручной ввод.

### Step 4

Пользователь выбирает:

> VIN

### Step 5

Система отображает поле ввода VIN.

### Step 6

Пользователь вводит VIN.

### Step 7

Система выполняет client-side validation.

Проверяется:

- длина;
- допустимые символы;
- базовый формат.

### Step 8

Если формат корректен, система отправляет запрос на backend.

### Step 9

Backend проверяет VIN.

### Step 10

Backend отправляет запрос к Vehicle Data Provider.

### Step 11

Vehicle Data Provider возвращает данные автомобиля.

### Step 12

Backend нормализует полученные данные.

### Step 13

Система показывает пользователю найденную информацию.

Например:

```text
Toyota
Camry
XV70
2020
2.5
Petrol
````

### Step 14

Пользователь подтверждает автомобиль.

### Step 15

Backend создаёт сущность Vehicle.

### Step 16

Vehicle связывается с User.

### Step 17

Система возвращает успешный результат.

### Step 18

Пользователь видит автомобиль в Digital Garage.

---

# 9. Main Flow Diagram

```text
User
 │
 │ Add Vehicle
 ↓
Digital Garage
 │
 │ Select VIN
 ↓
VIN Input
 │
 │ Enter VIN
 ↓
Client Validation
 │
 ├── Invalid → Validation Error
 │
 ↓
Backend
 │
 │ Validate VIN
 ↓
Vehicle Data Provider
 │
 ├── Error → Integration Error
 │
 ↓
Vehicle Data
 │
 ↓
Normalize Data
 │
 ↓
Show Vehicle Preview
 │
 │ Confirm
 ↓
Create Vehicle
 │
 ↓
Link Vehicle → User
 │
 ↓
Digital Garage
```

---

# 10. Alternative Flows

## A1 — User enters invalid VIN format

### Trigger

VIN does not satisfy basic validation rules.

### Flow

1. User enters VIN.
2. Client validates input.
3. Validation fails.
4. System displays error.
5. User corrects VIN.

### Result

External API is not called.

---

## A2 — VIN not found

### Trigger

Vehicle Data Provider does not return a matching vehicle.

### Flow

1. Backend sends VIN request.
2. Provider returns `not found`.
3. Backend does not create Vehicle.
4. System informs user.
5. System offers manual entry if supported.

### Result

Vehicle is not created automatically.

---

## A3 — Multiple vehicle matches

### Trigger

External provider returns multiple possible records.

### Flow

1. Backend receives multiple results.
2. System does not automatically create Vehicle.
3. System displays available matches.
4. User selects the correct vehicle.
5. System creates Vehicle.

---

## A4 — Vehicle already exists

### Trigger

VIN is already associated with the current user.

### Flow

1. Backend checks existing records.
2. Matching Vehicle is found.
3. New duplicate is not created.
4. System informs user.
5. Existing Vehicle is displayed.

---

## A5 — Vehicle belongs to another account

### Trigger

VIN already exists in the platform but is associated with another account.

### Expected Behavior

The system must not disclose private information about the other account.

The user should receive a generic message indicating that the vehicle cannot be added in the current context or requires an ownership verification process.

---

# 11. Exception Flows

## E1 — Vehicle Data Provider unavailable

### Condition

External API returns timeout or service unavailable.

### System Behavior

1. Backend records technical error.
2. Backend does not create Vehicle.
3. User receives understandable message.
4. User can retry.

Example:

> Не удалось получить данные автомобиля. Попробуйте ещё раз.

Technical details must not be exposed to the user.

---

## E2 — Request timeout

### Condition

External provider does not respond within configured timeout.

### Behavior

System terminates the request according to timeout policy.

The user receives retry option.

---

## E3 — Internal server error

### Condition

Unexpected backend error.

### Behavior

1. Error logged.
2. Transaction rolled back if necessary.
3. User receives generic error message.
4. No partially created Vehicle remains.

---

# 12. Business Rules

## BRULE-001

VIN must satisfy the supported VIN format before external lookup.

---

## BRULE-002

A Vehicle must belong to at least one User account.

---

## BRULE-003

A VIN cannot create duplicate Vehicle records for the same User.

---

## BRULE-004

The system must not expose another user's private Vehicle information.

---

## BRULE-005

Vehicle creation must be atomic.

Either:

```text
Vehicle created
```

or:

```text
Vehicle not created
```

A partially created Vehicle must not remain in the database.

---

## BRULE-006

External Vehicle Data Provider data must be treated as external data and validated before persistence.

---

## BRULE-007

If mandatory vehicle data cannot be obtained, automatic creation must not be completed.

---

# 13. Functional Requirements

## FR-001 — Add Vehicle

The system shall allow an authenticated user to initiate vehicle creation.

---

## FR-002 — VIN Input

The system shall provide an input field for VIN.

---

## FR-003 — VIN Validation

The system shall validate VIN format before sending an external request.

---

## FR-004 — Vehicle Lookup

The backend shall request vehicle information from the configured Vehicle Data Provider.

---

## FR-005 — Vehicle Data Normalization

The backend shall normalize external vehicle data into the platform's internal data model.

---

## FR-006 — Vehicle Preview

The system shall display retrieved vehicle information before final creation.

---

## FR-007 — User Confirmation

The system shall require explicit user confirmation before creating the Vehicle.

---

## FR-008 — Vehicle Creation

The system shall create a Vehicle record after successful validation and user confirmation.

---

## FR-009 — User Association

The system shall associate the created Vehicle with the authenticated User.

---

## FR-010 — Duplicate Detection

The system shall detect an existing Vehicle associated with the current User.

---

## FR-011 — Error Handling

The system shall display an appropriate user-facing error when vehicle lookup fails.

---

## FR-012 — Retry

The system shall allow the user to retry a failed vehicle lookup where technically appropriate.

---

# 14. Input Data

## VIN

| Parameter | Description            |
| --------- | ---------------------- |
| Name      | vin                    |
| Type      | String                 |
| Required  | Yes                    |
| Source    | User                   |
| Purpose   | Vehicle identification |

---

# 15. Output Data

After successful lookup:

```json
{
  "manufacturer": "Toyota",
  "model": "Camry",
  "generation": "XV70",
  "productionYear": 2020,
  "engine": "2.5",
  "fuelType": "petrol"
}
```

> Example only. Actual provider response and internal model may differ.

---

# 16. Data Entities

Основные сущности:

```text
User
 │
 └── Vehicle
       │
       ├── VIN
       ├── Manufacturer
       ├── Model
       ├── Generation
       ├── Production Year
       ├── Engine
       └── Fuel Type
```

---

# 17. Entity Relationship

На логическом уровне:

```text
User
  │
  │ 1
  │
  │ N
  ↓
Vehicle
```

Relationship:

> One User can have multiple Vehicles.

---

# 18. State Model

Vehicle lifecycle:

```text
[Not Created]
      │
      │ Successful Lookup
      ↓
[Preview]
      │
      │ User Confirms
      ↓
[Active]
```

Failure:

```text
[Preview]
    │
    │ Cancel
    ↓
[Not Created]
```

---

# 19. Non-Functional Requirements

## NFR-001 — Performance

При нормальной доступности внешнего провайдера пользователь должен получить результат поиска в пределах согласованного SLA.

Для MVP целевой ориентир:

> P95 ≤ 3 seconds

без учёта длительных задержек внешнего провайдера.

---

## NFR-002 — Availability

Функция добавления автомобиля должна быть доступна в рамках общего SLA платформы.

---

## NFR-003 — Security

VIN и связанные данные должны передаваться по защищённому соединению.

---

## NFR-004 — Authorization

Пользователь должен иметь доступ только к своим Vehicle records согласно модели авторизации.

---

## NFR-005 — Observability

Ошибки интеграции должны логироваться с correlation/request ID.

---

## NFR-006 — Idempotency

Повторная обработка одного и того же запроса не должна приводить к созданию дубликатов Vehicle.

---

## NFR-007 — Data Integrity

Создание Vehicle должно обеспечивать целостность связанных данных.

---

# 20. Acceptance Criteria

## AC-001 — Successful Vehicle Creation

**Given**

Пользователь авторизован.

**And**

VIN имеет корректный формат.

**And**

Vehicle Data Provider возвращает данные.

**When**

Пользователь подтверждает найденный автомобиль.

**Then**

Система создаёт Vehicle.

**And**

Vehicle связывается с текущим User.

**And**

Vehicle отображается в Digital Garage.

---

## AC-002 — Invalid VIN

**Given**

Пользователь находится на форме добавления автомобиля.

**When**

Пользователь вводит VIN некорректного формата.

**Then**

Система отображает ошибку валидации.

**And**

запрос к внешнему Vehicle Data Provider не выполняется.

---

## AC-003 — VIN Not Found

**Given**

VIN имеет корректный формат.

**When**

Vehicle Data Provider не находит автомобиль.

**Then**

Vehicle не создаётся.

**And**

пользователь получает понятное сообщение.

---

## AC-004 — Duplicate Vehicle

**Given**

Vehicle с таким VIN уже связан с текущим User.

**When**

пользователь пытается добавить автомобиль повторно.

**Then**

новая запись Vehicle не создаётся.

**And**

система предлагает открыть существующий автомобиль.

---

## AC-005 — Provider Unavailable

**Given**

Vehicle Data Provider недоступен.

**When**

пользователь выполняет поиск.

**Then**

система отображает ошибку.

**And**

предоставляет возможность повторить запрос.

---

## AC-006 — User Cancels

**Given**

Vehicle information successfully retrieved.

**When**

пользователь отменяет добавление.

**Then**

Vehicle не создаётся.

---

# 21. Traceability

```text
BR-01
  ↓
BR-02
  ↓
UC-001
  ↓
FR-001 ... FR-012
  ↓
AC-001 ... AC-006
```

---

# 22. Requirement Traceability Matrix

| Business Requirement | Use Case | Functional Requirement | Acceptance Criteria |
| -------------------- | -------- | ---------------------- | ------------------- |
| BR-01                | UC-001   | FR-001, FR-008, FR-009 | AC-001              |
| BR-02                | UC-001   | FR-003, FR-004         | AC-002, AC-003      |
| BR-01                | UC-001   | FR-010                 | AC-004              |
| BR-02                | UC-001   | FR-011, FR-012         | AC-005              |
| BR-01                | UC-001   | FR-007                 | AC-006              |

---

# 23. Open Questions

Следующие вопросы требуют уточнения до реализации:

### OQ-001

Какой именно источник Vehicle Data Provider используется?

### OQ-002

Какие данные доступны по VIN?

### OQ-003

Есть ли ограничения по количеству VIN-запросов?

### OQ-004

Как обрабатываются автомобили, отсутствующие в базе?

### OQ-005

Нужно ли подтверждение владения автомобилем?

### OQ-006

Может ли один Vehicle принадлежать нескольким пользователям?

### OQ-007

Как происходит передача Vehicle при продаже автомобиля?

### OQ-008

Какие поля являются обязательными для создания Vehicle?

### OQ-009

Какой SLA доступности внешнего провайдера?

---

# 24. Risks

| Risk                     | Impact | Probability | Mitigation                      |
| ------------------------ | ------ | ----------- | ------------------------------- |
| External API unavailable | High   | Medium      | Retry / fallback                |
| Incorrect vehicle data   | High   | Medium      | User confirmation               |
| Duplicate Vehicle        | Medium | Medium      | Idempotency / unique constraint |
| Provider rate limits     | Medium | Medium      | Caching / rate limiting         |
| Data privacy issue       | High   | Low/Medium  | Authorization                   |
| Incomplete vehicle data  | Medium | High        | Manual completion               |

---

# 25. Current Status

| Area                    | Status                  |
| ----------------------- | ----------------------- |
| Use Case                | Draft                   |
| Main Flow               | Defined                 |
| Alternative Flows       | Defined                 |
| Exception Flows         | Defined                 |
| Business Rules          | Defined                 |
| Functional Requirements | Defined                 |
| NFR                     | Initial                 |
| Acceptance Criteria     | Defined                 |
| Open Questions          | Identified              |
| Validation              | Not Completed           |
| Next Step               | Data Model / API Design |
| Owner                   | Project Author          |
