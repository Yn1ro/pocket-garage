# API Contract — Vehicle

> Контракт API для сценария UC-001 «Добавление автомобиля по VIN».

---

# 1. Назначение

API предоставляет клиентскому приложению возможность:

1. проверить VIN;
2. получить данные автомобиля;
3. показать пользователю найденный автомобиль;
4. создать автомобиль после подтверждения пользователем;
5. получить список автомобилей пользователя.

API является контрактом между Frontend и Backend.

---

# 2. Архитектурный контекст

```text
┌──────────────┐
│   Frontend   │
│ Mobile / Web │
└──────┬───────┘
       │
       │ HTTPS / JSON
       ↓
┌────────────────────┐
│      Backend       │
│  Карманный гараж   │
└──────┬─────────────┘
       │
       │ HTTPS
       ↓
┌────────────────────┐
│ Vehicle Data       │
│ Provider           │
└────────────────────┘
````

---

# 3. Общие требования API

## 3.1. Протокол

HTTPS.

## 3.2. Формат

JSON.

## 3.3. Кодировка

UTF-8.

## 3.4. Версионирование

Используется версия API:

```text
/api/v1
```

---

# 4. Авторизация

Для защищённых endpoints используется:

```http
Authorization: Bearer <access_token>
```

Пользователь должен быть авторизован.

---

# 5. Endpoint: Поиск автомобиля по VIN

## POST

```http
POST /api/v1/vehicles/lookup
```

Назначение:

Получение информации об автомобиле по VIN без создания записи Vehicle.

---

# 6. Request

## Headers

```http
Authorization: Bearer <access_token>
Content-Type: application/json
X-Request-ID: <uuid>
```

---

## Body

```json
{
  "vin": "JTNB11HK5K3000001"
}
```

---

# 7. Request Parameters

| Поле | Тип    | Обязательное | Описание       |
| ---- | ------ | -----------: | -------------- |
| vin  | string |           Да | VIN автомобиля |

---

# 8. VIN Validation

Перед отправкой запроса Backend должен выполнить серверную валидацию.

Минимальные проверки:

1. значение не пустое;
2. длина соответствует ожидаемому формату;
3. используются допустимые символы;
4. VIN нормализован;
5. отсутствуют недопустимые символы.

---

# 9. Successful Response

## HTTP 200 OK

```json
{
  "data": {
    "vehiclePreviewId": "vp_01J123456789",
    "vin": "JTNB11HK5K3000001",
    "manufacturer": "Toyota",
    "model": "Camry",
    "generation": "XV70",
    "productionYear": 2020,
    "engine": {
      "volume": 2.5,
      "fuelType": "petrol"
    }
  }
}
```

---

# 10. Vehicle Preview

Важно разделять:

```text
Vehicle Preview
```

и

```text
Vehicle
```

На этапе lookup автомобиль ещё не должен считаться созданным.

Система сначала получает данные:

```text
VIN
 ↓
Lookup
 ↓
Vehicle Preview
 ↓
User Confirmation
 ↓
Vehicle Creation
```

Это предотвращает создание автомобилей без явного подтверждения пользователя.

---

# 11. Endpoint: Создание автомобиля

## POST

```http
POST /api/v1/vehicles
```

Назначение:

Создание автомобиля после подтверждения пользователем.

---

# 12. Request

```http
POST /api/v1/vehicles
Authorization: Bearer <access_token>
Content-Type: application/json
Idempotency-Key: <uuid>
X-Request-ID: <uuid>
```

Body:

```json
{
  "vehiclePreviewId": "vp_01J123456789"
}
```

---

# 13. Successful Response

## HTTP 201 Created

```json
{
  "data": {
    "id": "veh_01J123456789",
    "vin": "JTNB11HK5K3000001",
    "manufacturer": "Toyota",
    "model": "Camry",
    "generation": "XV70",
    "productionYear": 2020,
    "engine": {
      "volume": 2.5,
      "fuelType": "petrol"
    },
    "createdAt": "2026-08-07T12:00:00Z"
  }
}
```

---

# 14. Endpoint: Получение автомобилей пользователя

## GET

```http
GET /api/v1/vehicles
```

Назначение:

Получение автомобилей текущего пользователя.

---

# 15. Successful Response

## HTTP 200 OK

```json
{
  "data": [
    {
      "id": "veh_01J123456789",
      "manufacturer": "Toyota",
      "model": "Camry",
      "productionYear": 2020,
      "vinMasked": "JTNB********0001"
    }
  ],
  "meta": {
    "total": 1
  }
}
```

---

# 16. Получение конкретного автомобиля

## GET

```http
GET /api/v1/vehicles/{vehicleId}
```

---

# 17. Path Parameters

| Параметр  | Тип    | Обязательное | Описание                 |
| --------- | ------ | -----------: | ------------------------ |
| vehicleId | string |           Да | Идентификатор автомобиля |

---

# 18. Successful Response

## HTTP 200 OK

```json
{
  "data": {
    "id": "veh_01J123456789",
    "vinMasked": "JTNB********0001",
    "manufacturer": "Toyota",
    "model": "Camry",
    "generation": "XV70",
    "productionYear": 2020,
    "engine": {
      "volume": 2.5,
      "fuelType": "petrol"
    }
  }
}
```

---

# 19. HTTP Status Codes

| Status | Значение              | Использование               |
| -----: | --------------------- | --------------------------- |
|    200 | OK                    | Успешный GET / lookup       |
|    201 | Created               | Автомобиль создан           |
|    400 | Bad Request           | Некорректный запрос         |
|    401 | Unauthorized          | Пользователь не авторизован |
|    403 | Forbidden             | Нет доступа                 |
|    404 | Not Found             | Ресурс не найден            |
|    409 | Conflict              | Конфликт / дубликат         |
|    422 | Unprocessable Entity  | Ошибка бизнес-валидации     |
|    429 | Too Many Requests     | Превышен rate limit         |
|    500 | Internal Server Error | Внутренняя ошибка           |
|    502 | Bad Gateway           | Ошибка внешнего провайдера  |
|    503 | Service Unavailable   | Сервис временно недоступен  |

---

# 20. Error Response

Все ошибки должны возвращаться в едином формате.

```json
{
  "error": {
    "code": "VEHICLE_NOT_FOUND",
    "message": "Не удалось найти автомобиль по указанному VIN.",
    "requestId": "7f2c1d6e-1111-4444-8888-123456789abc"
  }
}
```

---

# 21. Error Codes

## VIN_INVALID

VIN имеет некорректный формат.

```json
{
  "error": {
    "code": "VIN_INVALID",
    "message": "Проверьте корректность VIN.",
    "requestId": "..."
  }
}
```

HTTP:

```text
400
```

---

## VEHICLE_NOT_FOUND

Автомобиль не найден у внешнего поставщика данных.

HTTP:

```text
404
```

---

## VEHICLE_ALREADY_EXISTS

Автомобиль уже добавлен текущим пользователем.

HTTP:

```text
409
```

---

## VEHICLE_PROVIDER_UNAVAILABLE

Внешний источник данных временно недоступен.

HTTP:

```text
502
```

---

## VEHICLE_ACCESS_DENIED

Пользователь не имеет доступа к указанному автомобилю.

HTTP:

```text
403
```

---

# 22. Idempotency

Создание автомобиля должно поддерживать идемпотентность.

Для:

```http
POST /api/v1/vehicles
```

клиент передаёт:

```http
Idempotency-Key: <uuid>
```

Если один и тот же запрос повторяется с тем же ключом, система не должна создавать несколько автомобилей.

---

# 23. Request ID

Каждый запрос должен иметь:

```http
X-Request-ID
```

Идентификатор используется для:

* поиска запроса в логах;
* расследования ошибок;
* мониторинга;
* поддержки пользователей.

---

# 24. Security

## 24.1. Authorization

Пользователь может работать только со своими Vehicle records.

---

## 24.2. VIN Privacy

В списках автомобилей VIN не должен отображаться полностью без необходимости.

Пример:

```text
JTNB********0001
```

---

## 24.3. Input Validation

Все входные данные должны валидироваться на Backend независимо от client-side validation.

---

## 24.4. External Data

Данные внешнего поставщика не должны автоматически считаться доверенными.

Backend должен:

1. валидировать ответ;
2. нормализовать данные;
3. проверить обязательные поля;
4. только после этого сохранять данные.

---

# 25. Sequence Diagram

```text
User
 │
 │ Enter VIN
 ↓
Frontend
 │
 │ POST /vehicles/lookup
 ↓
Backend
 │
 │ Validate VIN
 │
 │ GET external provider
 ↓
Vehicle Data Provider
 │
 │ Vehicle Data
 ↓
Backend
 │
 │ Normalize
 │
 │ Vehicle Preview
 ↓
Frontend
 │
 │ Display Preview
 │
 │ User confirms
 │
 │ POST /vehicles
 ↓
Backend
 │
 │ Create Vehicle
 ↓
Database
 │
 │ Vehicle Created
 ↓
Backend
 │
 │ 201 Created
 ↓
Frontend
 │
 ↓
Digital Garage
```

---

# 26. Business Rules → API Mapping

| Business Rule | API Implementation                  |
| ------------- | ----------------------------------- |
| BRULE-001     | VIN validation                      |
| BRULE-002     | User-Vehicle relationship           |
| BRULE-003     | Unique constraint / duplicate check |
| BRULE-004     | Authorization                       |
| BRULE-005     | Transaction                         |
| BRULE-006     | External response validation        |
| BRULE-007     | Required field validation           |

---

# 27. Functional Requirements → API Mapping

| Functional Requirement | API                               |
| ---------------------- | --------------------------------- |
| FR-001                 | POST /vehicles                    |
| FR-002                 | POST /vehicles/lookup             |
| FR-003                 | VIN validation                    |
| FR-004                 | Vehicle Data Provider integration |
| FR-005                 | Data normalization                |
| FR-006                 | Vehicle Preview                   |
| FR-007                 | POST /vehicles                    |
| FR-008                 | Vehicle creation                  |
| FR-009                 | User-Vehicle relation             |
| FR-010                 | Duplicate detection               |
| FR-011                 | Error model                       |
| FR-012                 | Retry                             |

---

# 28. Open Questions

## OQ-API-001

Какой конкретно внешний поставщик данных используется?

---

## OQ-API-002

Какие ограничения по количеству запросов существуют у поставщика?

---

## OQ-API-003

Нужно ли хранить полный VIN в базе данных?

---

## OQ-API-004

Как долго действителен Vehicle Preview?

---

## OQ-API-005

Нужно ли кэшировать результаты VIN lookup?

---

## OQ-API-006

Какие поля являются обязательными для создания Vehicle?

---

## OQ-API-007

Как обрабатываются изменения данных автомобиля после создания?

---

# 29. Future Considerations

В будущих версиях API могут появиться:

```text
PUT /api/v1/vehicles/{vehicleId}
DELETE /api/v1/vehicles/{vehicleId}
GET /api/v1/vehicles/{vehicleId}/history
GET /api/v1/vehicles/{vehicleId}/expenses
GET /api/v1/vehicles/{vehicleId}/maintenance
GET /api/v1/vehicles/{vehicleId}/documents
```

Они не входят в текущий MVP API Contract.

---

# 30. Current Status

| Area                 | Status         |
| -------------------- | -------------- |
| API Contract         | Draft          |
| Endpoints            | Defined        |
| Request Models       | Defined        |
| Response Models      | Defined        |
| Error Model          | Defined        |
| Security             | Initial        |
| Idempotency          | Defined        |
| Sequence             | Defined        |
| External Integration | Hypothesis     |
| Validation           | Not Completed  |
| Next Step            | Data Model     |
| Owner                | Project Author |
