# Паттерн Mediator (Посредник)

## Описание
Посредник — это поведенческий паттерн проектирования, который позволяет уменьшить связанность между компонентами системы путем перемещения всех связей между ними в единый объект-посредник. Компоненты больше не ссылаются друг на друга напрямую, а взаимодействуют через посредника.

## Проблема
При разработке сложных систем возникает проблема тесной связи между объектами, которые должны взаимодействовать между собой. Каждый объект должен знать о внутреннем устройстве других объектов, что приводит к сложной, запутанной и трудноподдерживаемой системе.

## Решение
Паттерн Посредник предлагает создать объект-посредник, который будет управлять и координировать взаимодействие между компонентами. Вместо прямого взаимодействия объекты сообщают посреднику о своих событиях, а посредник передает эти сообщения всем заинтересованным объектам.

## Структура
- **Mediator** — интерфейс посредника, который определяет методы для взаимодействия с компонентами
- **ConcreteMediator** — конкретная реализация посредника, координирующая работу компонентов
- **Component** — интерфейс компонентов, определяющий способ взаимодействия с посредником
- **ConcreteComponent** — конкретная реализация компонента, взаимодействующая с посредником

## Пример на Python

```python
from abc import ABC, abstractmethod
from typing import List

# Mediator
class Mediator(ABC):
    @abstractmethod
    def notify(self, sender: object, event: str) -> None:
        pass

# ConcreteMediator
class ConcreteMediator(Mediator):
    def __init__(self, component1, component2):
        self._component1 = component1
        self._component1.mediator = self
        self._component2 = component2
        self._component2.mediator = self
    
    def notify(self, sender: object, event: str) -> None:
        if event == "A":
            print("Mediator reacts on A and triggers following operations:")
            self._component2.do_c()
        elif event == "D":
            print("Mediator reacts on D and triggers following operations:")
            self._component1.do_b()
            self._component2.do_c()

# Component
class BaseComponent:
    def __init__(self, mediator: Mediator = None):
        self._mediator = mediator
    
    @property
    def mediator(self) -> Mediator:
        return self._mediator
    
    @mediator.setter
    def mediator(self, mediator: Mediator) -> None:
        self._mediator = mediator

# ConcreteComponent
class Component1(BaseComponent):
    def do_a(self) -> None:
        print("Component 1 does A.")
        self.mediator.notify(self, "A")
    
    def do_b(self) -> None:
        print("Component 1 does B.")

# ConcreteComponent
class Component2(BaseComponent):
    def do_c(self) -> None:
        print("Component 2 does C.")
    
    def do_d(self) -> None:
        print("Component 2 does D.")
        self.mediator.notify(self, "D")
```

## Применение
В реальных приложениях Посредник часто используется для:
- Реализации пользовательского интерфейса (связь между элементами UI)
- Централизованного управления связями между множеством объектов
- Реализации систем обмена сообщениями
- Упрощения коммуникации между микросервисами
- Системы событийного программирования

## Плюсы
1. Уменьшает связанность между компонентами системы
2. Централизует связи между объектами в одном месте
3. Упрощает повторное использование компонентов
4. Упрощает понимание взаимодействия между объектами
5. Реализует принцип единственной ответственности (SRP)
6. Упрощает добавление новых компонентов

## Минусы
1. Посредник может стать "божественным объектом" (God Object)
2. Может привести к излишней централизации и усложнению кода посредника
3. Усложняет отладку при большом количестве компонентов
4. Требует дополнительного объекта-посредника

## Когда использовать
- Когда сложные связи между множеством компонентов делают систему трудной для понимания и поддержки
- Когда необходимо повторно использовать компоненты, но это затруднено из-за их сильной связанности
- Когда объекты взаимодействуют сложным образом и эти взаимодействия не определены явно
- Когда изменение одного компонента влияет на множество других компонентов
- Когда нужно снизить связанность между компонентами системы