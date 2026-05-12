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
classDiagram
    class Block {
        +str id
        +int view
        +str desc
        +bytes img
    }
    class Vote {
        +str id
        +str block_id
        +int voter_id
        +datetime timestamp
        +int source_id
    }
    class Person {
        +int id
        +str name
        +str addr
    }
    class Source {
        +int id
        +str ip_addr
        +str country_code
    }
    class ChainBuilder {
        +list chain
        +set votes_received
        +process_block(block)
        +process_vote(vote)
        +build_chain()
    }
    class BlockProcessor {
        +process_all_pending()
        +run_loop()
    }
    class BlockDB {
        +insert(block)
        +get(id)
    }
    class VoteDB {
        +insert(vote)
        +get_all()
    }
    BlockProcessor --> ChainBuilder
    BlockProcessor --> BlockDB
    BlockProcessor --> VoteDB
    BlockDB --> Block
    VoteDB --> Vote
    erDiagram
    BLOCKS {
        TEXT id PK
        INTEGER view
        TEXT desc
        BLOB img
    }
    VOTES {
        TEXT id PK
        TEXT block_id FK
        INTEGER voter_id
        DATETIME timestamp
        INTEGER source_id
    }
    PERSONS {
        INTEGER id PK
        TEXT name
        TEXT addr
    }
    SOURCES {
        INTEGER id PK
        TEXT ip_addr
        TEXT country_code
    }
    event_stream {
        INTEGER id PK
        TEXT type
        TEXT entity_id
        INTEGER processed
        TIMESTAMP created_at
    }
    VOTES ||--|| BLOCKS : block_id
    VOTES ||--|| PERSONS : voter_id
    VOTES ||--|| SOURCES : source_id
