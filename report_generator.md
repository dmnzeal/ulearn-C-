## 1. Описание предметной области и сущностей

Система предназначена для построения отчётов по измерениям (температура и влажность) с возможностью гибкой смены формата вывода и способа расчёта статистик.  
Основные компоненты: интерфейс форматтера (`IOutputFormatter`), отвечающий за представление данных (HTML или Markdown), и интерфейс калькулятора статистик (`IStatCalculator<TResult>`), выполняющий вычисления (среднее со стандартным отклонением или медиана).  
Конкретные форматтеры: `HtmlReportFormatter` и `MarkdownReportFormatter`.  
Калькуляторы: `MeanStdCalculator` и `MedianCalculator`.  
Сборка отчёта выполняется классом `ReportBuilder<TStat>`, который принимает форматтер и калькулятор и формирует итоговую строку.  
`ReportBuilder` последовательно вызывает методы форматтера для заголовка, начала списка, каждого статистического показателя и конца списка.  
Для каждого набора данных (температура, влажность) вызывается калькулятор, результат преобразуется в строку через форматтер.  
Статический класс `ReportMakerHelper` предоставляет четыре готовых метода для всех комбинаций (Mean/Std + HTML, Median + Markdown и т.д.).  
Такая архитектура позволяет легко добавлять новые форматы или статистики без изменения существующего кода.

## 2. Диаграмма классов

```mermaid
classDiagram
    class IOutputFormatter {
        <<interface>>
        +FormatHeader(string) string
        +FormatListStart() string
        +FormatListEnd() string
        +FormatItem(string, string) string
    }

    class IStatCalculator {
        <<interface>>
        +Title string
        +Compute(IEnumerable~double~) T
    }

    class MeanStdCalculator {
        +Title string
        +Compute(IEnumerable~double~) MeanAndStd
    }

    class MedianCalculator {
        +Title string
        +Compute(IEnumerable~double~) double
    }

    class HtmlReportFormatter {
        +FormatHeader(string) string
        +FormatListStart() string
        +FormatListEnd() string
        +FormatItem(string, string) string
    }

    class MarkdownReportFormatter {
        +FormatHeader(string) string
        +FormatListStart() string
        +FormatListEnd() string
        +FormatItem(string, string) string
    }

    class ReportBuilder {
        +ReportBuilder(IOutputFormatter, IStatCalculator)
        +Build(IEnumerable~Measurement~) string
    }

    class ReportMakerHelper {
        +MeanAndStdHtmlReport(IEnumerable~Measurement~) string
        +MedianMarkdownReport(IEnumerable~Measurement~) string
        +MeanAndStdMarkdownReport(IEnumerable~Measurement~) string
        +MedianHtmlReport(IEnumerable~Measurement~) string
    }

    IStatCalculator <|.. MeanStdCalculator
    IStatCalculator <|.. MedianCalculator
    IOutputFormatter <|.. HtmlReportFormatter
    IOutputFormatter <|.. MarkdownReportFormatter

    ReportBuilder --> IOutputFormatter : uses
    ReportBuilder --> IStatCalculator : uses
    ReportMakerHelper ..> ReportBuilder : creates

    style IOutputFormatter fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style IStatCalculator fill:#60A5FA,stroke:#2563EB,color:#1E3A8A,stroke-width:2px
    style MeanStdCalculator fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style MedianCalculator fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style HtmlReportFormatter fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style MarkdownReportFormatter fill:#3B82F6,stroke:#2563EB,color:#FFFFFF,stroke-width:2px
    style ReportBuilder fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
    style ReportMakerHelper fill:#1E3A8A,stroke:#0F2B6B,color:#FFFFFF,stroke-width:2px
