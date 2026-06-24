## 1. Описание предметной области и сущностей

В игровой среде объекты могут иметь владельца, вступать в бой или содержать ресурсы; игрок (Player) оценивает силы, получает награду и может погибнуть.
Объект карты (MapObject) — абстрактный базовый класс, а система взаимодействий (Interaction) управляет контактом через метод Make.
Для владения, боя и награды определены интерфейсы IOwned, ICombat и ILoot.
Interaction использует эти интерфейсы, не привязываясь к конкретным классам объектов.
При проверке боя Player обращается к Army, а при получении награды — к Treasure.

## 2. Диаграмма классов

```mermaid
classDiagram
    class MapObject {
        <<abstract>>
    }

    class IOwned {
        <<interface>>
        +int Owner
    }

    class ICombat {
        <<interface>>
        +Army Army
    }

    class ILoot {
        <<interface>>
        +Treasure Treasure
    }

    class Player {
        +int Id
        +CanBeat(Army) bool
        +Consume(Treasure)
        +Die()
    }

    class Army {
        +int Power
    }

    class Treasure {
        +int Value
    }

    class Dwelling {
        +int Growth
    }

    class Mine {
        +int DailyIncome
    }

    class Wolves {
        +int PackSize
    }

    class Creeps {
        +string Name
    }

    class ResourcePile {
    }

    class Interaction {
        +Make(Player, MapObject)
    }

    MapObject <|-- Dwelling
    MapObject <|-- Mine
    MapObject <|-- Wolves
    MapObject <|-- Creeps
    MapObject <|-- ResourcePile

    IOwned <|.. Dwelling
    IOwned <|.. Mine

    ICombat <|.. Wolves
    ICombat <|.. Creeps
    ICombat <|.. Mine

    ILoot <|.. Creeps
    ILoot <|.. Mine
    ILoot <|.. ResourcePile

    Interaction ..> MapObject : works with
    Interaction ..> IOwned : uses
    Interaction ..> ICombat : uses
    Interaction ..> ILoot : uses
    Player ..> Army : checks
    Player ..> Treasure : receives

    style MapObject fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
    style IOwned fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style ICombat fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style ILoot fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style Player fill:#2563EB,stroke:#1D4ED8,color:#FFFFFF,stroke-width:2px
    style Army fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style Treasure fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style Dwelling fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style Mine fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style Wolves fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style Creeps fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style ResourcePile fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style Interaction fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
```
