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

# Краткие ответы

- Что такое ORM и зачем она нужна, если есть database/sql?



- Как в GORM описать связи 1:N и M:N? Какие теги на полях вы использовали?



- Что делает AutoMigrate? В каких случаях его недостаточно?



- Чем Preload отличается от обычного Find/First? Когда его применять?


  
- Как в GORM обработать ошибку нарушения уникального индекса? Что вернёт БД/драйвер?


