# My Labs 2-6

## Project Structure

- Lab2_BlockChain - chain building
- Lab3_BlockProcessor - SQLite
- Lab4_ - OOP classes
- Lab5_ - event_stream
- Lab6_ - validation

## Flowchart

```mermaid
graph TD
    A[CSV] --> B[Updater]
    B --> C[SQLite]
    C --> D[event_stream]
    D --> E[Processor]
    E --> F{Vote exists?}
    F -->|Yes| G[Add block]
    F -->|No| H[Wait]
    G --> I[Chain]
classDiagram
    Block --> ChainBuilder
    Vote --> ChainBuilder
    ChainBuilder --> BlockProcessor
python Lab5/main.py
