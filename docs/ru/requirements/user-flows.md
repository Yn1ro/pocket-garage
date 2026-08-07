# User Flows & UX Scenarios — «Карманный гараж»

> **Паспорт документа**
>
> | Параметр | Значение |
> | :--- | :--- |
> | **Продукт** | Цифровая экосистема «Карманный гараж» |
> | **Тип документа** | UX Scenarios & Sequence Flows |
> | **Версия** | 1.0 (MVP) |

---

## 1. Назначение

Документ описывает основные пользовательские сценарии (User Flows), логику переходов между экранами мобильного приложения и последовательность взаимодействия компонентов системы (Sequence Diagrams).

---

## 2. Сценарий 1: Добавление автомобиля по VIN (UF-001)

### 2.1. Диаграмма процесса (Flowchart)

```mermaid
flowchart TD
    Start([Пользователь открывает экран 'Гараж']) --> ClickAdd[Нажатие на кнопку 'Добавить авто']
    ClickAdd --> InputVIN[Ввод 17-значного VIN]
    InputVIN --> ValidateFormat{Формат VIN верен?}
    
    ValidateFormat -- Нет --> ShowError1[Отобразить ошибку валидации]
    ShowError1 --> InputVIN
    
    ValidateFormat -- Да --> RequestLookup[Отправка POST /vehicles/lookup]
    RequestLookup --> ProviderCheck{VIN найден у провайдера?}
    
    ProviderCheck -- Нет --> ManualInput[Предложить ручной ввод марки/модели]
    ManualInput --> SaveVehicle[Сохранение авто]
    
    ProviderCheck -- Да --> ShowPreview[Отобразить карточку Vehicle Preview]
    ShowPreview --> ConfirmAdd{Пользователь подтверждает?}
    
    ConfirmAdd -- Отмена --> CancelFlow([Возврат в Гараж])
    ConfirmAdd -- Да --> RequestCreate[Отправка POST /vehicles]
    RequestCreate --> SaveVehicle
    SaveVehicle --> ShowSuccess[Экран 'Автомобиль успешно добавлен'] --> End([Автомобиль в Гараже])

```

### 2.2. Sequence Diagram (Взаимодействие компонентов)

```mermaid
sequenceDiagram
    autonumber
    actor User as Пользователь
    participant App as Mobile App
    participant GW as API Gateway
    participant VS as Vehicle Service
    participant Ext as VIN Decoder API
    participant DB as PostgreSQL

    User->>App: Вводит VIN (17 символов)
    App->>App: Локальная валидация (Regex)
    App->>GW: POST /api/v1/vehicles/lookup {vin}
    GW->>VS: Forward request (Auth Verified)
    VS->>Ext: GET /vin-specs/{vin}
    alt VIN найден
        Ext-->>VS: 200 OK (Марка, Модель, Год, Двигатель)
        VS->>DB: Сохранение Vehicle Preview (TTL 15 мин)
        VS-->>App: 200 OK {previewId, specData}
        App-->>User: Отображение карточки предварительного просмотра
        User->>App: Нажатие "Добавить в гараж"
        App->>GW: POST /api/v1/vehicles {previewId}
        GW->>VS: Create Vehicle
        VS->>DB: INSERT into vehicles (user_id, vin, ...)
        VS-->>App: 201 Created {vehicleId}
        App-->>User: Переход на главный экран Гаража
    else VIN не найден / Ошибка сервиса
        Ext-->>VS: 404 / 502
        VS-->>App: 404 Vehicle Not Found
        App-->>User: Показ формы ручного заполнения
    end

```

---

## 3. Сценарий 2: Поиск СТО и Онлайн-запись (UF-002)

### 3.1. Диаграмма процесса (Flowchart)

```mermaid
flowchart TD
    Start([Раздел 'СТО и Сервис']) --> RequestGPS[Запрос геолокации]
    RequestGPS --> ShowMap[Отображение СТО на карте и списком]
    ShowMap --> ApplyFilter[Применение фильтров: Рейтинг / Тип работ]
    ApplyFilter --> SelectService[Выбор карточки СТО]
    SelectService --> ClickBook[Нажатие 'Записаться на ТО']
    ClickBook --> SelectCar[Выбор авто из 'Гаража']
    SelectCar --> SelectDate[Выбор даты, времени и типа услуги]
    SelectDate --> SendBooking[Отправка POST /bookings]
    SendBooking --> StatusPending[Статус: PENDING_CONFIRMATION]
    StatusPending --> WaitService{СТО подтверждает?}
    
    WaitService -- Отклонено --> StatusDeclined[Статус: DECLINED] --> NotifyUser1[Уведомление: Выберите другое время]
    WaitService -- Подтверждено --> StatusConfirmed[Статус: CONFIRMED] --> NotifyUser2[Push-уведомление + Напоминание]

```

---

## 4. Сценарий 3: Учет расходов и внесение ТО (UF-003)

### 4.1. Диаграмма процесса (Flowchart)

```mermaid
flowchart TD
    Start([Экран 'Расходы']) --> ClickAdd[Нажатие '+ Добавить расход']
    ClickAdd --> SelectCategory{Выбор категории}
    
    SelectCategory -- Заправка --> FuelForm[Объем л, цена/л, пробег]
    SelectCategory -- ТО / Ремонт --> MaintForm[Тип работ, СТО, стоимость]
    SelectCategory -- Прочее --> OtherForm[Категория, сумма, описание]
    
    FuelForm --> PhotoCheck{Загрузить чек?}
    MaintForm --> PhotoCheck
    OtherForm --> PhotoCheck
    
    PhotoCheck -- Да --> UploadPhoto[Снимок / Фото чека] --> SaveExpense
    PhotoCheck -- Нет --> SaveExpense[Сохранение расхода]
    
    SaveExpense --> UpdateStats[Перерасчет графиков и среднего расхода]
    UpdateStats --> End([Расход отображен в истории])

```

---

## 5. Таблица состояний и переходов (State Transitions)

### Состояния заявки на запись (Booking Statuses)

| Текущее состояние | Событие (Event) | Новое состояние | Инициатор |
| --- | --- | --- | --- |
| **`REQUESTED`** | Пользователь отправил форму | `REQUESTED` | Пользователь |
| **`REQUESTED`** | СТО подтвердила дату и время | `CONFIRMED` | СТО (Партнер) |
| **`REQUESTED`** | СТО отклонила заявку | `DECLINED` | СТО (Партнер) |
| **`REQUESTED`** | Пользователь отменил заявку | `CANCELLED` | Пользователь |
| **`CONFIRMED`** | Услуга успешно оказана | `COMPLETED` | СТО / Пользователь |
| **`CONFIRMED`** | Пользователь не приехал в назначенное время | `NO_SHOW` | СТО |
