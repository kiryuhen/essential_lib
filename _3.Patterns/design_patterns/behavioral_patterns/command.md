# Паттерн Command (Команда)

## Описание
Команда — это поведенческий паттерн проектирования, который превращает запрос или простую операцию в отдельный объект. Такая трансформация позволяет откладывать выполнение операций, выстраивать их в очереди, хранить историю, передавать их по сети и отменять операции.

## Проблема
Необходимо отделить объект, инициирующий операцию, от объекта, который знает, как её выполнить. Также может потребоваться реализация отложенного запуска операций, их постановка в очередь, отмена операций и ведение истории.

## Решение
Паттерн Команда предлагает инкапсулировать различные действия в объектах-командах, которые реализуют общий интерфейс. Вместо того чтобы непосредственно вызывать методы других объектов, клиент создаёт объект-команду и передаёт его в специальный объект-инициатор. Инициатор хранит команду и вызывает её в нужный момент.

## Структура
- **Command** — интерфейс команды с методом execute()
- **ConcreteCommand** — конкретная реализация команды
- **Invoker** — инициатор, который хранит команду и вызывает её метод execute()
- **Receiver** — получатель, который выполняет действия, связанные с обработкой запроса
- **Client** — создаёт и настраивает конкретные объекты команд

## Пример на Python

```python
from abc import ABC, abstractmethod

# Command
class Command(ABC):
    @abstractmethod
    def execute(self) -> None:
        pass
    
    @abstractmethod
    def undo(self) -> None:
        pass

# Receiver
class Light:
    def __init__(self, name: str) -> None:
        self.name = name
        self.is_on = False
    
    def turn_on(self) -> None:
        self.is_on = True
        print(f"{self.name} light is now ON")
    
    def turn_off(self) -> None:
        self.is_on = False
        print(f"{self.name} light is now OFF")

# ConcreteCommand
class LightOnCommand(Command):
    def __init__(self, light: Light) -> None:
        self._light = light
    
    def execute(self) -> None:
        self._light.turn_on()
    
    def undo(self) -> None:
        self._light.turn_off()

# ConcreteCommand
class LightOffCommand(Command):
    def __init__(self, light: Light) -> None:
        self._light = light
    
    def execute(self) -> None:
        self._light.turn_off()
    
    def undo(self) -> None:
        self._light.turn_on()

# Invoker
class RemoteControl:
    def __init__(self) -> None:
        self._commands = {}
        self._history = []
    
    def set_command(self, button_name: str, command: Command) -> None:
        self._commands[button_name] = command
    
    def press_button(self, button_name: str) -> None:
        if button_name in self._commands:
            self._commands[button_name].execute()
            self._history.append(self._commands[button_name])
        else:
            print(f"Button {button_name} not configured")
    
    def undo_last_action(self) -> None:
        if self._history:
            last_command = self._history.pop()
            last_command.undo()
```

## Применение
В реальных приложениях Команда часто используется для:
- Реализации отмены действий (undo/redo)
- Составления макросов (последовательность команд)
- Планирования задач
- Реализации транзакций
- Создания очереди задач
- Параллельной обработки запросов
- Организации GUI (связь кнопок с действиями)

## Плюсы
1. Отделяет объект, инициирующий операцию, от объекта, выполняющего её
2. Позволяет создавать последовательности команд (макросы)
3. Возможность отмены операций (undo/redo)
4. Поддерживает принцип открытости/закрытости (OCP)
5. Позволяет реализовать отложенный запуск операций
6. Позволяет сериализовать команды и передавать их по сети

## Минусы
1. Увеличивает количество классов, что может усложнить код
2. Необходимость создания отдельного класса для каждой операции
3. Объекты команд могут содержать излишние ссылки на другие объекты
4. Реализация отмены операций может быть сложной для некоторых команд

## Когда использовать
- Когда нужно параметризовать объекты выполняемым действием
- Когда требуется выполнять операции в определенной последовательности или очереди
- Когда нужна функциональность отмены операций
- Когда нужно реализовать отложенный запуск операций
- Когда необходимо хранить историю выполненных операций