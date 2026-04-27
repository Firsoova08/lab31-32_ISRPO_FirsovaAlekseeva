## README.md для лабораторной работы №31-32
# Лабораторная работа №31-32: Entity Framework Core

**Выполнили:** Фирсова, Алексеева
**Группа:** isp-232

## Краткое описание работы

В данной лабораторной работе было создано ASP.NET Core Web API приложение для управления задачами (Task Manager) с использованием Entity Framework Core и SQLite. Реализованы CRUD операции, фильтрация, поиск, пагинация, статистика, а также массовое обновление и удаление задач.

---

## Полезные команды dotnet ef

| Команда | Описание |
|---------|----------|
| `dotnet ef migrations add Имя` | Создать новую миграцию |
| `dotnet ef database update` | Применить миграции к БД |
| `dotnet ef migrations list` | Показать список миграций |
| `dotnet ef migrations remove` | Удалить последнюю миграцию |
| `dotnet ef database update ИмяМиграции` | Откатиться до указанной миграции |

---

## Структура проекта

```
TaskDb/
├── Models/
│   ├── TaskItem.cs          # Модель задачи
│   └── TaskDtos.cs           # DTO для создания/обновления
├── Data/
│   └── AppDbContext.cs       # Контекст базы данных
├── Controllers/
│   └── TasksController.cs    # API контроллер
├── Migrations/               # Папка с миграциями (создаётся автоматически)
├── Program.cs                # Настройка приложения
├── appsettings.json          # Конфигурация (строка подключения)
├── appsettings.Development.json # Логирование SQL
├── taskdb.db                 # Файл базы данных SQLite
└── TaskDb.csproj             # Файл проекта
```

---

## Список всех реализованных маршрутов

| Метод | Маршрут | Описание |
|-------|---------|----------|
| GET | `/api/tasks` | Получить все задачи (с фильтрацией по `completed` и `priority`) |
| GET | `/api/tasks/{id}` | Получить задачу по ID |
| GET | `/api/tasks/search?query=&priority=&completed=` | Поиск по тексту, приоритету и статусу |
| GET | `/api/tasks/stats` | Получить статистику по задачам |
| GET | `/api/tasks/paged?page=&pageSize=` | Пагинация задач |
| GET | `/api/tasks/overdue` | Получить просроченные задачи |
| POST | `/api/tasks` | Создать новую задачу |
| PUT | `/api/tasks/{id}` | Обновить задачу |
| PATCH | `/api/tasks/{id}/complete` | Переключить статус выполнения |
| PATCH | `/api/tasks/complete-all` | Отметить все невыполненные как выполненные |
| DELETE | `/api/tasks/{id}` | Удалить задачу по ID |
| DELETE | `/api/tasks/completed` | Удалить все выполненные задачи |

---

## Таблица применённых миграций

| Миграция | Описание |
|----------|----------|
| `InitialCreate` | Создание таблицы `Tasks` с начальными данными (3 задачи) |
| `AddDueDateToTask` | Добавление поля `DueDate` (срок выполнения) в таблицу `Tasks` |

---

## Сравнительная таблица LINQ vs SQL

| LINQ | SQL |
|------|-----|
| `.Where(t => t.IsCompleted == false)` | `WHERE is_completed = 0` |
| `.OrderBy(t => t.CreatedAt)` | `ORDER BY created_at ASC` |
| `.OrderByDescending(t => t.CreatedAt)` | `ORDER BY created_at DESC` |
| `.Take(10)` | `LIMIT 10` |
| `.Skip(20).Take(10)` | `OFFSET 20 LIMIT 10` |
| `.Count()` | `SELECT COUNT(*)` |
| `.Any(t => t.Priority == "High")` | `SELECT EXISTS(...)` |
| `.GroupBy(t => t.Priority)` | `GROUP BY priority` |
| `.Select(t => t.Title)` | `SELECT title` |

---

## Сравнение: хранение в памяти vs EF Core + SQLite

| Концепция | Хранение в памяти (static List<T>) | EF Core + SQLite |
|-----------|-------------------------------------|------------------|
| Хранение данных | RAM (оперативная память) | Файл .db на диске |
| После перезапуска | Данные пропадают | Данные сохраняются |
| Поиск по условию | LINQ to Objects | LINQ to Entities → SQL |
| Создание структуры | Не нужно | Миграции (`dotnet ef`) |
| Начальные данные | Хардкод в коде | `HasData()` в миграции |
| Получение данных | `list.FirstOrDefault(...)` | `await db.Table.FindAsync(id)` |
| Добавление | `list.Add(item)` | `db.Table.Add(item)` + `SaveChangesAsync()` |
| Удаление | `list.Remove(item)` | `db.Table.Remove(item)` + `SaveChangesAsync()` |
| Масштабируемость | Ограничена RAM | Гигабайты данных |
| Транзакции | Нет | Встроены в EF Core |

---

## Итоговая таблица: команды и концепции

| Команда / концепция | Описание |
|---------------------|----------|
| `dotnet add package` | Установить NuGet-пакет |
| `dotnet tool install --global dotnet-ef` | Установить инструмент для миграций |
| `dotnet ef migrations add Имя` | Создать новую миграцию |
| `dotnet ef database update` | Применить миграции к БД |
| `dotnet ef migrations list` | Показать список миграций |
| `AppDbContext` | Главный класс EF Core — подключение к БД |
| `DbSet<T>` | Коллекция-таблица в коде C# |
| `HasData(...)` | Начальные данные (seed) |
| `AsQueryable()` | Начать построение отложенного запроса |
| `ToListAsync()` | Выполнить запрос, получить список |
| `FindAsync(id)` | Найти запись по первичному ключу |
| `SaveChangesAsync()` | Зафиксировать изменения в БД |
| `Where(t => ...)` | Фильтрация — `WHERE` в SQL |
| `OrderBy` / `OrderByDescending` | Сортировка — `ORDER BY` в SQL |
| `Skip(n).Take(m)` | Пагинация — `OFFSET n LIMIT m` |
| `CountAsync(...)` | Подсчёт записей — `SELECT COUNT(*)` |
| `GroupBy(...)` | Группировка — `GROUP BY` в SQL |
| `[Required]`, `[MaxLength]` | Атрибуты валидации + ограничения в БД |
| `DateTime?` (nullable) | Необязательное поле — `NULL` допустим |
| Code First | Подход: сначала код C#, потом БД |

## Главные выводы

1. **EF Core — это переводчик между C# и SQL.** Вы пишете LINQ, он автоматически генерирует и выполняет SQL-запросы, избавляя от ручного написания SQL.

2. **Миграции — это система контроля версий для структуры БД.** Как Git для кода, миграции позволяют отслеживать и применять изменения схемы базы данных.

3. **Code First удобнее, чем писать SQL вручную.** Изменил класс модели → создал миграцию → база данных обновлена автоматически.

4. **`SaveChangesAsync()` — ключевой момент.** До его вызова все изменения живут только в памяти приложения. Только после вызова изменения фиксируются в базе данных.

5. **`async/await` при работе с БД — это стандарт, а не опция.** Блокировать поток на время ожидания ответа от базы данных — плохая практика, которая снижает производительность сервера.