# Паттерн Singleton (Одиночка)

## Описание

Паттерн Singleton гарантирует, что класс имеет только один экземпляр, и предоставляет глобальную точку доступа к этому экземпляру. Это особенно полезно, когда нужно координировать действия по всей системе через один объект.

## Когда использовать

- Когда должен быть ровно один экземпляр класса, доступный всем клиентам
- Когда требуется строгий контроль над глобальными переменными
- Для управления разделяемыми ресурсами (пулы соединений с БД, кэши, менеджеры настроек)
- При необходимости централизованного управления состоянием или операциями

## Преимущества

- Гарантирует наличие только одного экземпляра класса
- Предоставляет глобальную точку доступа к экземпляру
- Объект создается только при первом запросе (ленивая инициализация)
- Контролируемый доступ к единственному экземпляру

## Недостатки

- Нарушает принцип единственной ответственности (класс решает две задачи)
- Может скрывать плохую архитектуру, когда используется как глобальная переменная
- Затрудняет модульное тестирование (сложно подменить синглтон моком)
- Проблемы с многопоточностью, если реализация не предусматривает это
- Усложняет наследование от класса-одиночки

## Реализация на Python

```python
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            # Инициализация происходит здесь
        return cls._instance
```

Более современная и потокобезопасная реализация с использованием декоратора:

```python
from functools import wraps

def singleton(cls):
    instances = {}
    
    @wraps(cls)
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    
    return get_instance

@singleton
class DatabaseConnection:
    def __init__(self, connection_string):
        # Здесь происходит "тяжелая" инициализация соединения
        self.connection_string = connection_string
        print(f"Initializing connection to {connection_string}")
    
    def query(self, sql):
        print(f"Executing: {sql}")
        # Выполнение запроса
```

## Где применяется в реальных проектах

- Подключения к базам данных
- Файловые системы и логгеры
- Пулы подключений или объектов
- Кэши
- Настройки приложения
- Диспетчеры устройств
- Сервисы регистрации (Registry)

## Связанные паттерны

- **Фабричный метод**: Может использоваться для создания Singleton
- **Абстрактная фабрика**: Реализации абстрактной фабрики часто реализуются как синглтоны
- **Фасад**: Объект фасада часто является синглтоном

## Рекомендации по использованию

- Старайтесь не злоупотреблять синглтонами - часто инъекция зависимостей является более гибким подходом
- Убедитесь, что ваша реализация потокобезопасна, если это требуется
- Рассмотрите альтернативы: сервис-локаторы, внедрение зависимостей или простые статические классы
- Синглтон часто критикуется как "анти-паттерн", поэтому используйте его осознанно
