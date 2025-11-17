# ЭФМО-01-25 Буров М.А. ПР6

# Описание проекта
Использование ORM (GORM). Модели, миграции и связи между таблицами.

# Требования к проекту
* Go 1.25+
* PostgreSQL 10+
* Git

# Версия Go
<img width="317" height="55" alt="image" src="https://github.com/user-attachments/assets/43f9087b-95b9-4c7d-86e9-746258c45c63" />

# Версия PostgreSQL
<img width="276" height="55" alt="image" src="https://github.com/user-attachments/assets/bca8a5ed-eb3b-4b1c-9eee-16fb8ec409e8" />

# Цели:
- Понять, что такое ORM и чем удобен GORM.
- Научиться описывать модели Go-структурами и автоматически создавать таблицы (миграции через AutoMigrate).
- Освоить базовые связи: 1:N и M:N + выборки с Preload.
- Написать короткий REST (2–3 ручки) для проверки результата.

# Структура проекта
Дерево структуры проекта: 
```
pz6-gorm/
├── internal/
│   ├── db/
│   │   └── postgres.go
│   ├── models/
│   │   └── models.go
│   └── http/
│       ├── router.go
│       └── handlers.go
├── cmd/
│   └── server/
│       └── main.go
├── go.mod
└── go.sum
```
# Краткое описание

ORM (Object-Relational Mapping) --- это технология сопоставления объектно-ориентированных структур программы с таблицами реляционной БД. 

GORM помог путём значительного упрощения кода: вместо написания параметризованных SQL-запросов с плейсхолдерами, разработчик работает со структурами Go, а GORM автоматически генерирует SQL. Кроме того, GORM предоставляет встроенную поддержку миграций (AutoMigrate), связей между таблицами (1:N, M:N) и безопасной подгрузки связанных данных (Preload), что экономит сотни строк кода. GORM также защищает от SQL-инъекций благодаря встроенной параметризации.

Используемый DB_DSN:
```
DB_DSN "host=127.0.0.1 user=postgres password=1234 dbname=pz6_gorm port=5433 sslmode=disable"
```

Возникшие проблемы:

При тестировании с помощью Postman запросы на создание пользователя (POST /users) и заметки (POST /notes) выдавали ошибки.

Для /users из-за отсутствия маршрута GET /users при запросе возвращался 404 page not found.

Для /notes при создании заметки возникала ошибка нарушения внешнего ключа SQLSTATE 23503, означающая, что указанный UserID не существует в таблице пользователей.

После удаления неправильно созданных данных и пересоздания запросов через Postman они начали работать корректно — пользователи успешно создавались и заметки создавались с валидным ссылочным ключом (единственное, первая заметка в итоге оказалась с ID 2...).

# Скриншоты

Проверка здоровья:

<img width="1376" height="727" alt="image" src="https://github.com/user-attachments/assets/28781ece-b6fd-4ae7-8e04-c8dd9cade751" />

Создание пользователя:

<img width="1361" height="749" alt="image" src="https://github.com/user-attachments/assets/7ddd9f40-d0bb-47ea-9510-97f2a4dcad17" />

Создание заметки:

<img width="1371" height="883" alt="image" src="https://github.com/user-attachments/assets/5ddfe2d6-de96-42ae-807e-87ad1408245b" />

Получение заметки:

<img width="1371" height="872" alt="image" src="https://github.com/user-attachments/assets/11b66fa4-7759-4aab-8291-c3aedb3907d5" />

Схема БД:

<img width="739" height="790" alt="image" src="https://github.com/user-attachments/assets/e1195327-01a2-43d5-8c4c-1ac36e1bcc57" />

# Ответы на контрольные вопросы

- Что такое ORM и зачем она нужна, если есть database/sql?

ORM (Object-Relational Mapping) — это технология, которая позволяет работать с базой данных через объекты и структуры языка программирования, а не писать SQL вручную. Она облегчает разработку, ускоряя написание кода, особенно для стандартных операций CRUD, а также автоматически управляет связями и миграциями. ORM помогает избежать ошибок при составлении запросов и защищает от SQL-инъекций.

Она может быть менее производительной и менее гибкой для сложных запросов по сравнению с прямой работой через database/sql и сырой SQL. Иногда для оптимизации всё равно нужно использовать собственные запросы.

- Как в GORM описать связи 1:N и M:N? Какие теги на полях вы использовали?

Связь 1:N (один ко многим):
```
type User struct {
	ID        uint      `gorm:"primaryKey"`
	Name      string    `gorm:"size:100;not null"`
	Email     string    `gorm:"size:200;uniqueIndex;not null"`
	Notes     []Note    // 1:N
	CreatedAt time.Time
	UpdatedAt time.Time
}
```
Связь M:N (многие ко многим):
```
type Note struct {
	ID        uint      `gorm:"primaryKey"`
	Title     string    `gorm:"size:200;not null"`
	Content   string    `gorm:"type:text"`
	UserID    uint      `gorm:"not null"`
	User      User
	Tags      []Tag     `gorm:"many2many:note_tags;"` // M:N
	CreatedAt time.Time
	UpdatedAt time.Time
}

type Tag struct {
	ID        uint      `gorm:"primaryKey"`
	Name      string    `gorm:"size:50;uniqueIndex;not null"`
	Notes     []Note    `gorm:"many2many:note_tags;"`
	CreatedAt time.Time
	UpdatedAt time.Time
}
```
Используются стандарты тегов GORM: primaryKey, not null, uniqueIndex, many2many для правильной миграции и построения связей.

- Что делает AutoMigrate? В каких случаях его недостаточно?

AutoMigrate автоматически создаёт таблицы и добавляет новые поля в базу по структурам моделей. Однако он не удаляет поля и не меняет типы существующих столбцов, чтобы избежать потерю данных. Для сложных миграций, удалений колонок или изменений типа лучше использовать специализированные инструменты миграций. AutoMigrate хорош для старта и небольших проектов.

- Чем Preload отличается от обычного Find/First? Когда его применять?

По умолчанию GORM при выборе записи не загружает связанные данные (например, связанные заметки или теги), чтобы экономить ресурсы. Обычный Find/First вернёт только основную сущность. Preload позволяет заранее загрузить связанные данные одним или минимальным числом запросов, что решает проблему большого количества запросов. Preload применяют, когда нужно сразу получить все связанные данные (например, вывести заметку вместе с её автором и тегами).
  
- Как в GORM обработать ошибку нарушения уникального индекса? Что вернёт БД/драйвер?

```
u := models.User{Name: in.Name, Email: in.Email}
if err := h.db.Create(&u).Error; err != nil {
    writeErr(w, http.StatusConflict, err.Error())
    return
}

```
Если вставляется запись с дублирующим значением поля с уникальным индексом, PostgreSQL вернёт ошибку с кодом SQLSTATE 23505. В GORM эта ошибка попадёт в .Error метода Create. Её можно обработать, проверяя код ошибки, и вернуть клиенту HTTP-ответ с кодом 409 (Conflict) и понятным сообщением, например, "email already exists".
