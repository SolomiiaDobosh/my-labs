# BlockProcessor: Система побудови ланцюжка блоків з SQLite та асинхронною обробкою подій

## Опис проєкту

Цей проєкт реалізує повноцінну систему обробки блоків (BlockProcessor), яка:

- читає вхідні дані (блоки та голоси) з CSV або консолі,
- зберігає їх у базі даних SQLite,
- використовує чергу подій (`event_stream`) для асинхронної обробки,
- будує валідний ланцюжок блоків згідно з правилами (голоси + порядок view).

Розроблено в рамках лабораторних робіт 2–6 курсу "Практика програмування".

## Архітектура системи

```mermaid
flowchart TD
    Джерела[Джерела даних]
    CSV[CSV файл]
    Console[Консольний ввід]

    Джерела --> CSV
    Джерела --> Console

    CSV --> LoadCSV[load_csv]
    Console --> Commands[команди]

    LoadCSV --> Updater[Updater]
    Commands --> Updater

    Updater --> ReadRows[Читання рядків]
    ReadRows --> InsertEvents[Вставка в BLOCKS/VOTES]
    InsertEvents --> InsertStream[INSERT INTO event_stream]

    InsertStream --> EventStream[(event_stream\nSQLite БД)]

    EventStream --> PeriodicProcessor[Періодичний процесор]
    PeriodicProcessor --> SelectEvents[SELECT FROM event_stream WHERE processed=0]

    SelectEvents --> ForEach{Для кожної події}

    ForEach -->|тип block| LoadBlock[Завантажити блок з BLOCKS]
    ForEach -->|тип vote| LoadVote[Завантажити голос з VOTES]

    LoadBlock --> CreateBlock[Створити об'єкт Block]
    LoadVote --> CreateVote[Створити об'єкт Vote]

    CreateBlock --> ChainBuilder[ChainBuilder]
    CreateVote --> ChainBuilder

    ChainBuilder --> MarkProcessed[UPDATE event_stream SET processed=1]

    MarkProcessed --> Result[Готовий ланцюжок блоків]
