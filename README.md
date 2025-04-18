# ZooERP

## Описание проекта

ZooERP — это веб-приложение для автоматизации бизнес-процессов Московского зоопарка:
- Управление животными (добавление, удаление, просмотр)
- Управление вольерами (добавление, удаление, уборка, просмотр)
- Организация кормлений (создание расписания, отметка кормления)
- Перемещение животных между вольерами
- Сбор статистики по зоопарку (количество животных, свободных вольеров)

Проект реализован в архитектурном стиле Clean Architecture с применением концепций Domain-Driven Design и снабжен API-документацией через Swagger.

---

## Структура проекта

```plaintext
ZooERP/                # Решение и корневой проект
├─ Domain/             # Слой домена (ядро)
│  ├─ Entities/        # Сущности: Animal, Enclosure, FeedingSchedule
│  ├─ Events/          # Доменные события: AnimalMovedEvent, FeedingTimeEvent
│  ├─ ValueObjects/    # Value Objects: AnimalName
│  └─ Interfaces/      # Контракты репозиториев (IAnimalRepository, IEnclosureRepository, IFeedingScheduleRepository)
├─ Application/        # Слой бизнес-логики
│  └─ Services/        # Сервисы: AnimalTransferService, FeedingOrganizationService, ZooStatisticsService
├─ Infrastructure/     # Слой инфраструктуры
│  └─ Persistence/     # In-memory реализации репозиториев
│     ├─ InMemoryAnimalRepository.cs
│     ├─ InMemoryEnclosureRepository.cs
│     └─ InMemoryFeedingScheduleRepository.cs
├─ Presentation/       # Слой представления (Web API)
│  ├─ Controllers/     # Контроллеры для Animals, Enclosures, FeedingSchedules
│  └─ Program.cs       # Точка входа, регистрация DI, Swagger, middleware
└─ ZooERP.sln          # Файл решения
```

---

## Основные технологии и зависимости

- **.NET 6.0** с SDK `Microsoft.NET.Sdk.Web`
- **ASP.NET Core Web API** (`builder.Services.AddControllers()`)
- **Swagger (Swashbuckle.AspNetCore 6.2.3)** для документирования API
- **Dependency Injection**: встроенный контейнер Microsoft.Extensions.DependencyInjection
- **NUnit 3** + **Microsoft.NET.Test.Sdk** для модульного тестирования

---

## Подходы и паттерны

### Clean Architecture
- **Domain** зависит ни от чего
- **Application** зависит от Domain (интерфейсы, модели)
- **Infrastructure** зависит от Domain и Application через интерфейсы
- **Presentation** зависит от Application и Domain через DI и сервисы

### Domain-Driven Design (DDD)
- **Entities** (Animal, Enclosure, FeedingSchedule) содержат логику:
    - Animal: Feed, Treat, MoveToEnclosure
    - Enclosure: AddAnimal, RemoveAnimal, CleanUp
    - FeedingSchedule: ChangeSchedule, MarkAsDone
- **Value Object** `AnimalName` инкапсулирует логику валидации имени
- **Доменные события** для важных изменений:
    - `AnimalMovedEvent`
    - `FeedingTimeEvent`

---

## Запуск и тестирование

1. **Сборка и запуск**
   ```bash
   dotnet run --project Root/Root.csproj
   ```
2. **Swagger UI**
   Откройте в браузере:
   ```
   https://localhost:5001/
   ```
   или
   ```
   https://localhost:5001/swagger
   ```
   Для ручного тестирования всех эндпоинтов.

3. **Unit-тесты**
   ```bash
   dotnet test
   ```
   Проверяет логику доменных методов (класс Animal и др.).

4. **Integration-тесты** (если реализованы)
    - Используется `Microsoft.AspNetCore.Mvc.Testing` для поднятия сервера в памяти
    - Примеры в `ZooERP.Tests/ApiIntegrationTests.cs`

---

## Покрытие тестами

- Юнит-тесты для `Animal.Feed()`, `Animal.Treat()`, `Animal.MoveToEnclosure()`
- Интеграционные тесты для основных эндпоинтов API
- Цель: не менее 65% покрытия кода

---
