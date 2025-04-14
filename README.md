# AI Enterprise TaskManager

## 1. Описание предметной области

### Проблема
Команды и организации перегружены проектами и задачами. Современные таск-менеджеры либо слишком примитивны, либо перегружены, не помогают пользователям эффективно работать.  
**AI Enterprise TaskManager** — это ИИ-ассистент и корпоративная платформа, объединяющая:
- автоматизацию работы с задачами,
- планирование проектов и ресурсов,
- коллаборацию в командах,
- прогнозирование сроков и загрузки.

### Основные бизнес-процессы и правила:
- Создание **организаций** и **команд**.
- Управление ролями: **администраторы**, **менеджеры**, **участники**, **гости**.
- Планирование проектов с зависимостями и сроками.
- ИИ предлагает приоритеты и прогнозы выполнения.
- Пользователи могут назначать задачи, комментировать, обмениваться файлами.
- Автоматизация: шаблоны, повторяющиеся задачи, триггеры.
- Метрики по эффективности, загрузке, дедлайнам.
- Поддержка подписки (Free / Pro / Enterprise).

---

## 2. Основные сущности

| Сущность         | Атрибуты                                                                 | Поведение / Методы                                                            |
|------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| **Organization** | id, name, domain, plan_type                                              | Управление участниками и командами                                            |
| **User**         | id, email, name, role (в контексте орг), preferences                     | Вход, настройка, переключение между организациями                             |
| **Team**         | id, org_id, name, members                                                 | Разделение пользователей по рабочим группам                                   |
| **Project**      | id, team_id, name, description, status                                    | Включает задачи, имеет владельца и сроки                                       |
| **Task**         | id, project_id, title, status, deadline, assignee_id, dependency_task_ids | Может быть зависимой, иметь теги, комментарии, вложения                        |
| **AutomationRule** | id, trigger_type, action_type, task_filters                           | Правила автоматизации (например, “если просрочена — изменить статус”)         |
| **AIProfile**    | id, user_id, model_version, behavior_settings                             | Настройки ИИ и фидбэк                                                         |
| **Report**       | id, type, range, metrics                                                  | Автоматически создаётся по задачам / проектам / командам                      |
| **Subscription** | org_id, tier, status, renewal_date                                        | Уровень подписки организации                                                  |

---

## 3. Агрегаты и границы консистентности

| Агрегат            | Включает                              | Причина                       | Консистентность       |
|--------------------|----------------------------------------|-------------------------------|------------------------|
| OrganizationAgg    | Organization, Teams, Users             | Управление участниками        | Строгая                |
| ProjectAgg         | Project, Tasks                         | План проекта                  | Умеренная              |
| TaskAgg            | Task, Comments, Attachments            | Единица планирования          | Строгая (в рамках задачи)|
| AutomationAgg      | AutomationRule                         | Реакция на события            | Умеренная              |
| ReportAgg          | Report                                 | Отчётность по данным          | Eventual Consistency   |

---

## 4. Контексты (Bounded Contexts)

| Контекст            | Ответственность                                         |
|---------------------|---------------------------------------------------------|
| **User Management** | Регистрация, роли, аутентификация                       |
| **Organization & Teams** | Создание и управление организациями и командами |
| **Project Planning** | Создание проектов, задач, зависимостей                 |
| **AI Assistant**     | Анализ задач, прогноз сроков, рекомендаций             |
| **Automation Engine**| Автоматизация по событиям                              |
| **Reporting**        | Генерация отчётов, аналитика                           |
| **Billing**          | Управление подписками                                  |

---

## 5. Диаграмма сущностей 

---
![Диаграмма сущностей в UML](https://www.plantuml.com/plantuml/png/ZLFBRjmm3BpxAtmku1yeYY85SabHD4KsUpQuziHLguT1qIcsIV-zicf35PP0zc93CnI7r90tKOXbSRQ15oFdzfudykOtYGc-VMdQzbrhnlPzUppUtUQJ9uTqFWWRFsLa38wChx7Pak_bFAF0VvfTun7ahipju4xWcC0l9ig7DV9Z8hekVG9FVRbVqXyZM4qFHyQM_BhYp7W2mmzOLJyVxhzcc8DLU_d6uGS6kVQf40SsStxSYeX2iiI2RIqAoAazFbBygMecraBFNHT2epKU2RqbGSOeHZDve0XkeJFy23-SUqcgYTNjcF-zX5hkfqM2opFri5ZeXREP9d0zD9cXORse4BcS9sC5l7lrl-PTyYQnkjmIarODkJ32zi_WU67F2T_fsGJk8qGIgCWzO0wipy5bvKn-kfXqOJCeYG_BgGnDJhzDVcqx-6tN6Hw_oFOZoUxyAbJj_EjhruMNlACQaRUhMTaDs3UWsVuRd2UqMOCteFXHeJbCqQdQRcxWnyNPlm00//www.plantuml.com/plantuml/png/ZLFBRjmm3BpxAtmku1yeYY85SabHD4KsUpQuziHLguT1qIcsIV-zicf35PP0zc93CnI7r90tKOXbSRQ15oFdzfudykOtYGc-VMdQzbrhnlPzUppUtUQJ9uTqFWWRFsLa38wChx7Pak_bFAF0VvfTun7ahipju4xWcC0l9ig7DV9Z8hekVG9FVRbVqXyZM4qFHyQM_BhYp7W2mmzOLJyVxhzcc8DLU_d6uGS6kVQf40SsStxSYeX2iiI2RIqAoAazFbBygMecraBFNHT2epKU2RqbGSOeHZDve0XkeJFy23-SUqcgYTNjcF-zX5hkfqM2opFri5ZeXREP9d0zD9cXORse4BcS9sC5l7lrl-PTyYQnkjmIarODkJ32zi_WU67F2T_fsGJk8qGIgCWzO0wipy5bvKn-kfXqOJCeYG_BgGnDJhzDVcqx-6tN6Hw_oFOZoUxyAbJj_EjhruMNlACQaRUhMTaDs3UWsVuRd2UqMOCteFXHeJbCqQdQRcxWnyNPlm00)


---


## 6. Взаимодействие контекстов

| Источник → Получатель | Что передаётся                            | Тип взаимодействия         |
|------------------------|-------------------------------------------|-----------------------------|
| Task → AI Assistant    | Список задач с приоритетами               | REST / Event                |
| Project → Reporting    | Статистика задач                          | Event / Batch               |
| Billing → Org Management | Обновление подписки                    | Event                       |
| Automation Engine → Task | Действия по задаче (смена статуса и т.п.) | Command / Internal Event    |

---

## 7. Доменные сценарии

### Сценарий 1: Создание проекта
- Менеджер создаёт проект в рамках команды.
- Добавляет задачи, настраивает зависимости.
- Задачи назначаются участникам.

### Сценарий 2: Автоматизация
- Админ настраивает правило: "Если задача просрочена — изменить статус".
- Событие от Task передаётся в Automation Engine.
- Статус задачи меняется автоматически.

### Сценарий 3: Ежемесячный отчёт
- Reporting собирает данные по завершённым задачам.
- Генерирует PDF-отчёт и отправляет менеджеру.

---

## 8. Доменные сервисы

| Сервис                | Задачи                                                                       |
|------------------------|------------------------------------------------------------------------------|
| **UserService**        | CRUD пользователей, роли, аутентификация                                     |
| **OrgService**         | Управление организациями и командами                                         |
| **ProjectService**     | Создание проектов, добавление задач, настройка зависимостей                  |
| **TaskService**        | CRUD задач, изменение статуса, комментарии                                   |
| **AutomationService**  | Реакция на события, применение правил                                        |
| **AiService**          | Планирование, приоритезация, прогноз сроков                                  |
| **ReportService**      | Сбор метрик, генерация отчетов                                               |
| **SubscriptionService**| Подписка и ограничения функциональности по тарифу                            |
