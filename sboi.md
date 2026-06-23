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
