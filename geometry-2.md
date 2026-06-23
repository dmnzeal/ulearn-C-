## 1. Описание предметной области и сущностей

В системе представлены трёхмерные геометрические тела (шар, параллелепипед, цилиндр и составное тело), для которых выполняются различные операции через паттерн Visitor. Для каждой операции создаётся отдельный класс-посетитель, реализующий обобщённый интерфейс IVisitor<T>, а каждое тело предоставляет метод Accept, принимающий такого посетителя и возвращающий результат. Конкретные посетители: BoundingBoxVisitor вычисляет минимальный ограничивающий параллелепипед, а BoxifyVisitor заменяет все простые тела на их параллелепипеды, рекурсивно обрабатывая составные. Паттерн гарантирует, что все типы тел обработаны на этапе компиляции, исключая условные операторы и приведения типов. Добавление новой фигуры потребует реализации всех методов Visit, что предотвращает ошибки. В итоге система легко расширяется новыми операциями без изменения существующих классов, становясь более модульной и устойчивой.

## 2. Диаграмма классов

```mermaid
classDiagram
    class Body {
        <<abstract>>
        +Vector3 Position
        +Accept(IVisitor) T*
    }

    class Ball {
        +double Radius
        +Accept(IVisitor) T
    }

    class RectangularCuboid {
        +double SizeX
        +double SizeY
        +double SizeZ
        +Accept(IVisitor) T
    }

    class Cylinder {
        +double SizeZ
        +double Radius
        +Accept(IVisitor) T
    }

    class CompoundBody {
        +IReadOnlyList~Body~ Parts
        +Accept(IVisitor) T
    }

    class IVisitor {
        <<interface>> (generic T)
        +Visit(Ball) T
        +Visit(RectangularCuboid) T
        +Visit(Cylinder) T
        +Visit(CompoundBody) T
    }

    class BoundingBoxVisitor {
        +Visit(Ball) RectangularCuboid
        +Visit(RectangularCuboid) RectangularCuboid
        +Visit(Cylinder) RectangularCuboid
        +Visit(CompoundBody) RectangularCuboid
    }

    class BoxifyVisitor {
        +Visit(Ball) Body
        +Visit(RectangularCuboid) Body
        +Visit(Cylinder) Body
        +Visit(CompoundBody) Body
    }

    Body <|-- Ball
    Body <|-- RectangularCuboid
    Body <|-- Cylinder
    Body <|-- CompoundBody

    IVisitor <|.. BoundingBoxVisitor
    IVisitor <|.. BoxifyVisitor

    CompoundBody *-- Body : contains

    Body ..> IVisitor : uses (Accept parameter)
    BoundingBoxVisitor ..> RectangularCuboid : creates
    BoxifyVisitor ..> Body : creates/returns

    style Body fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
    style Ball fill:#2563EB,stroke:#1D4ED8,color:#FFFFFF,stroke-width:2px
    style RectangularCuboid fill:#2563EB,stroke:#1D4ED8,color:#FFFFFF,stroke-width:2px
    style Cylinder fill:#2563EB,stroke:#1D4ED8,color:#FFFFFF,stroke-width:2px
    style CompoundBody fill:#2563EB,stroke:#1D4ED8,color:#FFFFFF,stroke-width:2px
    style IVisitor fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style BoundingBoxVisitor fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style BoxifyVisitor fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
