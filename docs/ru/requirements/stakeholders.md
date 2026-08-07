# Stakeholder Analysis — Заинтересованные стороны

> Документ определяет ключевых участников экосистемы «Карманный гараж», их интересы, влияние на систему и основные взаимодействия.

---

# 1. Purpose

Stakeholder Analysis используется для определения:

- кто заинтересован в продукте;
- кто взаимодействует с системой;
- кто принимает решения;
- кто предоставляет данные;
- кто является потребителем результатов работы системы;
- какие конфликты интересов необходимо учитывать.

---

# 2. Stakeholder Categories

Stakeholders разделены на следующие группы:

1. End Users
2. Business Partners
3. Platform Operations
4. External Systems
5. Regulatory / External Actors

---

# 3. Stakeholder Matrix

| ID | Stakeholder | Category | Interest | Influence | Priority |
|---|---|---|---|---|---|
| ST-01 | Vehicle Owner | End User | High | High | P0 |
| ST-02 | Service Provider | Business Partner | High | High | P0 |
| ST-03 | Parts Seller | Business Partner | Medium | Medium | P1 |
| ST-04 | Platform Administrator | Operations | High | High | P0 |
| ST-05 | Platform Owner | Business | High | Very High | P0 |
| ST-06 | AI Provider | External System | Medium | Medium | P1 |
| ST-07 | Vehicle Data Provider | External System | High | High | P0 |
| ST-08 | Payment Provider | External System | Medium | High | P1 |
| ST-09 | Insurance Partner | Business Partner | Medium | Medium | P2 |

---

# 4. ST-01 — Vehicle Owner

## Description

Основной пользователь платформы и владелец автомобиля.

## Goals

- управлять информацией об автомобиле;
- контролировать обслуживание;
- контролировать расходы;
- находить СТО;
- получать техническую информацию;
- быстро решать возникающие проблемы.

## Needs

- простая регистрация;
- актуальные данные;
- понятные рекомендации;
- прозрачные цены;
- сохранение истории.

## Key Interactions

```text
User
 ↓
Vehicle
 ↓
Garage
 ↓
Maintenance
 ↓
Service
 ↓
Booking
````

## Success Criteria

Пользователь способен решить типовую задачу владения автомобилем без использования нескольких разрозненных сервисов.

---

# 5. ST-02 — Service Provider

## Description

Автосервис, предоставляющий услуги пользователям платформы.

## Goals

* получать новых клиентов;
* управлять входящими заявками;
* демонстрировать услуги;
* повышать загрузку;
* получать положительные отзывы.

## Needs

* профиль СТО;
* список услуг;
* цены;
* расписание;
* заявки;
* статусы заказов;
* коммуникация с клиентом.

## Key Interactions

```text
Service Provider
       ↓
Service Profile
       ↓
Service Catalog
       ↓
Booking Request
       ↓
Booking Status
```

## Business Value

Получение дополнительного канала привлечения клиентов.

---

# 6. ST-03 — Parts Seller

## Description

Продавец новых или бывших в употреблении автомобильных запчастей.

## Goals

* получать целевой трафик;
* продавать запчасти;
* показывать наличие;
* получать заказы.

## Needs

* каталог товаров;
* совместимость;
* цена;
* остатки;
* заказы.

## Key Interaction

```text
Vehicle
 ↓
VIN
 ↓
Part Search
 ↓
Compatibility
 ↓
Seller
 ↓
Purchase
```

---

# 7. ST-04 — Platform Administrator

## Description

Внутренний пользователь, отвечающий за эксплуатацию и модерацию платформы.

## Goals

* поддерживать качество данных;
* управлять пользователями;
* модерировать контент;
* контролировать сервисы;
* обрабатывать жалобы;
* предотвращать злоупотребления.

## Needs

* Admin Panel;
* User Management;
* Service Management;
* Content Moderation;
* Complaint Management;
* Audit Logs.

---

# 8. ST-05 — Platform Owner

## Description

Владелец продукта / бизнеса.

## Goals

* создать устойчивую бизнес-модель;
* увеличить пользовательскую базу;
* увеличить количество транзакций;
* контролировать unit economics;
* масштабировать платформу.

## Key KPIs

* MAU;
* Retention;
* Booking Conversion;
* GMV;
* Revenue;
* CAC;
* LTV;
* Conversion Rate.

---

# 9. ST-06 — AI Provider

## Description

Внешний AI-сервис, используемый для обработки пользовательских запросов и изображений.

## Responsibilities

* обработка запроса;
* классификация изображения;
* генерация объяснения;
* формирование структурированного результата.

## Constraints

AI не должен рассматриваться как источник гарантированного технического диагноза.

---

# 10. ST-07 — Vehicle Data Provider

## Description

Внешний источник данных об автомобиле.

Может предоставлять:

* марку;
* модель;
* модификацию;
* год выпуска;
* двигатель;
* VIN-related information.

## Risks

* недоступность API;
* ограничения лицензии;
* неполные данные;
* задержки;
* ошибки источника.

---

# 11. ST-08 — Payment Provider

## Description

Внешняя система для обработки платежей.

## Responsibilities

* создание платежа;
* подтверждение платежа;
* возврат;
* обработка статуса транзакции.

## Constraints

Платёжные данные не должны храниться непосредственно в системе без необходимости.

---

# 12. ST-09 — Insurance Partner

## Description

Внешний партнёр, предоставляющий страховые продукты.

## Potential Services

* ОСАГО;
* КАСКО;
* дополнительные страховые продукты.

---

# 13. Stakeholder Expectations

| Stakeholder           | Primary Expectation                       |
| --------------------- | ----------------------------------------- |
| Vehicle Owner         | Простое и надёжное управление автомобилем |
| Service Provider      | Новые клиенты и удобная обработка заявок  |
| Parts Seller          | Продажи целевым пользователям             |
| Administrator         | Контроль платформы                        |
| Platform Owner        | Рост и прибыльность                       |
| AI Provider           | Корректная интеграция                     |
| Vehicle Data Provider | Корректное использование API              |
| Payment Provider      | Корректные транзакции                     |
| Insurance Partner     | Качественные лиды                         |

---

# 14. Stakeholder Conflicts

В системе потенциально существуют конфликтующие интересы.

## Conflict 01 — User vs Service Provider

Пользователь заинтересован в минимальной стоимости.

СТО заинтересовано в максимальной выручке.

### Product Requirement

Платформа должна обеспечивать прозрачное отображение:

* стоимости;
* состава услуги;
* статуса цены;
* дополнительных работ.

---

## Conflict 02 — User vs Platform Owner

Пользователь заинтересован в независимых рекомендациях.

Платформа может получать деньги от рекламного размещения.

### Product Requirement

Платное продвижение должно быть явно отделено от органической выдачи.

---

## Conflict 03 — User vs AI

Пользователь хочет получить однозначный ответ.

AI может иметь недостаточную уверенность.

### Product Requirement

Ответ должен учитывать uncertainty и при необходимости рекомендовать профессиональную диагностику.

---

# 15. Power / Interest Matrix

## High Power / High Interest

* Platform Owner
* Vehicle Owner
* Service Provider
* Platform Administrator

### Strategy

**Manage Closely**

---

## High Power / Low Interest

* Payment Provider
* External Data Provider

### Strategy

**Keep Satisfied**

---

## Low Power / High Interest

* Parts Seller
* Community Members

### Strategy

**Keep Informed**

---

## Low Power / Low Interest

Вторичные внешние участники.

### Strategy

**Monitor**

---

# 16. Stakeholder → System Interaction

```text
                         ┌─────────────────────┐
                         │  Platform Owner     │
                         └──────────┬──────────┘
                                    │
                                    ↓
┌──────────────┐          ┌─────────────────────┐
│ Vehicle Owner│ ───────→ │ Карманный гараж     │
└──────────────┘          └──────────┬──────────┘
                                    │
                 ┌──────────────────┼──────────────────┐
                 ↓                  ↓                  ↓
        ┌────────────────┐  ┌────────────────┐  ┌───────────────┐
        │ Service        │  │ Parts Seller   │  │ AI Provider   │
        │ Provider       │  │                │  │               │
        └────────────────┘  └────────────────┘  └───────────────┘
                 │                  │
                 └──────────┬───────┘
                            ↓
                    External Systems
```

---

# 17. Stakeholder Requirements Traceability

Stakeholder needs должны быть связаны с требованиями.

Пример:

```text
Stakeholder
    ↓
Need
    ↓
Business Requirement
    ↓
Functional Requirement
    ↓
Use Case
```

Пример:

```text
Vehicle Owner
    ↓
Хочу быстро найти СТО
    ↓
BR-06 Service Discovery
    ↓
FR-XXX Service Search
    ↓
UC-002 Search Service
```

---

# 18. Stakeholder Interview Plan

В рамках Customer Discovery необходимо отдельно исследовать:

## Vehicle Owners

Questions:

* Как сейчас выбираете СТО?
* Что является главным критерием?
* Где храните историю обслуживания?
* Как отслеживаете ТО?
* Покупаете ли запчасти самостоятельно?

---

## Service Providers

Questions:

* Откуда приходят клиенты?
* Как принимаете заявки?
* Используете ли онлайн-запись?
* Как формируете цены?
* Какие проблемы возникают при работе с агрегаторами?

---

# 19. Stakeholder Validation

Текущая модель является предварительной.

Необходимо проверить:

* реальные роли;
* реальные потребности;
* willingness to use;
* willingness to pay;
* конфликт интересов;
* доступность интеграций.

---

# 20. Current Status

| Area                       | Status            |
| -------------------------- | ----------------- |
| Stakeholder Identification | Initial           |
| Stakeholder Matrix         | Defined           |
| Stakeholder Needs          | Hypothesis        |
| Stakeholder Conflicts      | Identified        |
| Interviews                 | Not Completed     |
| Validation                 | Not Completed     |
| Next Step                  | Use Case Analysis |
| Owner                      | Project Author    |
