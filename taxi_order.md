## 1. Описание предметной области и сущностей

Система управляет заказами такси (создание, назначение водителя, статусы, хронология), центральным агрегатом является `TaxiOrder`, содержащий клиента, адреса, водителя, статус и временную шкалу.  
Для имени и адресов используются Value-объекты (`PersonName`, `Address`), водитель (`Driver`) — Entity с именем и автомобилем (`Car`).  
`Car` и `OrderTimeline` (хранит времена ключевых событий) также являются Value-типами, наследуемыми от `ValueType<T>`, который автоматически реализует сравнение по свойствам.  
`TaxiOrder` наследует от `Entity<int>` и инкапсулирует бизнес-логику (`AssignDriver`, `Cancel`, `StartRide`, `FinishRide`) с проверкой допустимости действий по статусу.  
`TaxiApi` — фасад, делегирующий вызовы методам заказа, а `DriversRepository` занимается только получением водителей по ID.  
Архитектура следует DDD: богатая доменная модель, инкапсуляция, Value-объекты и репозитории.

## 2. Диаграмма классов

```mermaid
classDiagram
    %% Базовые абстрактные классы
    class Entity_Int {
        <<abstract>>
        +int Id
    }

    class ValueType_T {
        <<abstract>>
        +Equals(object) bool
        +Equals(T) bool
        +GetHashCode() int
        +ToString() string
    }

    %% Value-объекты
    class Car {
        +string Model
        +string Color
        +string PlateNumber
    }

    class PersonName {
        +string FirstName
        +string LastName
    }

    class Address {
        +string Street
        +string Building
    }

    class OrderTimeline {
        +DateTime CreationTime
        +DateTime DriverAssignmentTime
        +DateTime CancelTime
        +DateTime StartRideTime
        +DateTime FinishRideTime
    }

    %% Агрегаты и сущности
    class Driver {
        +PersonName Name
        +Car Car
    }

    class TaxiOrder {
        +PersonName ClientName
        +Address Start
        +Address Destination
        +Driver Driver
        +TaxiOrderStatus Status
        +OrderTimeline Timeline
        +UpdateDestination(Address)
        +AssignDriver(Driver, DateTime)
        +UnassignDriver()
        +Cancel(DateTime)
        +StartRide(DateTime)
        +FinishRide(DateTime)
        +GetDriverFullInfo() string
        +GetShortOrderInfo() string
    }

    %% Сервисы
    class ITaxiApi {
        <<interface>>
        +CreateOrderWithoutDestination(...) TaxiOrder
        +UpdateDestination(...)
        +AssignDriver(...)
        +UnassignDriver(...)
        +Cancel(...)
        +StartRide(...)
        +FinishRide(...)
        +GetDriverFullInfo(...) string
        +GetShortOrderInfo(...) string
    }

    class TaxiApi {
        +CreateOrderWithoutDestination(...) TaxiOrder
        +UpdateDestination(TaxiOrder, string, string)
        +AssignDriver(TaxiOrder, int)
        +UnassignDriver(TaxiOrder)
        +Cancel(TaxiOrder)
        +StartRide(TaxiOrder)
        +FinishRide(TaxiOrder)
        +GetDriverFullInfo(TaxiOrder) string
        +GetShortOrderInfo(TaxiOrder) string
    }

    class DriversRepository {
        +GetDriverById(int) Driver
    }

    %% Наследование
    Entity_Int <|-- TaxiOrder
    Entity_Int <|-- Driver
    ValueType_T <|-- Car
    ValueType_T <|-- PersonName
    ValueType_T <|-- Address
    ValueType_T <|-- OrderTimeline

    %% Композиция / Агрегация / Ассоциация
    TaxiOrder --> PersonName : contains
    TaxiOrder --> Address : contains (Start, Destination)
    TaxiOrder --> Driver : references (optional)
    TaxiOrder --> OrderTimeline : contains
    Driver --> PersonName : contains
    Driver --> Car : contains

    %% Зависимости сервисов
    TaxiApi --> TaxiOrder : manages
    TaxiApi --> DriversRepository : uses
    TaxiApi ..|> ITaxiApi : implements

    %% Стилизация
    style Entity_Int fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style ValueType_T fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style Car fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style PersonName fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style Address fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style OrderTimeline fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style Driver fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style TaxiOrder fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
    style ITaxiApi fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style TaxiApi fill:#2563EB,stroke:#1D4ED8,color:#FFFFFF,stroke-width:2px
    style DriversRepository fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
```
