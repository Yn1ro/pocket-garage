# Data Model — Карманный гараж

> Логическая модель данных MVP платформы «Карманный гараж».

---

# 1. Назначение

Документ определяет основные бизнес-сущности MVP, их атрибуты, связи и ограничения целостности данных.

Модель является логической и не привязана к конкретной СУБД.

Физическая модель базы данных будет определена на следующем этапе.

---

# 2. Основные сущности

В MVP используются следующие основные сущности:

```text
User
Vehicle
Vehicle Preview
Maintenance Record
Expense
Service
Booking
````

---

# 3. Общая модель

```text
User
 │
 │ 1:N
 ↓
Vehicle
 │
 ├───────────────┐
 │               │
 │ 1:N           │ 1:N
 ↓               ↓
Maintenance    Expense
 │
 │
 ↓
Service

User
 │
 │ 1:N
 ↓
Booking
 │
 │ N:1
 ↓
Service

Booking
 │
 │ N:1
 ↓
Vehicle
```

---

# 4. Entity: User

## Назначение

Представляет зарегистрированного пользователя платформы.

## Атрибуты

| Поле          | Тип      | Обязательное | Описание                 |
| ------------- | -------- | -----------: | ------------------------ |
| id            | UUID     |           Да | Уникальный идентификатор |
| email         | string   |           Да | Email пользователя       |
| phone         | string   |          Нет | Номер телефона           |
| password_hash | string   |           Да | Хэш пароля               |
| created_at    | datetime |           Да | Дата создания            |
| updated_at    | datetime |           Да | Дата изменения           |

---

# 5. Entity: Vehicle

## Назначение

Центральная бизнес-сущность продукта — автомобиль пользователя.

## Атрибуты

| Поле            | Тип      | Обязательное | Описание                 |
| --------------- | -------- | -----------: | ------------------------ |
| id              | UUID     |           Да | Идентификатор автомобиля |
| vin             | string   |           Да | VIN                      |
| manufacturer    | string   |           Да | Производитель            |
| model           | string   |           Да | Модель                   |
| generation      | string   |          Нет | Поколение                |
| production_year | integer  |          Нет | Год выпуска              |
| engine_volume   | decimal  |          Нет | Объём двигателя          |
| fuel_type       | enum     |          Нет | Тип топлива              |
| created_at      | datetime |           Да | Дата создания            |
| updated_at      | datetime |           Да | Дата изменения           |

---

# 6. Vehicle Ownership

На уровне MVP автомобиль принадлежит пользователю.

Логическая связь:

```text
User 1 ───────── N Vehicle
```

Один пользователь:

```text
может иметь несколько автомобилей
```

Каждый автомобиль:

```text
связан как минимум с одним пользователем
```

---

# 7. VIN Constraints

VIN должен:

* быть обязательным;
* храниться в нормализованном формате;
* проходить серверную валидацию;
* иметь ограничение уникальности в соответствии с бизнес-правилами.

При этом необходимо различать:

```text
VIN
```

и

```text
Vehicle ID
```

VIN является бизнес-идентификатором автомобиля.

Vehicle ID является внутренним техническим идентификатором записи.

---

# 8. Entity: Vehicle Preview

## Назначение

Временное представление данных автомобиля, полученных от внешнего поставщика до подтверждения пользователем.

## Атрибуты

| Поле       | Тип      | Обязательное | Описание              |
| ---------- | -------- | -----------: | --------------------- |
| id         | UUID     |           Да | Идентификатор preview |
| vin        | string   |           Да | VIN                   |
| provider   | string   |           Да | Источник данных       |
| payload    | JSON     |           Да | Полученные данные     |
| expires_at | datetime |           Да | Время истечения       |
| created_at | datetime |           Да | Дата создания         |

---

# 9. Vehicle Preview Lifecycle

```text
Created
   ↓
Displayed
   ↓
Confirmed
   ↓
Vehicle Created
```

Альтернативный сценарий:

```text
Created
   ↓
Expired
```

или:

```text
Created
   ↓
Cancelled
```

После истечения срока preview он не должен использоваться для создания автомобиля.

---

# 10. Entity: Maintenance Record

## Назначение

Запись об обслуживании автомобиля.

## Атрибуты

| Поле             | Тип      | Обязательное | Описание             |
| ---------------- | -------- | -----------: | -------------------- |
| id               | UUID     |           Да | Идентификатор записи |
| vehicle_id       | UUID     |           Да | Автомобиль           |
| service_id       | UUID     |          Нет | Сервис               |
| maintenance_type | string   |           Да | Тип обслуживания     |
| mileage          | integer  |          Нет | Пробег               |
| performed_at     | datetime |           Да | Дата выполнения      |
| cost             | decimal  |          Нет | Стоимость            |
| description      | text     |          Нет | Описание             |
| created_at       | datetime |           Да | Дата создания        |

---

# 11. Maintenance Examples

Примеры типов обслуживания:

```text
Oil Change
Brake Pads Replacement
Air Filter Replacement
Cabin Filter Replacement
Timing Belt Replacement
Tire Service
Diagnostics
Repair
Other
```

На уровне бизнес-модели эти значения должны быть нормализованы в справочник или enum после уточнения требований.

---

# 12. Entity: Expense

## Назначение

Фиксирует расходы пользователя на автомобиль.

## Атрибуты

| Поле         | Тип      | Обязательное | Описание      |
| ------------ | -------- | -----------: | ------------- |
| id           | UUID     |           Да | Идентификатор |
| vehicle_id   | UUID     |           Да | Автомобиль    |
| category     | enum     |           Да | Категория     |
| amount       | decimal  |           Да | Сумма         |
| currency     | string   |           Да | Валюта        |
| expense_date | datetime |           Да | Дата          |
| mileage      | integer  |          Нет | Пробег        |
| description  | text     |          Нет | Описание      |
| created_at   | datetime |           Да | Дата создания |

---

# 13. Expense Categories

Предварительный набор:

```text
Fuel
Maintenance
Repair
Parts
Insurance
Car Wash
Tire Service
Evacuation
Other
```

---

# 14. Entity: Service

## Назначение

Представляет автосервис, зарегистрированный на платформе.

## Атрибуты

| Поле        | Тип      | Обязательное | Описание               |
| ----------- | -------- | -----------: | ---------------------- |
| id          | UUID     |           Да | Идентификатор          |
| name        | string   |           Да | Название               |
| description | text     |          Нет | Описание               |
| phone       | string   |          Нет | Телефон                |
| address     | string   |           Да | Адрес                  |
| latitude    | decimal  |          Нет | Географическая широта  |
| longitude   | decimal  |          Нет | Географическая долгота |
| rating      | decimal  |          Нет | Рейтинг                |
| status      | enum     |           Да | Статус                 |
| created_at  | datetime |           Да | Дата создания          |
| updated_at  | datetime |           Да | Дата изменения         |

---

# 15. Service Status

Предварительно:

```text
ACTIVE
INACTIVE
SUSPENDED
PENDING_VERIFICATION
```

---

# 16. Entity: Booking

## Назначение

Представляет заявку пользователя на обслуживание автомобиля.

## Атрибуты

| Поле           | Тип      | Обязательное | Описание             |
| -------------- | -------- | -----------: | -------------------- |
| id             | UUID     |           Да | Идентификатор        |
| user_id        | UUID     |           Да | Пользователь         |
| vehicle_id     | UUID     |           Да | Автомобиль           |
| service_id     | UUID     |           Да | СТО                  |
| requested_at   | datetime |           Да | Дата создания заявки |
| requested_date | datetime |          Нет | Желаемая дата        |
| service_type   | string   |          Нет | Тип услуги           |
| comment        | text     |          Нет | Комментарий          |
| status         | enum     |           Да | Статус               |
| created_at     | datetime |           Да | Дата создания        |
| updated_at     | datetime |           Да | Дата изменения       |

---

# 17. Booking Status

```text
REQUESTED
CONFIRMED
DECLINED
CANCELLED
COMPLETED
NO_SHOW
```

---

# 18. Relationships

## User → Vehicle

```text
1 : N
```

Один пользователь может иметь несколько автомобилей.

---

## Vehicle → Maintenance Record

```text
1 : N
```

Один автомобиль может иметь множество записей обслуживания.

---

## Vehicle → Expense

```text
1 : N
```

Один автомобиль может иметь множество расходов.

---

## Service → Maintenance Record

```text
1 : N
```

Одно СТО может выполнять множество работ.

Связь опциональна, поскольку пользователь может добавить обслуживание самостоятельно.

---

## User → Booking

```text
1 : N
```

Пользователь может создать множество заявок.

---

## Vehicle → Booking

```text
1 : N
```

Для одного автомобиля может существовать множество заявок.

---

## Service → Booking

```text
1 : N
```

Одно СТО может получать множество заявок.

---

# 19. Entity Relationship Diagram

```text
┌──────────────┐
│     User     │
├──────────────┤
│ id           │
│ email        │
│ phone        │
│ password_hash│
└──────┬───────┘
       │
       │ 1:N
       ↓
┌──────────────┐
│   Vehicle    │
├──────────────┤
│ id           │
│ vin          │
│ manufacturer │
│ model        │
│ year         │
│ engine       │
└──┬────────┬──┘
   │        │
   │ 1:N    │ 1:N
   ↓        ↓
┌────────┐ ┌──────────┐
│Maint.  │ │ Expense  │
│Record  │ │          │
└───┬────┘ └──────────┘
    │
    │ N:1
    ↓
┌──────────────┐
│   Service    │
├──────────────┤
│ id           │
│ name         │
│ address      │
│ rating       │
│ status       │
└──────┬───────┘
       │
       │ 1:N
       ↓
┌──────────────┐
│   Booking    │
├──────────────┤
│ id           │
│ user_id      │
│ vehicle_id   │
│ service_id   │
│ status       │
│ requested_at │
└──────────────┘
```

---

# 20. Data Integrity Rules

## DI-001

Vehicle не может существовать без владельца в рамках текущей модели MVP.

---

## DI-002

Maintenance Record должен ссылаться на существующий Vehicle.

---

## DI-003

Expense должен ссылаться на существующий Vehicle.

---

## DI-004

Booking должен ссылаться на существующие:

* User;
* Vehicle;
* Service.

---

## DI-005

Удаление Vehicle не должно приводить к неконтролируемому удалению истории обслуживания и расходов.

Стратегия удаления должна быть определена отдельно.

---

## DI-006

Сумма Expense не может быть отрицательной.

---

## DI-007

Стоимость Maintenance Record не может быть отрицательной.

---

## DI-008

Booking не может ссылаться на неактивный Service.

---

# 21. Soft Delete

Для критичных бизнес-сущностей предпочтительно рассмотреть soft delete.

Например:

```text
deleted_at
```

вместо физического удаления.

Это особенно актуально для:

* Vehicle;
* Maintenance Record;
* Expense;
* Booking.

Причина:

необходимость сохранения истории и аудита.

---

# 22. Audit

Для важных изменений в будущем рекомендуется хранить:

```text
created_at
updated_at
created_by
updated_by
```

Для критичных операций может использоваться отдельный Audit Log.

---

# 23. Data Ownership

| Entity             | Owner                   |
| ------------------ | ----------------------- |
| User               | Platform                |
| Vehicle            | User                    |
| Vehicle Preview    | Platform / User Context |
| Maintenance Record | User                    |
| Expense            | User                    |
| Service            | Service Provider        |
| Booking            | User + Service Provider |

---

# 24. Data Privacy

Пользователь должен иметь доступ только к своим данным.

В частности:

* Vehicle;
* Maintenance Record;
* Expense;
* Booking.

Публичными могут быть только данные, предусмотренные бизнес-моделью.

Например:

```text
Service
 ├── Name
 ├── Address
 ├── Services
 └── Rating
```

---

# 25. Indexes — Initial Proposal

На уровне физической модели необходимо рассмотреть индексы:

```text
User.email
Vehicle.vin
Vehicle.user_id
MaintenanceRecord.vehicle_id
Expense.vehicle_id
Booking.user_id
Booking.vehicle_id
Booking.service_id
Service.location
```

Конкретная стратегия индексации будет определена после анализа запросов и выбранной СУБД.

---

# 26. Open Questions

## OQ-DATA-001

Может ли один автомобиль принадлежать нескольким пользователям?

---

## OQ-DATA-002

Нужна ли отдельная сущность Vehicle Ownership?

---

## OQ-DATA-003

Нужно ли хранить полную историю изменения владельцев?

---

## OQ-DATA-004

Какие данные автомобиля являются обязательными?

---

## OQ-DATA-005

Нужно ли хранить документы автомобиля?

---

## OQ-DATA-006

Как хранить фотографии и чеки?

---

## OQ-DATA-007

Нужна ли отдельная сущность Attachment?

---

## OQ-DATA-008

Какой максимальный размер истории обслуживания?

---

## OQ-DATA-009

Нужно ли поддерживать несколько валют?

---

# 27. Future Entities

В следующих версиях могут появиться:

```text
Part
PartOffer
Seller
Review
Service
ServicePrice
MaintenancePlan
Reminder
Document
Attachment
Notification
Community
Post
Comment
InsurancePolicy
EmergencyRequest
AIRequest
AIRecommendation
```

Они намеренно не включены в текущий MVP.

---

# 28. Current Status

| Area               | Status              |
| ------------------ | ------------------- |
| Logical Data Model | Draft               |
| Core Entities      | Defined             |
| Attributes         | Initial             |
| Relationships      | Defined             |
| Integrity Rules    | Initial             |
| Privacy            | Initial             |
| Indexes            | Initial             |
| Physical Model     | Not Started         |
| Validation         | Not Completed       |
| Next Step          | Architecture Design |
| Owner              | Project Author      |
