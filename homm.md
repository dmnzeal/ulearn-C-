## 1. Описание предметной области и сущностей

В игровой среде присутствуют объекты, размещённые на карте, и реализован механизм взаимодействия, позволяющий игроку воздействовать на эти объекты.  
Объекты могут обладать различными свойствами: принадлежать владельцу, вступать в бой или содержать ресурсы.
Игрок (Player) – основной субъект, имеющий идентификатор, возможность оценить свои силы, получить награду и погибнуть.  
Армия (Army) представляет боевую мощь, а Сокровища (Treasure) – ресурсную ценность.  
Объект карты (MapObject) – абстрактный базовый класс для всех игровых объектов.  
Система взаимодействий (Interaction) управляет контактом игрока с объектами через единый метод Make.  
Для различных аспектов взаимодействия определены три интерфейса: владения (IOwned), боеспособности (ICombat) и награды (ILoot).  
IOwned позволяет установить владельца объекта, ICombat даёт доступ к армии, а ILoot – к сокровищам.  
Interaction использует эти интерфейсы, не привязываясь к конкретным классам объектов.  
Player взаимодействует с Army при проверке боя и с Treasure при получении награды.

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
