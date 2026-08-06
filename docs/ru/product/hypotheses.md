# Product Hypotheses — Продуктовые гипотезы

> Документ содержит ключевые гипотезы продукта «Карманный гараж», способы их проверки, метрики и критерии принятия решений.
>
> Все гипотезы являются предположениями до момента получения подтверждающих данных.

---

# 1. Purpose

Цель Product Discovery — определить, какие пользовательские проблемы действительно существуют и какие продуктовые решения имеют достаточную ценность для дальнейшей разработки.

Основной принцип:

> **Сначала проверяем проблему и ценность, затем инвестируем в разработку.**

---

# 2. Hypothesis Structure

Каждая гипотеза описывается через:

```text
Problem
   ↓
Hypothesis
   ↓
Assumption
   ↓
Risk
   ↓
Experiment
   ↓
Metric
   ↓
Success Criteria
   ↓
Decision
````

---

# 3. Hypothesis Statuses

| Status       | Meaning                                   |
| ------------ | ----------------------------------------- |
| Backlog      | Гипотеза сформирована, проверка не начата |
| In Discovery | Проводится исследование                   |
| Validated    | Получены достаточные подтверждения        |
| Invalidated  | Гипотеза не получила подтверждения        |
| Inconclusive | Данных недостаточно                       |
| Archived     | Гипотеза больше не является актуальной    |

---

# 4. H01 — Digital Garage

## Hypothesis

> Автовладельцы испытывают потребность в едином цифровом профиле автомобиля, где хранятся данные, история обслуживания и расходы.

### Problem

Информация об автомобиле хранится в различных местах.

### Assumption

Пользователь готов хранить данные автомобиля в одном приложении.

### Risk

Пользователь может не видеть достаточной ценности в переносе информации в новый сервис.

### Experiment

Customer Interviews.

Дополнительно:

Prototype Testing.

### Questions

* Где пользователь хранит историю обслуживания?
* Какие данные он хранит?
* Как часто ему требуется эта информация?
* Какие проблемы возникают при поиске старых данных?
* Был бы полезен единый профиль автомобиля?

### Metrics

* % пользователей, имеющих проблему с хранением информации;
* % пользователей, использующих несколько источников;
* % пользователей, выражающих интерес к Digital Garage.

### Success Criteria

Гипотеза считается предварительно подтверждённой, если значимая доля интервьюируемых:

1. уже использует несколько источников;
2. испытывает friction при поиске информации;
3. считает единый профиль полезным.

### Status

**Backlog**

---

# 5. H02 — Maintenance Reminders

## Hypothesis

> Автоматические напоминания о плановом обслуживании будут иметь практическую ценность для автовладельцев.

### Problem

Пользователь может забывать о плановом обслуживании.

### Assumption

Пользователь хочет получать автоматические рекомендации.

### Risk

Пользователь может воспринимать reminders как ненужные уведомления.

### Experiment

Prototype + MVP.

### Metrics

* Reminder Activation Rate;
* Reminder Open Rate;
* Reminder Completion Rate;
* D30 Retention.

### Success Criteria

Пользователи:

* создают reminders;
* взаимодействуют с ними;
* возвращаются к функции;
* выполняют рекомендованные действия.

### Status

**Backlog**

---

# 6. H03 — Service Marketplace

## Hypothesis

> Пользователи готовы искать и выбирать СТО через единый цифровой marketplace.

### Problem

Поиск СТО требует использования нескольких источников.

### Assumption

Пользователь доверяет агрегированной информации о сервисах.

### Risk

Пользователь может предпочитать знакомый сервис или рекомендации знакомых.

### Experiment

Concierge MVP.

На первом этапе поиск и запись могут обрабатываться частично вручную.

### Funnel

```text
Service Search
      ↓
Service View
      ↓
Price / Rating Review
      ↓
Booking Intent
      ↓
Booking
```

### Metrics

* Search → View Conversion;
* View → Booking Intent;
* Booking Completion Rate;
* Repeat Booking Rate.

### Success Criteria

Наблюдается стабильный переход пользователей от поиска к намерению записаться.

### Status

**Backlog**

---

# 7. H04 — Transparent Pricing

## Hypothesis

> Прозрачная стоимость типовых работ повышает доверие пользователя к выбору СТО.

### Problem

Пользователь часто не понимает стоимость работ до обращения в сервис.

### Assumption

Цена является одним из основных факторов выбора.

### Experiment

A/B Test / Prototype Test.

Сравнить:

**Variant A**

СТО без предварительной цены.

**Variant B**

СТО с ориентировочной стоимостью типовых работ.

### Metrics

* CTR;
* Booking Intent;
* Booking Conversion;
* Time to Decision.

### Success Criteria

Вариант с прозрачной ценой показывает более высокий conversion.

### Status

**Backlog**

---

# 8. H05 — VIN Parts Search

## Hypothesis

> Автоматический подбор запчастей по VIN снижает количество ошибок при выборе деталей и сокращает время поиска.

### Problem

Подбор деталей зависит от модификации автомобиля.

### Assumption

Пользователю сложно самостоятельно определить совместимость.

### Risk

Источники VIN / catalog data могут иметь неполные или некорректные данные.

### Experiment

Prototype.

Сценарий:

```text
VIN
 ↓
Vehicle
 ↓
Part Category
 ↓
Compatible Parts
 ↓
Price Comparison
```

### Metrics

* Search Completion Rate;
* Search Time;
* Zero-result Rate;
* Product Click-through Rate;
* Purchase Conversion.

### Success Criteria

Пользователь может получить релевантный список деталей быстрее, чем при ручном поиске.

### Status

**Backlog**

---

# 9. H06 — AI Dashboard Assistant

## Hypothesis

> Пользователь испытывает значимую потребность в быстром объяснении неизвестных предупреждений на приборной панели.

### Problem

Пользователь может не понимать значение индикатора.

### Assumption

Фото индикатора является достаточным первым шагом для получения полезной информации.

### Risk

AI может ошибиться при распознавании.

### Safety Constraint

AI не должен позиционироваться как замена профессиональной диагностике.

Ответ должен содержать:

* описание индикатора;
* возможные причины;
* оценку срочности;
* рекомендуемое действие;
* предупреждение о необходимости профессиональной диагностики при необходимости.

### Experiment

Prototype Testing.

### Scenario

```text
Photo
 ↓
Recognition
 ↓
Explanation
 ↓
Urgency
 ↓
Recommendation
```

### Metrics

* Recognition Success Rate;
* Feature Adoption;
* User Satisfaction;
* Recommendation Acceptance;
* Service Search Conversion.

### Success Criteria

Пользователь способен понять:

1. что означает индикатор;
2. насколько ситуация срочная;
3. какое действие рекомендуется следующим.

### Status

**Backlog**

---

# 10. H07 — AI → Service Conversion

## Hypothesis

> Пользователь, получивший объяснение ошибки, с большей вероятностью воспользуется предложением найти подходящий сервис.

### Problem

AI-информация сама по себе не решает физическую проблему автомобиля.

### Assumption

После получения информации пользователь хочет перейти к действию.

### Experiment

Prototype / MVP Funnel.

### Funnel

```text
AI Recognition
      ↓
Explanation
      ↓
Urgency
      ↓
Recommended Action
      ↓
Service Search
      ↓
Booking
```

### Metrics

* AI → Service Search;
* Service Search → Booking;
* Booking Completion;
* Time to Action.

### Success Criteria

Наблюдается измеримый переход из информационного сценария в transactional scenario.

### Status

**Backlog**

---

# 11. H08 — Cost Analytics

## Hypothesis

> Пользователи хотят видеть полную стоимость владения автомобилем.

### Problem

Расходы распределены между различными категориями.

### Assumption

Пользователь не имеет единого представления о стоимости владения.

### Experiment

Prototype Testing.

### Metrics

* Expense Entry Rate;
* Monthly Active Users of Analytics;
* Analytics View Frequency;
* Retention.

### Success Criteria

Пользователь регулярно возвращается к аналитике и использует её для принятия решений.

### Status

**Backlog**

---

# 12. H09 — Community

## Hypothesis

> Локальные автомобильные сообщества повышают retention и создают дополнительную ценность платформы.

### Problem

Автовладельцы уже используют отдельные сообщества и форумы.

### Assumption

Локальный контекст делает информацию более полезной.

### Experiment

Создать ограниченное локальное сообщество в рамках пилотного региона.

### Metrics

* DAU / WAU;
* Posts per Active User;
* Comments per Post;
* Returning Users;
* Community Retention.

### Success Criteria

Пользователи создают и потребляют пользовательский контент без постоянного стимулирования платформой.

### Status

**Backlog**

---

# 13. H10 — Online Booking

## Hypothesis

> Возможность записаться в СТО онлайн снижает friction по сравнению с телефонным звонком.

### Problem

Телефонная запись требует времени и синхронизации между пользователем и сервисом.

### Assumption

Пользователь предпочитает self-service booking.

### Experiment

Concierge MVP.

### Metrics

* Booking Start Rate;
* Booking Completion Rate;
* Drop-off Rate;
* Time to Booking.

### Success Criteria

Пользователи успешно завершают запись без телефонного взаимодействия.

### Status

**Backlog**

---

# 14. H11 — Vehicle History

## Hypothesis

> Единая история обслуживания увеличивает долгосрочную ценность продукта.

### Problem

История ремонта часто хранится фрагментарно.

### Assumption

Пользователь хочет сохранять историю автомобиля.

### Experiment

MVP.

### Metrics

* Service Record Creation;
* Records per Vehicle;
* Monthly Active Garage Users;
* Retention.

### Success Criteria

Пользователь добавляет историю нескольких обслуживаний и возвращается к ней.

### Status

**Backlog**

---

# 15. H12 — Freemium

## Hypothesis

> Пользователь готов платить за расширенные функции управления автомобилем.

### Potential Premium Features

* расширенная аналитика;
* несколько автомобилей;
* расширенные напоминания;
* история без ограничений;
* приоритетная поддержка;
* дополнительные AI-функции.

### Experiment

Fake Door / Pricing Test.

Показывается premium feature и измеряется interest.

### Metrics

* Paywall CTR;
* Trial Conversion;
* Subscription Conversion;
* Churn;
* ARPU.

### Success Criteria

Получен подтверждённый willingness to pay.

### Status

**Backlog**

---

# 16. Hypothesis Prioritization

Для первичной приоритизации используется **ICE Framework**.

```text
ICE Score = Impact × Confidence × Ease
```

Каждый параметр оценивается по шкале:

**1–10**

---

# 17. ICE Matrix

| ID  | Hypothesis            | Impact | Confidence | Ease | ICE |
| --- | --------------------- | -----: | ---------: | ---: | --: |
| H01 | Digital Garage        |      9 |          6 |    7 | 378 |
| H02 | Maintenance Reminders |      8 |          6 |    8 | 384 |
| H03 | Service Marketplace   |     10 |          5 |    4 | 200 |
| H04 | Transparent Pricing   |      8 |          5 |    6 | 240 |
| H05 | VIN Parts Search      |      9 |          5 |    5 | 225 |
| H06 | AI Assistant          |     10 |          4 |    5 | 200 |
| H07 | AI → Service          |      9 |          3 |    5 | 135 |
| H08 | Cost Analytics        |      7 |          5 |    8 | 280 |
| H09 | Community             |      6 |          3 |    5 |  90 |
| H10 | Online Booking        |      9 |          5 |    5 | 225 |
| H11 | Vehicle History       |      8 |          6 |    7 | 336 |
| H12 | Freemium              |      7 |          3 |    7 | 147 |

> ICE-оценка является предварительной и основана на экспертной оценке автора. После customer research значения должны быть пересмотрены.

---

# 18. Discovery Priority

На первом этапе приоритет получают гипотезы, которые:

1. имеют высокий потенциальный impact;
2. относительно дёшево проверяются;
3. уменьшают наиболее критичные риски продукта.

### Priority Group P0

* H01 — Digital Garage
* H02 — Maintenance Reminders
* H11 — Vehicle History
* H08 — Cost Analytics

### Priority Group P1

* H04 — Transparent Pricing
* H05 — VIN Parts Search
* H10 — Online Booking

### Priority Group P2

* H03 — Service Marketplace
* H06 — AI Assistant
* H07 — AI → Service

### Priority Group P3

* H09 — Community
* H12 — Freemium

---

# 19. Critical Assumptions

Наиболее рискованные предположения:

### A01

Пользователь готов хранить данные автомобиля в стороннем сервисе.

### A02

Пользователь доверяет рекомендациям платформы.

### A03

СТО готовы предоставлять актуальные цены.

### A04

СТО готовы принимать клиентов через платформу.

### A05

VIN data может быть получена и обработана с достаточной точностью.

### A06

AI способен безопасно объяснять распространённые dashboard warnings.

### A07

Пользователь готов перейти от информации к транзакции внутри одной платформы.

---

# 20. Riskiest Assumption

Наиболее критичная бизнес-гипотеза:

> **Пользователь будет готов доверить платформе управление значимой частью сценариев владения автомобилем.**

Если эта гипотеза не подтверждается, концепция SuperApp теряет значительную часть потенциальной ценности.

Поэтому необходимо проверить trust и willingness to use до масштабной разработки.

---

# 21. Experiment Backlog

| Experiment | Goal                         | Method              | Priority |
| ---------- | ---------------------------- | ------------------- | -------- |
| E01        | Проверить реальные боли      | Customer Interviews | P0       |
| E02        | Проверить Digital Garage     | Prototype           | P0       |
| E03        | Проверить reminders          | Prototype           | P0       |
| E04        | Проверить service discovery  | Concierge MVP       | P0       |
| E05        | Проверить online booking     | Concierge MVP       | P1       |
| E06        | Проверить VIN search         | Prototype           | P1       |
| E07        | Проверить AI Assistant       | Prototype           | P1       |
| E08        | Проверить willingness to pay | Fake Door           | P2       |
| E09        | Проверить community          | Local Pilot         | P2       |

---

# 22. Decision Framework

После каждого эксперимента принимается решение:

### KEEP

Гипотеза получает достаточное подтверждение.

Продолжаем развитие.

### ITERATE

Есть признаки ценности, но необходимо изменить решение.

### PIVOT

Проблема существует, но исходное решение не работает.

### KILL

Проблема или решение не получили достаточного подтверждения.

---

# 23. Evidence Hierarchy

Сила доказательства оценивается следующим образом:

```text
Assumption
    ↓
Opinion
    ↓
Interview Evidence
    ↓
Prototype Behavior
    ↓
MVP Behavior
    ↓
Transaction
    ↓
Repeated Behavior
```

Чем ближе доказательство к реальному поведению пользователя, тем выше его ценность.

---

# 24. Product Discovery Principle

> **Необходимо измерять не то, нравится ли пользователю идея, а то, меняет ли он своё поведение ради решения проблемы.**

Поэтому предпочтение отдаётся:

* behavioral data;
* prototype interactions;
* completed actions;
* bookings;
* purchases;
* retention.

а не только:

* likes;
* opinions;
* survey answers;
* verbal intent.

---

# 25. Current Status

| Area              | Status              |
| ----------------- | ------------------- |
| Hypotheses        | Defined             |
| Prioritization    | Initial             |
| Validation        | Not started         |
| Experiments       | Planned             |
| Customer Research | Not started         |
| Project Stage     | Product Discovery   |
| Next Step         | Customer Interviews |
| Owner             | Project Author      |
