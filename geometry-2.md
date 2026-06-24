## 1. Описание предметной области и сущностей

В системе реализован классический паттерн Visitor для работы с трёхмерными геометрическими телами (шар, параллелепипед, цилиндр, составное тело). Каждое тело определяет метод Accept, принимающий посетителя (IVisitor) и передающий управление соответствующему методу Visit. Посетители не хранят ссылок на тела, а только обрабатывают их как параметры. Конкретные посетители: BoundingBoxVisitor вычисляет минимальный ограничивающий параллелепипед, сохраняя результат в своём поле; BoxifyVisitor заменяет все простые тела на их параллелепипеды, рекурсивно обрабатывая составные объекты. Такой подход гарантирует обработку всех типов на этапе компиляции, исключает условные операторы и позволяет легко добавлять новые операции без изменения классов тел.

## 2. Диаграмма классов

```mermaid
classDiagram
    class Body {
        <<abstract>>
        +Vector3 Position
        +Accept(IVisitor visitor)*
    }

    class Ball {
        +double Radius
        +Accept(IVisitor visitor)
    }

    class RectangularCuboid {
        +double SizeX
        +double SizeY
        +double SizeZ
        +Accept(IVisitor visitor)
    }

    class Cylinder {
        +double SizeZ
        +double Radius
        +Accept(IVisitor visitor)
    }

    class CompoundBody {
        +IReadOnlyList~Body~ Parts
        +Accept(IVisitor visitor)
    }

    class IVisitor {
        <<interface>>
        +Visit(Ball ball)
        +Visit(RectangularCuboid cuboid)
        +Visit(Cylinder cylinder)
        +Visit(CompoundBody compound)
    }

    class BoundingBoxVisitor {
        -RectangularCuboid _result
        +GetResult() RectangularCuboid
        +Visit(Ball ball)
        +Visit(RectangularCuboid cuboid)
        +Visit(Cylinder cylinder)
        +Visit(CompoundBody compound)
    }

    class BoxifyVisitor {
        -Body _result
        +GetResult() Body
        +Visit(Ball ball)
        +Visit(RectangularCuboid cuboid)
        +Visit(Cylinder cylinder)
        +Visit(CompoundBody compound)
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
```
