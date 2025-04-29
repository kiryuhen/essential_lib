# Паттерн Memento (Снимок)

## Описание
Снимок — это поведенческий паттерн проектирования, который позволяет сохранять и восстанавливать прошлые состояния объектов, не раскрывая подробностей их реализации. Паттерн Снимок даёт возможность реализовать такие функции как "отмена/возврат" (undo/redo), историю состояний и т.п.

## Проблема
Необходимо сохранить внутреннее состояние объекта для возможности его восстановления позже, но при этом не нарушить принципов инкапсуляции. Также может потребоваться возможность отмены действий, возврата к предыдущим состояниям или навигации по истории состояний.

## Решение
Паттерн Снимок предлагает создать специальный объект — "снимок", который будет хранить копию состояния целевого объекта. Снимок имеет ограниченный интерфейс, который не позволяет изменять сохраненное состояние. Доступ к снимку и его содержимому имеют только объект, создавший снимок (Originator), и объект, управляющий историей снимков (Caretaker).

## Структура
- **Originator** — исходный объект, состояние которого нужно сохранять и восстанавливать
- **Memento** — объект снимка, хранящий состояние Originator
- **Caretaker** — объект-хранитель, управляющий историей снимков

## Пример на Python

```python
from __future__ import annotations
from typing import List, Any
import datetime

# Memento
class Memento:
    def __init__(self, state: str) -> None:
        self._state = state
        self._date = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    
    def get_state(self) -> str:
        return self._state
    
    def get_date(self) -> str:
        return self._date

# Originator
class Originator:
    def __init__(self, state: str) -> None:
        self._state = state
        print(f"Originator: Initial state is: {self._state}")
    
    def do_something(self, action: str) -> None:
        print(f"Originator: Doing {action}")
        self._state += f" + {action}"
        print(f"Originator: State changed to: {self._state}")
    
    def save(self) -> Memento:
        print("Originator: Saving to Memento.")
        return Memento(self._state)
    
    def restore(self, memento: Memento) -> None:
        self._state = memento.get_state()
        print(f"Originator: State restored to: {self._state}")

# Caretaker
class Caretaker:
    def __init__(self, originator: Originator) -> None:
        self._mementos: List[Memento] = []
        self._originator = originator
    
    def backup(self) -> None:
        print("Caretaker: Saving Originator's state...")
        self._mementos.append(self._originator.save())
    
    def undo(self) -> None:
        if not self._mementos:
            return
        
        memento = self._mementos.pop()
        print(f"Caretaker: Restoring state from: {memento.get_date()}")
        self._originator.restore(memento)
    
    def show_history(self) -> None:
        print("Caretaker: Here's the list of mementos:")
        for i, memento in enumerate(self._mementos):
            print(f"{i}: {memento.get_date()} / {memento.get_state()}")
```

## Применение
В реальных приложениях Снимок часто используется для:
- Реализации функций отмены/возврата (undo/redo)
- Сохранения и восстановления состояния игры
- Сохранения контрольных точек в процессе выполнения долгих вычислений
- Реализации транзакций (сохранение состояния перед выполнением операции)
- Создания снапшотов системы

## Плюсы
1. Позволяет сохранять состояние объекта без нарушения инкапсуляции
2. Упрощает структуру Originator, перенося хранение истории в Caretaker
3. Предоставляет удобный механизм для реализации отмены/возврата действий
4. Позволяет создавать снимки внутреннего состояния объекта и восстанавливать его
5. Обеспечивает безопасный доступ к состоянию объекта

## Минусы
1. Может потребовать значительного объема памяти для хранения большого количества снимков
2. Требует внимательного управления жизненным циклом снимков в Caretaker
3. Для динамических языков, таких как Python, требуется особое внимание к глубокому копированию объектов
4. Дополнительные затраты на создание и хранение снимков, даже если состояние не меняется
5. В некоторых случаях сложно определить, какую именно информацию следует включать в снимок

## Когда использовать
- Когда нужно сохранять и восстанавливать состояние объекта без нарушения инкапсуляции
- Когда необходима функциональность отмены/возврата (undo/redo)
- Когда требуется создавать контрольные точки в работе программы
- Когда непосредственный доступ к полям объекта нарушил бы его инкапсуляцию