# 🚗 Pocket Garage («Карманный гараж»)
> **Product & System Analysis Case Study** — Цифровая экосистема для автовладельцев и автосервисов.

Проект представляет собой полный цикл проектирования микросервисной системы: от исследования целевой аудитории и формулирования продуктовых гипотез до составления REST API контрактов, моделей данных и архитектуры (C4 Model / Event-Driven Architecture).

---

## 📌 Навигация по документации

### 1. Product Analysis (`/docs/ru/product`)
* [📋 Постановка проблемы (Problem Statement)](docs/ru/product/problem-statement.md)
* [🎯 Целевая аудитория и Персоны (Target Audience & Personas)](docs/ru/product/personas.md)
* [💡 Видение продукта и Гипотезы (Vision & Hypotheses)](docs/ru/product/hypotheses.md)

### 2. Requirements & System Analysis (`/docs/ru/requirements`)
* [👥 Стейкхолдеры и Матрица Влияния](docs/ru/requirements/stakeholders.md)
* [📑 Бизнес-требования (Business Requirements)](docs/ru/requirements/business-requirements.md)
* [🎬 Use Case UC-001: Добавление автомобиля по VIN](docs/ru/requirements/use-cases/UC-001-add-vehicle.md)
* [🔌 REST API Contract (Vehicle Subsystem)](docs/ru/requirements/api/vehicle-api.md)
* [🗄️ Логическая модель данных (Data Model)](docs/ru/requirements/data-model.md)

### 3. Architecture & Infrastructure (`/docs/ru/architecture`)
* [🏗️ Системная архитектура (C4 Model, Sequence & Kafka Events)](docs/ru/architecture/system-architecture.md)

---

## 🛠️ Архитектурный стек системы

| Слой | Технологии / Паттерны |
| :--- | :--- |
| **Architecture Style** | Microservices, Event-Driven Architecture (EDA) |
| **API & Integration** | REST API, JSON Schema, OpenAPI 3.0, gRPC (internal) |
| **Messaging & Events** | Apache Kafka (Async Event Bus) |
| **Data Stores** | PostgreSQL (Database per Service), Redis (Caching / Rate Limit) |
| **Security** | OAuth 2.0, JWT (Access/Refresh Tokens), API Gateway |

---

## 📐 Ключевая схема системы (C4 Container Diagram)

```mermaid
graph TB
    Client[Mobile / Web Client] --> GW[API Gateway]
    GW --> US[User Service]
    GW --> VS[Vehicle Service]
    GW --> BS[Booking Service]
    
    VS --> DB_Veh[(PostgreSQL: Vehicles)]
    VS -.-> Kafka{{Apache Kafka}}
    Kafka --> Notif[Notification Service]
