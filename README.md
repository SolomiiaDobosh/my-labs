# BlockProcessor Project (Labs 2-6)

## Project Structure

- **Lab2_BlockChain/** - chain building logic (while added algorithm)
- **Lab3_BlockProcessor/** - SQLite database (BLOCKS, VOTES, PERSONS, SOURCES)
- **Lab4_/** - OOP classes: Block, Person, Source, Vote
- **Lab5_/** - event_stream table, updater and processor
- **Lab6_/** - pydantic validation + pytest tests

## Flowchart Diagram

```mermaid
flowchart TD
    A[CSV file] --> B[Updater]
    B --> C[(SQLite DB)]
    C --> D[event_stream]
    D --> E[BlockProcessor]
    E --> F{Unread events?}
    F -->|Yes| G[Read event]
    G --> H{Event type?}
    H -->|block| I[Get block from BLOCKS]
    H -->|vote| J[Add vote]
    I --> K[ChainBuilder]
    J --> K
    K --> L{view == len chain and vote exists?}
    L -->|Yes| M[Add block to chain]
    L -->|No| N[Wait]
    M --> O[Mark event processed]
    O --> D
    N --> D
    F -->|No| P[Wait 2 seconds]
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
