# BlockProcessor Project (Labs 2-6)

## Project Structure

- **Lab2_BlockChain/** - логіка побудови ланцюга (while added algorithm)
- **Lab3_BlockProcessor/** - SQLite база даних (BLOCKS, VOTES, PERSONS, SOURCES)
- **Lab4_/** - OOP класи Block, Person, Source, Vote
- **Lab5/** - event_stream таблиця, updater та processor
- **Lab6/** - валідація pydantic + pytest тести


```mermaid
flowchart TD
    A[CSV файл] --> B[Updater]
    B --> C[(SQLite БД)]
    C --> D[event_stream]
    D --> E[BlockProcessor]
    E --> F{Є непрочитані події?}
    F -->|Так| G[Читаємо подію]
    G --> H{Тип події?}
    H -->|block| I[Отримуємо блок з BLOCKS]
    H -->|vote| J[Додаємо голос]
    I --> K[ChainBuilder]
    J --> K
    K --> L{view == len(chain) та є голос?}
    L -->|Так| M[Додаємо блок до ланцюга]
    L -->|Ні| N[Чекаємо]
    M --> O[Позначаємо подію як processed]
    O --> D
    N --> D
    F -->|Ні| P[Очікування 2 секунди]
    P --> D
