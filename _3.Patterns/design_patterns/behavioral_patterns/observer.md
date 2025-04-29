# Паттерн Observer (Наблюдатель)

## Описание
Наблюдатель — это поведенческий паттерн проектирования, который создаёт механизм подписки, позволяющий одним объектам следить и реагировать на события, происходящие в других объектах. Это один из наиболее используемых паттернов для реализации распределённых систем событий.

## Проблема
Необходимо установить зависимость "один ко многим" между объектами таким образом, чтобы при изменении состояния одного объекта (субъекта) все зависимые от него объекты (наблюдатели) автоматически уведомлялись и обновлялись.

## Решение
Паттерн Наблюдатель предлагает разделить объекты на две роли: субъект (Subject) и наблюдатель (Observer). Субъект содержит состояние и уведомляет своих наблюдателей о его изменении. Наблюдатели могут подписываться на эти уведомления и реагировать на них.

## Структура
- **Subject** — интерфейс субъекта, определяющий методы для работы с наблюдателями
- **ConcreteSubject** — конкретная реализация субъекта, содержащая состояние и уведомляющая наблюдателей
- **Observer** — интерфейс наблюдателя, определяющий метод обновления
- **ConcreteObserver** — конкретная реализация наблюдателя, реагирующая на изменения состояния субъекта

## Пример на Python

```python
from abc import ABC, abstractmethod
from typing import List

# Observer
class Observer(ABC):
    @abstractmethod
    def update(self, subject) -> None:
        pass

# Subject
class Subject(ABC):
    @abstractmethod
    def attach(self, observer: Observer) -> None:
        pass
    
    @abstractmethod
    def detach(self, observer: Observer) -> None:
        pass
    
    @abstractmethod
    def notify(self) -> None:
        pass

# ConcreteSubject
class ConcreteSubject(Subject):
    def __init__(self):
        self._observers: List[Observer] = []
        self._state = 0
    
    def attach(self, observer: Observer) -> None:
        print("Subject: Attached an observer.")
        self._observers.append(observer)
    
    def detach(self, observer: Observer) -> None:
        self._observers.remove(observer)
    
    def notify(self) -> None:
        print("Subject: Notifying observers...")
        for observer in self._observers:
            observer.update(self)
    
    @property
    def state(self) -> int:
        return self._state
    
    def set_state(self, state: int) -> None:
        print(f"Subject: Setting state to {state}")
        self._state = state
        self.notify()

# ConcreteObserver
class ConcreteObserverA(Observer):
    def update(self, subject: ConcreteSubject) -> None:
        if subject.state < 3:
            print("ConcreteObserverA: Reacted to the event")

# ConcreteObserver
class ConcreteObserverB(Observer):
    def update(self, subject: ConcreteSubject) -> None:
        if subject.state >= 2:
            print("ConcreteObserverB: Reacted to the event")
```

## Применение
В реальных приложениях Наблюдатель часто используется для:
- Реализации событийно-ориентированных систем (GUI, веб-приложения)
- Организации связи между различными модулями приложения
- Системы уведомлений и оповещений
- Реализации архитектуры MVC (Model-View-Controller)
- Обновления представлений при изменении модели данных
- Реактивного программирования (RxPy и другие библиотеки)

## Плюсы
1. Слабая связанность между субъектом и наблюдателями
2. Поддержки принципа открытости/закрытости (OCP)
3. Возможность установки и удаления связей между объектами во время выполнения
4. Один субъект может уведомлять множество наблюдателей
5. Обеспечивает обмен данными между объектами, сохраняя их независимость друг от друга

## Минусы
1. Наблюдатели уведомляются в случайном порядке
2. Потенциальные утечки памяти из-за неявных ссылок между субъектом и наблюдателями
3. Непредвиденные обновления наблюдателей при сложной цепочке зависимостей
4. Дополнительные накладные расходы на рассылку уведомлений
5. Может вызвать лавину обновлений при сложной структуре наблюдателей

## Когда использовать
- Когда изменение состояния одного объекта требует изменения других объектов
- Когда объекты должны наблюдать за другими объектами только временно
- Когда один объект должен уведомлять неизвестное количество других объектов
- Когда система должна быть расширяемой: новые классы-наблюдатели должны легко добавляться
- Когда существует необходимость в слабой связи между компонентами