## 1. Описание предметной области и сущностей

Система предоставляет Fluent API для построения описания графов в формате DOT, используемом инструментом GraphViz.  
Базовый класс `GraphBuilderBase` управляет графом и предоставляет методы `AddNode` и `AddEdge` для добавления элементов.  
`DotGraphBuilder` (наследник) создаёт направленный или ненаправленный граф через статические методы `DirectedGraph` и `UndirectedGraph`.  
`NodeBuilder` и `EdgeBuilder`, также наследующие `GraphBuilderBase`, позволяют настраивать атрибуты узлов и рёбер через метод `With`, принимающий делегат для установки свойств.  
Атрибуты (цвет, размер шрифта, метка, форма для узлов; вес для рёбер) задаются через классы `NodeAttributes` и `EdgeAttributes`.  
Fluent API гарантирует, что в каждом контексте доступны только соответствующие атрибуты, предотвращая ошибки, и архитектура легко расширяется новыми атрибутами без изменения основного API.

## 2. Диаграмма классов

```mermaid
classDiagram
    class GraphBuilderBase {
        <<abstract>>
        #Graph _graphData
        +AddNode(string) NodeBuilder
        +AddEdge(string, string) EdgeBuilder
        +Build() string
    }

    class DotGraphBuilder {
        +DirectedGraph(string) DotGraphBuilder
        +UndirectedGraph(string) DotGraphBuilder
    }

    class NodeBuilder {
        -GraphNode _nodeRef
        +With(Action~NodeAttributes~) GraphBuilderBase
    }

    class EdgeBuilder {
        -GraphEdge _edgeRef
        +With(Action~EdgeAttributes~) GraphBuilderBase
    }

    class NodeAttributes {
        -Dictionary _attributeStorage
        +Color(string) NodeAttributes
        +FontSize(int) NodeAttributes
        +Label(string) NodeAttributes
        +Shape(NodeShape) NodeAttributes
    }

    class EdgeAttributes {
        -Dictionary _attributeStorage
        +Color(string) EdgeAttributes
        +FontSize(int) EdgeAttributes
        +Label(string) EdgeAttributes
        +Weight(double) EdgeAttributes
    }

    class NodeShape {
        <<enum>>
        Ellipse
        Box
    }

    GraphBuilderBase <|-- DotGraphBuilder
    GraphBuilderBase <|-- NodeBuilder
    GraphBuilderBase <|-- EdgeBuilder

    NodeBuilder --> NodeAttributes : uses
    EdgeBuilder --> EdgeAttributes : uses
    NodeAttributes --> NodeShape : uses

    GraphBuilderBase --> NodeBuilder : creates
    GraphBuilderBase --> EdgeBuilder : creates

    style GraphBuilderBase fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style DotGraphBuilder fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style NodeBuilder fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style EdgeBuilder fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style NodeAttributes fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style EdgeAttributes fill:#93C5FD,stroke:#3B82F6,color:#1E3A8A,stroke-width:2px
    style NodeShape fill:#DBEAFE,stroke:#60A5FA,color:#1E3A8A,stroke-width:2px
```
