# Паттерн Chain of Responsibility (Цепочка обязанностей)

## Описание
Цепочка обязанностей — это поведенческий паттерн проектирования, который позволяет передавать запросы последовательно по цепочке обработчиков. Каждый обработчик решает, может ли он обработать запрос сам, и если нет, то передаёт запрос дальше по цепочке.

## Проблема
Когда необходимо, чтобы разные объекты обрабатывали запрос в определенной последовательности, но нет уверенности заранее, какой именно объект должен обработать конкретный запрос.

## Решение
Паттерн Цепочка обязанностей предлагает связать объекты-обработчики в цепочку. Каждый обработчик имеет ссылку на следующий обработчик в цепочке. Когда поступает запрос, он передается по цепочке, пока не будет обработан соответствующим обработчиком.

## Структура
- **Handler** — интерфейс обработчика, определяющий метод для обработки запросов и ссылку на следующий обработчик
- **ConcreteHandler** — конкретная реализация обработчика, решающая, обрабатывать запрос или передать его дальше
- **Client** — инициатор запроса к первому обработчику в цепочке

## Пример на Python

```python
from abc import ABC, abstractmethod
from typing import Optional

# Handler
class Handler(ABC):
    def __init__(self, successor: Optional['Handler'] = None) -> None:
        self._successor = successor
    
    def set_successor(self, successor: 'Handler') -> None:
        self._successor = successor
    
    @abstractmethod
    def handle_request(self, request: int) -> Optional[str]:
        pass

# ConcreteHandler
class ConcreteHandler1(Handler):
    def handle_request(self, request: int) -> Optional[str]:
        if 0 <= request < 10:
            return f"Handler 1 handled request {request}"
        elif self._successor:
            return self._successor.handle_request(request)
        return None

# ConcreteHandler
class ConcreteHandler2(Handler):
    def handle_request(self, request: int) -> Optional[str]:
        if 10 <= request < 20:
            return f"Handler 2 handled request {request}"
        elif self._successor:
            return self._successor.handle_request(request)
        return None

# ConcreteHandler
class ConcreteHandler3(Handler):
    def handle_request(self, request: int) -> Optional[str]:
        if 20 <= request < 30:
            return f"Handler 3 handled request {request}"
        elif self._successor:
            return self._successor.handle_request(request)
        return None
```

## Применение
В реальных приложениях Цепочка обязанностей часто используется для:
- Обработки HTTP запросов через middleware
- Системы логирования с разными уровнями логов
- Обработки событий в GUI
- Механизмов авторизации и аутентификации
- Фильтрации данных
- Обработки исключений

## Плюсы
1. Уменьшает связанность между отправителем запроса и его получателями
2. Реализует принцип единственной ответственности (SRP)
3. Реализует принцип открытости/закрытости (OCP)
4. Позволяет динамически изменять цепочку обработчиков
5. Каждый обработчик может быть реализован как отдельный, независимый модуль

## Минусы
1. Запрос может остаться необработанным, если цепочка не настроена должным образом
2. Сложно отлаживать, если цепочка длинная
3. Рекурсивные вызовы могут привести к проблемам с производительностью
4. Может быть сложно отследить, какой обработчик обработал запрос

## Когда использовать
- Когда несколько объектов могут обрабатывать запрос, но обработчик заранее неизвестен
- Когда необходимо передать запрос одному из нескольких объектов, не указывая явно получателя
- Когда набор объектов, способных обработать запрос, должен определяться динамически
- Когда порядок обработки важен
- Когда нужно разделить обработку запроса на несколько этапов