## 1. Описание предметной области и сущностей

В системе реализована архитектура для управления роботами, разделяющая генерацию команд и их исполнение.  
Базовые компоненты: интеллектуальный агент (AI), вырабатывающий команды, и устройство (Device), исполняющее эти команды.  
Для разных типов роботов (строители, стрелки) существуют свои специализированные команды и соответствующие AI.  
Устройства представлены подвижной частью (Mover) и его расширенной версией для стрелков (ShooterMover), поддерживающей дополнительную логику укрытия.  
Ключевая особенность – использование ковариантности для интерфейса AI (возвращает команды производных типов) и контравариантности для устройства (принимает команды базовых типов).  
Это позволяет подключать AI с более специфичными командами к устройствам, ожидающим более общие команды, обеспечивая гибкость системы.  
Класс Robot связывает AI и устройство через обобщённый параметр команды, гарантируя типобезопасность.

## 2. Диаграмма классов

```mermaid
classDiagram
    class IRobotAI {
        <<interface>>
        +GetCommand() TCommand
    }

    class RobotAI {
        <<abstract>>
        +GetCommand() TCommand
    }

    class ShooterAI {
        +GetCommand() ShooterCommand
    }

    class BuilderAI {
        +GetCommand() BuilderCommand
    }

    class IDevice {
        <<interface>>
        +ExecuteCommand(TCommand) string
    }

    class Device {
        <<abstract>>
        +ExecuteCommand(TCommand) string
    }

    class Mover {
        +ExecuteCommand(IMoveCommand) string
    }

    class ShooterMover {
        +ExecuteCommand(IShooterMoveCommand) string
    }

    class Robot {
        +Robot(IRobotAI, IDevice)
        +Start(int) IEnumerable~string~
    }

    class RobotFactory {
        +Create(IRobotAI, IDevice) Robot
    }

    IRobotAI <|.. RobotAI
    RobotAI <|-- ShooterAI
    RobotAI <|-- BuilderAI

    IDevice <|.. Device
    Device <|-- Mover
    Device <|-- ShooterMover

    Robot --> IRobotAI : depends
    Robot --> IDevice : depends
    RobotFactory ..> Robot : creates
    RobotFactory ..> IRobotAI : uses
    RobotFactory ..> IDevice : uses

    style IRobotAI fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style RobotAI fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style ShooterAI fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style BuilderAI fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style IDevice fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style Device fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style Mover fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style ShooterMover fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style Robot fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
    style RobotFactory fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
