# Архитектурный паттерн Hexagonal Architecture (Гексагональная архитектура)

## Описание
Гексагональная архитектура (также известная как Ports and Adapters) — это архитектурный паттерн, который изолирует бизнес-логику от внешних интерфейсов и технических деталей. Главная идея заключается в том, чтобы приложение могло одинаково управляться пользователями, программами, автоматизированными тестами или пакетными скриптами, а также оставаться независимым от баз данных, UI-фреймворков и других внешних элементов.

## Проблема
Традиционные архитектуры создают тесные связи между бизнес-логикой и внешними компонентами, что затрудняет изменение или замену этих компонентов без влияния на основную логику. Это усложняет тестирование, эволюцию и поддержку системы.

## Решение
Гексагональная архитектура размещает бизнес-логику в центре приложения и определяет порты (интерфейсы) для взаимодействия с внешними системами. Адаптеры, реализующие эти порты, обеспечивают связь между бизнес-логикой и конкретными технологиями.

## Структура
- **Domain (Домен)** — ядро приложения, содержащее бизнес-логику и бизнес-объекты
- **Ports (Порты)** — интерфейсы, определяющие способы взаимодействия с ядром
- **Primary/Driving Adapters (Первичные адаптеры)** — адаптеры, инициирующие взаимодействие с ядром (UI, REST API, CLI)
- **Secondary/Driven Adapters (Вторичные адаптеры)** — адаптеры, с которыми взаимодействует ядро (базы данных, внешние сервисы)

## Пример на Python

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import List, Optional

# Domain (Core)
@dataclass
class Task:
    id: int
    title: str
    description: str
    completed: bool = False

# Port (Output/Secondary Port)
class TaskRepository(ABC):
    @abstractmethod
    def get_all(self) -> List[Task]:
        pass
    
    @abstractmethod
    def get_by_id(self, task_id: int) -> Optional[Task]:
        pass
    
    @abstractmethod
    def save(self, task: Task) -> Task:
        pass
    
    @abstractmethod
    def delete(self, task_id: int) -> bool:
        pass

# Use Case / Application Service (Core functionality)
class TaskService:
    def __init__(self, task_repository: TaskRepository):
        self.task_repository = task_repository
    
    def get_all_tasks(self) -> List[Task]:
        return self.task_repository.get_all()
    
    def create_task(self, title: str, description: str) -> Task:
        # Бизнес-правила проверки задачи могли бы быть здесь
        if not title:
            raise ValueError("Task title cannot be empty")
            
        last_id = max([t.id for t in self.task_repository.get_all()], default=0)
        task = Task(id=last_id + 1, title=title, description=description)
        return self.task_repository.save(task)
    
    def complete_task(self, task_id: int) -> Task:
        task = self.task_repository.get_by_id(task_id)
        if not task:
            raise ValueError(f"Task with ID {task_id} not found")
        
        task.completed = True
        return self.task_repository.save(task)

# Secondary/Driven Adapter (Implementation of Output Port)
class InMemoryTaskRepository(TaskRepository):
    def __init__(self):
        self.tasks = {}
    
    def get_all(self) -> List[Task]:
        return list(self.tasks.values())
    
    def get_by_id(self, task_id: int) -> Optional[Task]:
        return self.tasks.get(task_id)
    
    def save(self, task: Task) -> Task:
        self.tasks[task.id] = task
        return task
    
    def delete(self, task_id: int) -> bool:
        if task_id in self.tasks:
            del self.tasks[task_id]
            return True
        return False

# Port (Input/Primary Port) - часто неявный в Python из-за duck typing
class TaskUseCase:
    def get_all_tasks(self) -> List[Task]:
        pass
    
    def create_task(self, title: str, description: str) -> Task:
        pass
    
    def complete_task(self, task_id: int) -> Task:
        pass

# Primary/Driving Adapter (CLI)
class TaskCLI:
    def __init__(self, task_service: TaskService):
        self.task_service = task_service
    
    def run(self):
        while True:
            print("\n1. List all tasks")
            print("2. Create new task")
            print("3. Complete task")
            print("4. Exit")
            choice = input("Enter your choice: ")
            
            if choice == "1":
                tasks = self.task_service.get_all_tasks()
                if not tasks:
                    print("No tasks found.")
                else:
                    for task in tasks:
                        status = "Completed" if task.completed else "Pending"
                        print(f"{task.id}. {task.title} - {status}")
            
            elif choice == "2":
                title = input("Enter task title: ")
                description = input("Enter task description: ")
                try:
                    task = self.task_service.create_task(title, description)
                    print(f"Task created with ID: {task.id}")
                except ValueError as e:
                    print(f"Error: {str(e)}")
            
            elif choice == "3":
                try:
                    task_id = int(input("Enter task ID to complete: "))
                    task = self.task_service.complete_task(task_id)
                    print(f"Task '{task.title}' marked as completed.")
                except ValueError as e:
                    print(f"Error: {str(e)}")
            
            elif choice == "4":
                break
            
            else:
                print("Invalid choice, try again.")

# Primary/Driving Adapter (REST API using Flask)
from flask import Flask, jsonify, request

class FlaskTaskAPI:
    def __init__(self, task_service: TaskService):
        self.task_service = task_service
        self.app = Flask(__name__)
        self.setup_routes()
    
    def setup_routes(self):
        @self.app.route('/tasks', methods=['GET'])
        def get_tasks():
            tasks = self.task_service.get_all_tasks()
            return jsonify([self._task_to_dict(task) for task in tasks])
        
        @self.app.route('/tasks', methods=['POST'])
        def create_task():
            data = request.json
            try:
                task = self.task_service.create_task(
                    data.get('title', ''), data.get('description', '')
                )
                return jsonify(self._task_to_dict(task)), 201
            except ValueError as e:
                return jsonify({"error": str(e)}), 400
        
        @self.app.route('/tasks/<int:task_id>/complete', methods=['POST'])
        def complete_task(task_id):
            try:
                task = self.task_service.complete_task(task_id)
                return jsonify(self._task_to_dict(task))
            except ValueError as e:
                return jsonify({"error": str(e)}), 404
    
    def _task_to_dict(self, task):
        return {
            "id": task.id,
            "title": task.title,
            "description": task.description,
            "completed": task.completed
        }
    
    def run(self, host='0.0.0.0', port=5000):
        self.app.run(host=host, port=port)
```

## Применение
Гексагональная архитектура используется в:
- Приложениях с комплексной бизнес-логикой
- Системах, требующих гибкости при замене технологий
- Приложениях с множественными интерфейсами (API, UI, CLI)
- При использовании Domain-Driven Design (DDD)
- Когда важно тестирование бизнес-логики в изоляции от внешних зависимостей
- Приложениях с длительным жизненным циклом и переходом между технологиями

## Плюсы
1. Изоляция бизнес-логики от внешних зависимостей
2. Легкость тестирования бизнес-логики с помощью заглушек (stubs) и мокапов
3. Независимость от конкретных технологий и фреймворков
4. Возможность разработки и тестирования бизнес-логики без UI и инфраструктуры
5. Легкая замена адаптеров без влияния на ядро приложения
6. Четкое разделение ответственности между компонентами

## Минусы
1. Повышенная сложность кода, особенно для небольших приложений
2. Дополнительный код для портов и адаптеров
3. Более сложная первоначальная настройка проекта
4. Более высокая кривая обучения для новых разработчиков
5. Потенциальное дублирование моделей данных (доменные модели и DTO)

## Когда использовать
- В проектах со сложной бизнес-логикой
- Когда приложение должно поддерживать множество входных и выходных интерфейсов
- Когда требуется высокая тестируемость бизнес-логики
- В долгосрочных проектах, где технологии могут меняться со временем
- При использовании методологии Domain-Driven Design (DDD)