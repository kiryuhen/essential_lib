# Паттерн Strategy (Стратегия)

## Описание
Паттерн Strategy — это поведенческий паттерн проектирования, который определяет семейство алгоритмов, инкапсулирует каждый из них и делает их взаимозаменяемыми. Стратегия позволяет изменять алгоритмы независимо от клиентов, которые их используют.

## Проблема
Часто возникает необходимость использовать разные алгоритмы или варианты поведения в различных ситуациях. Наивный подход с использованием условных операторов делает код сложным для поддержки и нарушает принцип единственной ответственности.

## Решение
Паттерн Strategy предлагает выделить все алгоритмы в отдельные классы, называемые стратегиями, с общим интерфейсом. Объект, который должен использовать разные алгоритмы, хранит ссылку на объект стратегии и делегирует ей выполнение действий, не зная о конкретной реализации.

## Структура
- **Context** — контекст, содержащий ссылку на объект стратегии
- **Strategy** — интерфейс, общий для всех поддерживаемых алгоритмов
- **ConcreteStrategy** — конкретные реализации алгоритмов

## Применение
В реальных приложениях Strategy часто используется для:
- Различных алгоритмов сортировки и поиска
- Различных стратегий валидации данных
- Алгоритмов сжатия и шифрования
- Различных методов оплаты в интернет-магазинах
- Разных алгоритмов маршрутизации в навигационных системах
- Различных форматов экспорта данных

## Плюсы
1. **Замена наследования композицией**: Вместо реализации множества подклассов с разным поведением, можно менять поведение основного класса через композицию.
2. **Изоляция реализации алгоритмов**: Детали алгоритмов скрыты от клиента.
3. **Устранение условных операторов**: Вместо switch/if-else для выбора алгоритма, клиент просто использует нужную стратегию.
4. **Легкость расширения**: Добавление новых стратегий не требует изменения клиентского кода.
5. **Выбор алгоритмов во время выполнения**: Можно менять стратегии динамически.

## Минусы
1. **Увеличение числа объектов**: Если стратегий много, может потребоваться создание большого количества классов.
2. **Накладные расходы на коммуникацию**: Стратегии могут требовать данные от контекста или возвращать данные контексту.
3. **Клиент должен знать о стратегиях**: Клиент должен понимать различия между стратегиями, чтобы выбрать подходящую.
4. **Усложнение кода при небольшом числе стратегий**: При малом количестве алгоритмов, простые условные операторы могут быть более понятными.
5. **Увеличение сложности системы**: При большом количестве стратегий может быть сложно управлять их взаимосвязями.

## Пример на Python

```python
from abc import ABC, abstractmethod

# Интерфейс стратегии
class SortStrategy(ABC):
    @abstractmethod
    def sort(self, dataset):
        pass

# Конкретные стратегии
class QuickSort(SortStrategy):
    def sort(self, dataset):
        print("Применение алгоритма быстрой сортировки")
        # Реализация быстрой сортировки
        return sorted(dataset)

class MergeSort(SortStrategy):
    def sort(self, dataset):
        print("Применение алгоритма сортировки слиянием")
        # Реализация сортировки слиянием
        return sorted(dataset)

# Контекст
class Sorter:
    def __init__(self, strategy=None):
        self._strategy = strategy
    
    def set_strategy(self, strategy):
        self._strategy = strategy
    
    def sort(self, dataset):
        if self._strategy is None:
            raise ValueError("Стратегия сортировки не установлена")
        return self._strategy.sort(dataset)
```

## Когда использовать
- Когда нужно использовать различные варианты алгоритма внутри объекта и выбирать нужный во время выполнения
- Когда есть много похожих классов, отличающихся только поведением
- Когда нужно скрыть от клиента сложные, специфичные для алгоритма данные
- Когда класс содержит множество условных операторов для выбора вариантов поведения
- Когда требуется возможность подмены алгоритмов в рантайме