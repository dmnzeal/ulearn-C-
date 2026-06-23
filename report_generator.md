## 1. Описание предметной области и сущностей

Система строит отчёты по измерениям (температура, влажность) с гибкой сменой формата (HTML/Markdown) и способа расчёта статистик (среднее со стандартным отклонением или медиана).
Компоненты: интерфейс форматтера (IOutputFormatter) и калькулятора (IStatCalculator<TResult>), с конкретными реализациями для каждого формата и статистики.
ReportBuilder<TStat> собирает отчёт, последовательно вызывая методы форматтера и калькулятора для каждого набора данных.
Статический класс ReportMakerHelper предоставляет готовые методы для всех комбинаций форматов и статистик.
Архитектура позволяет легко добавлять новые форматы или статистики без изменения существующего кода.

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
