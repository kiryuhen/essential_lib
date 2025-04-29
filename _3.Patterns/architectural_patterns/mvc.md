# Архитектурный паттерн MVC (Model-View-Controller)

## Описание
MVC — это архитектурный паттерн, который разделяет приложение на три основных компонента: модель (данные), представление (интерфейс) и контроллер (логика взаимодействия). Это позволяет обеспечить разделение ответственности и сделать код более модульным и легко поддерживаемым.

## Проблема
При разработке сложных приложений бизнес-логика, управление данными и пользовательский интерфейс оказываются тесно связанными, что затрудняет внесение изменений, тестирование и повторное использование отдельных компонентов.

## Решение
Паттерн MVC предлагает разделить приложение на три взаимосвязанных компонента, каждый из которых отвечает за определенный аспект функциональности:

## Структура
- **Model (Модель)** — отвечает за данные и бизнес-логику приложения. Не зависит от представления и контроллера.
- **View (Представление)** — отвечает за отображение данных пользователю. Получает данные от модели и отображает их.
- **Controller (Контроллер)** — обрабатывает ввод пользователя, изменяет модель и обновляет представление.

## Пример на Python

```python
# Model
class TodoModel:
    def __init__(self):
        self.tasks = []
        self.observers = []
    
    def add_task(self, task):
        self.tasks.append(task)
        self.notify_observers()
    
    def remove_task(self, index):
        if 0 <= index < len(self.tasks):
            del self.tasks[index]
            self.notify_observers()
    
    def get_tasks(self):
        return self.tasks
    
    def add_observer(self, observer):
        self.observers.append(observer)
    
    def notify_observers(self):
        for observer in self.observers:
            observer.model_updated()

# View
class TodoView:
    def display_tasks(self, tasks):
        print("=== TODO LIST ===")
        for i, task in enumerate(tasks):
            print(f"{i+1}. {task}")
        print("=================")
    
    def get_user_input(self):
        return input("Enter command (add/remove/exit): ")
    
    def get_task_input(self):
        return input("Enter task: ")
    
    def get_task_index(self):
        return int(input("Enter task number: ")) - 1
    
    def model_updated(self):
        self.display_tasks(self.controller.get_tasks())
    
    def set_controller(self, controller):
        self.controller = controller

# Controller
class TodoController:
    def __init__(self, model, view):
        self.model = model
        self.view = view
        self.view.set_controller(self)
        self.model.add_observer(self.view)
    
    def start(self):
        while True:
            self.view.display_tasks(self.model.get_tasks())
            command = self.view.get_user_input()
            
            if command == "add":
                task = self.view.get_task_input()
                self.model.add_task(task)
            elif command == "remove":
                index = self.view.get_task_index()
                self.model.remove_task(index)
            elif command == "exit":
                break
    
    def get_tasks(self):
        return self.model.get_tasks()
```

## Применение
MVC широко используется в:
- Веб-фреймворках (Django, Flask с шаблонизаторами)
- Десктопных приложениях с интерфейсом (PyQt, Tkinter)
- Мобильной разработке
- Корпоративных приложениях с развитым UI
- Прикладных решениях, где требуется разделение данных, логики и представления

## Плюсы
1. Четкое разделение ответственности между компонентами
2. Модульность и повторное использование кода
3. Параллельная разработка (разные команды могут работать над разными компонентами)
4. Упрощение сопровождения и тестирования
5. Более высокая гибкость при внесении изменений

## Минусы
1. Избыточность для простых приложений
2. Увеличение сложности кода
3. Необходимость поддерживать согласованность между компонентами
4. Потенциальное снижение производительности из-за дополнительного уровня абстракции
5. Более сложный процесс обучения для новых разработчиков

## Когда использовать
- Для сложных приложений с богатым пользовательским интерфейсом
- Когда нужно обеспечить разделение ответственности между компонентами
- Когда несколько разработчиков работают над одним проектом
- Когда требуется переиспользование кода и компонентов
- Когда важна гибкость и масштабируемость приложения