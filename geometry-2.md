## 1. Описание предметной области и сущностей

В системе трёхмерные тела (шар, параллелепипед, цилиндр, составное) обрабатываются через паттерн Visitor: каждый посетитель реализует обобщённый интерфейс, а тела принимают его через метод Accept. Конкретные посетители вычисляют ограничивающий параллелепипед или заменяют тела на него, рекурсивно обрабатывая составные объекты. Паттерн гарантирует обработку всех типов на этапе компиляции и позволяет легко добавлять новые операции без изменения существующих классов.

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
```
