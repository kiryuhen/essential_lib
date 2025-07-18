# Паттерн Bridge (Мост)

## Описание

Мост — это структурный паттерн проектирования, который разделяет абстракцию от реализации так, чтобы они могли изменяться независимо друг от друга. Он заключается в выделении отдельных иерархий для абстракций и их реализаций, а затем установлении связи между ними через композицию.

## Когда использовать

- Когда нужно избежать постоянной привязки абстракции к реализации
- Когда как абстракции, так и их реализации должны расширяться путем создания подклассов
- Когда изменения в реализации не должны влиять на клиентский код
- Когда имеется множество вариаций реализации и нужно избежать экспоненциального роста числа классов
- Когда хотите скрыть детали реализации от клиентов

## Преимущества

- Отделяет абстракцию от реализации
- Повышает удобство сопровождения кода путем его организации в более чистые иерархии
- Скрывает детали реализации от клиентского кода
- Позволяет независимо изменять абстракцию и реализацию
- Реализует принцип открытости/закрытости: можно вводить новые абстракции и реализации, не изменяя существующий код

## Недостатки

- Усложняет код из-за введения дополнительных классов и уровней абстракции
- Может быть избыточным для простых классов, где не требуется много вариаций
- Требует продуманного начального проектирования для правильного разделения ответственности

## Реализация на Python

```python
from abc import ABC, abstractmethod

# Абстракция реализации
class DrawingAPI(ABC):
    @abstractmethod
    def draw_circle(self, x, y, radius):
        pass

# Конкретные реализации
class SVGDrawingAPI(DrawingAPI):
    def draw_circle(self, x, y, radius):
        print(f"SVG: рисуем круг в ({x}, {y}) с радиусом {radius}")

class CanvasDrawingAPI(DrawingAPI):
    def draw_circle(self, x, y, radius):
        print(f"Canvas: рисуем круг в ({x}, {y}) с радиусом {radius}")

# Абстракция
class Shape(ABC):
    def __init__(self, drawing_api):
        self.drawing_api = drawing_api
    
    @abstractmethod
    def draw(self):
        pass
    
    @abstractmethod
    def resize(self, factor):
        pass

# Уточненная абстракция
class Circle(Shape):
    def __init__(self, x, y, radius, drawing_api):
        super().__init__(drawing_api)
        self.x = x
        self.y = y
        self.radius = radius
    
    def draw(self):
        self.drawing_api.draw_circle(self.x, self.y, self.radius)
    
    def resize(self, factor):
        self.radius *= factor

# Использование
svg_api = SVGDrawingAPI()
circle1 = Circle(1, 2, 3, svg_api)
circle1.draw()

canvas_api = CanvasDrawingAPI()
circle2 = Circle(5, 7, 11, canvas_api)
circle2.draw()

# Изменение состояния и реализации независимо
circle1.resize(2)
circle1.draw()
```

## Где применяется в реальных проектах

- Графические системы, где отделяют абстракцию фигур от механизмов их отрисовки
- Мультиплатформенные приложения, где разделяются абстракции функциональности и платформенные реализации
- Драйверы устройств, где интерфейс драйвера отделен от его реализации для конкретного устройства
- Фреймворки пользовательского интерфейса
- Системы логирования с разными бэкендами
- Различные ORM-системы
- Клиенты баз данных, работающие с разными СУБД

## Связанные паттерны

- **Адаптер**: Адаптирует существующий интерфейс к другому, а мост разделяет интерфейс от реализации
- **Стратегия**: Мост имеет структуру, похожую на стратегию, но с другим назначением
- **Абстрактная фабрика**: Может использоваться для создания связок абстракций и реализаций
- **Компоновщик**: Может использоваться с мостом для создания сложных структур с разными реализациями

## Рекомендации по использованию

- Определите иерархию абстракций и реализаций до применения паттерна
- Подумайте, действительно ли нужно независимое изменение абстракций и реализаций
- Отдавайте предпочтение композиции над наследованием, когда это возможно
- При реализации применяйте принцип инверсии зависимостей: абстракции не должны зависеть от деталей
- Используйте паттерн на ранних этапах проектирования, когда видите потенциально разрастающиеся иерархии классов
- Рассмотрите возможность использования паттерна "Фабрика" для создания подходящих пар абстракций и реализаций
