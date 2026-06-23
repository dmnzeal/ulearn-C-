## 1. Описание предметной области и сущностей

Система предназначена для символьного (аналитического) дифференцирования математических выражений, заданных в виде LINQ-деревьев выражений.  
Входное выражение представляет собой функцию одной переменной типа `double`, содержащую операции сложения, умножения, а также вызовы функций синуса и косинуса.  
Ядро системы — статический класс `Algebra` с методом `Differentiate`, который принимает выражение и возвращает новое выражение, представляющее его производную.  
Для обхода дерева выражения используется встроенный класс `ExpressionVisitor`, от которого наследуется внутренний посетитель `DerivativeVisitor`, выполняющий дифференцирование каждого узла.  
Каждый тип узла (константа, параметр, бинарная операция, вызов метода) обрабатывается отдельным методом в посетителе.  
Для константы и параметра производная равна 0 и 1 соответственно.  
Для сложения производная равна сумме производных слагаемых; для умножения применяется правило произведения.  
Для синуса и косинуса вычисляются цепные правила с использованием соответствующих тригонометрических функций.  
При обнаружении неподдерживаемой операции выбрасывается исключение с информативным сообщением.  
Архитектура позволяет легко добавлять новые функции и операторы, расширяя посетитель новыми методами обработки.

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
