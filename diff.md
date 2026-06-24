## 1. Описание предметной области и сущностей

Система выполняет символьное дифференцирование выражений (сложение, умножение, синус, косинус) через LINQ-деревья, принимая функцию одной переменной double.
Ядро — класс Algebra с методом Differentiate, использующий ExpressionVisitor и посетитель DerivativeVisitor для обхода и обработки каждого узла.
Константы и параметры дают производные 0 и 1, для сложения и умножения применяются суммы производных и правило произведения соответственно.
Для синуса и косинуса вычисляются цепные правила, а при неподдерживаемых операциях выбрасывается исключение.
Архитектура легко расширяется добавлением новых функций и операторов через доработку посетителя.

## 2. Диаграмма классов

```mermaid
classDiagram
    class Algebra {
        +Differentiate(Expression~Func~double, double~~) Expression~Func~double, double~~
    }

    class DerivativeEvaluator {
        +Evaluate(Expression) Expression
    }

    class DerivativeVisitor {
        +VisitConstant(ConstantExpression) Expression
        +VisitParameter(ParameterExpression) Expression
        +VisitBinary(BinaryExpression) Expression
        +VisitMethodCall(MethodCallExpression) Expression
    }

    class ExpressionVisitor {
        <<abstract>>
    }

    class Expression {
        <<abstract>>
    }

    class ConstantExpression {
    }

    class ParameterExpression {
    }

    class BinaryExpression {
    }

    class MethodCallExpression {
    }

    class Math {
        +Sin(double) double
        +Cos(double) double
    }

    Algebra ..> DerivativeEvaluator : uses
    DerivativeEvaluator *-- DerivativeVisitor : contains
    DerivativeVisitor --|> ExpressionVisitor : inherits
    DerivativeVisitor ..> Expression : visits
    DerivativeVisitor ..> Math : calls

    Expression <|-- ConstantExpression
    Expression <|-- ParameterExpression
    Expression <|-- BinaryExpression
    Expression <|-- MethodCallExpression

    style Algebra fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
    style DerivativeEvaluator fill:#2563EB,stroke:#1D4ED8,color:#FFFFFF,stroke-width:2px
    style DerivativeVisitor fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style ExpressionVisitor fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style Expression fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style ConstantExpression fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style ParameterExpression fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style BinaryExpression fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style MethodCallExpression fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style Math fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
```
