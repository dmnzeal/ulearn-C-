## 1. Описание предметной области и сущностей

Система формирует список устройств, на которых до заданной даты произошли критические (серьёзные) сбои. Основные сущности — `Device` (устройство с идентификатором и названием) и `Failure` (сбой с типом, датой и ссылкой на устройство). Рефакторинг инкапсулировал данные в классы, вынес определение серьёзности в метод `IsSerious` и преобразование устаревших форматов – в отдельные вспомогательные методы.

## 2. Диаграмма классов

```mermaid
classDiagram
    class Device {
        +int Id
        +string Name
        +Device(int id, string name)
    }
    class Failure {
        +FailureType Type
        +DateTime Date
        +Device Device
        +Failure(FailureType type, DateTime date, Device device)
        +bool IsSerious()
    }
    class FailureType {
        <<enum>>
        UnexpectedShutdown
        ShortNonResponding
        HardwareFailures
        ConnectionProblems
    }
    class ReportMaker {
        +FindDevicesFailedBeforeDate(DateTime date, List~Failure~ failures) List~string~
        +FindDevicesFailedBeforeDateObsolete(...) List~string~
    }

    Failure --> Device : contains
    Failure --> FailureType : type
    ReportMaker ..> Failure : uses
    ReportMaker ..> Device : uses
    ReportMaker ..> FailureType : uses

    style Device fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
    style Failure fill:#2563EB,stroke:#1D4ED8,color:#FFFFFF,stroke-width:2px
    style FailureType fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style ReportMaker fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    linkStyle default stroke:#64748B,stroke-width:1.5px
