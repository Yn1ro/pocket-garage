#  Pocket Garage («Карманный гараж»)

<p align="center">
  <img src="https://img.shields.io/badge/Status-Ready_for_Development-success?style=for-the-badge" alt="Status" />
  <img src="https://img.shields.io/badge/Architecture-C4_Model_%7C_EDA-blue?style=for-the-badge" alt="Architecture" />
  <img src="https://img.shields.io/badge/API_Spec-OpenAPI_3.0-green?style=for-the-badge" alt="OpenAPI" />
  <img src="https://img.shields.io/badge/Domain-Automotive_%7C_Marketplace-orange?style=for-the-badge" alt="Domain" />
</p>

---

##  О Проекте (Product Overview)

**«Карманный гараж»** - цифровая экосистема «единого окна» для автовладельцев, независимых СТО и магазинов автозапчастей. Проект решает проблему высокой фрагментации автосервисного рынка в РФ, объединяя учет расходов, автоматическое напоминание о ТО, поиск и запись в СТО, подбор запчастей по VIN и локальное автосообщество.

> **Цель этого репозитория:** Продемонстрировать полный сквозной цикл проектирования сложной продуктовой и микросервисной системы: от исследовательской аналитики (Product Discovery) до контрактов API и модели данных.

---

##  Автор Проекта и Компетенции

**Петр** - Product Manager / System Analyst

*  **Образование:** РЭУ им. Г.В. Плеханова - Бизнес-информатика («Управление ИТ-инфраструктурой»).
*  **Доменная экспертиза:** Дипломированный автомеханик - глубокое понимание внутренних бизнес-процессов СТО, логистики запчастей и болей автовладельцев изнутри.

---

##  Технологический и Аналитический Стек

* **Product Management:** Product Discovery, Customer Development, Personas, HASS/ICE Prioritization, Unit Economics, Funnel Analytics.
* **System Analysis:** Use Cases, Business Requirements (FR/NFR), Traceability Matrix, Logic Data Modeling, Sequence Diagrams.
* **Architecture & Specs:** C4 Model (Context, Container), Event-Driven Architecture (Apache Kafka), REST API, OpenAPI 3.0 (Swagger), Mermaid.js.

---

##  Структура и Навигатор по Документации

Вся документация проекта аккуратно распределена по разделам и готова к работе команды разработки:

### 1.  Продуктовый контур (`docs/ru/product/`)
* 📄 [**Проблематика продукта (Problem Statement)**](docs/ru/product/problem-statement.md) - Анализ фрагментации рынка СТО и потерь автовладельцев.
* 📄 [**Целевая аудитория и Персоны**](docs/ru/product/target-audience.md) - Сегментация пользователей (25–45 лет, EV-сегмент) и UX-персоны.
* 📄 [**Видение продукта и Гипотезы**](docs/ru/product/vision.md) - УТП, стратегический фокус и фреймворк ICE-приоритизации гипотез.
* 📄 [**Бизнес-модель и Юнит-экономика**](docs/ru/product/business-model.md) - B2B-комиссии 3-5%, InsurTech/Ads монетизация, KPI и план пилота в Усинске.

### 2. 📑 Требования и Системный Анализ (`docs/ru/requirements/`)
* 📄 [**Бизнес-требования (FR/NFR)**](docs/ru/requirements/business-requirements.md) — Спецификация функциональных требований и матрица трассировки.
* 📄 [**Use Case UC-001: Добавление ТС по VIN**](docs/ru/requirements/use-cases/UC-001-add-vehicle.md) - Подробный сценарий использования с обработкой ошибок.
* 📄 [**Логическая модель данных**](docs/ru/requirements/data-model.md) - 8 ключевых сущностей, правила целостности (DI Rules) и индексы.
* 📄 [**User Flows & Sequence Diagrams**](docs/ru/requirements/user-flows.md) - UX-диаграммы переходов и взаимодействия сервисов в Mermaid.
* 📄 [**Продуктовая аналитика**](docs/ru/requirements/product-analytics.md) - Tracking Plan событий, воронки конверсии для Amplitude/AppMetrica.

### 3.  Архитектура и API Контракты (`docs/ru/architecture/` и `api/`)
* 📄 [**Системная архитектура (C4 Model)**](docs/ru/architecture/system-architecture.md) - Context & Container диаграммы, шина Kafka, Redis, PostgreSQL.
* 📄 [**Контракт Vehicle API**](docs/ru/requirements/api/vehicle-api.md) - Описание REST-эндпоинтов, структур DTO и кодов ответов.
* 📄 [**OpenAPI 3.0 Specification**](docs/ru/requirements/api/openapi.yaml) - Машиночитаемая YAML-спецификация Swagger для генерации кода.

### 4.  Стратегия Развития
* 📄 [**Product Roadmap**](ROADMAP.md) - План разработки от MVP и локального пилота в Усинске до EV-трека и СНГ.

---

##  План пилотного запуска 

1. **B2B Onboarding:** Подключение 5-10 местной независимой СТО для обработки заказов.
2. **User Acquisition:** Привлечение первой 1 000 автовладельцев через региональные автоклубы.
3. **Unit Validation:** Проверка сходимости экономики на комиссии 3-5% с чека СТО.
