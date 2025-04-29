# Архитектурный паттерн Repository (Репозиторий)

## Описание
Repository — это архитектурный паттерн, который изолирует доменную модель от деталей хранения данных, обеспечивая абстрактный доступ к данным. Репозиторий предоставляет коллекцию подобный интерфейс для доступа к сущностям домена.

## Проблема
Бизнес-логика приложения часто напрямую взаимодействует с механизмами доступа к данным (базами данных, файлами, внешними API), что создает тесные зависимости, затрудняет тестирование и вызывает проблемы при изменении источника данных или логики.

## Решение
Паттерн Repository создает промежуточный слой между бизнес-логикой и источником данных. Он скрывает детали доступа к данным, предоставляя абстрактный интерфейс для работы с коллекциями объектов домена.

## Структура
- **Repository Interface** — определяет методы для работы с коллекцией объектов (добавление, удаление, поиск)
- **Concrete Repository** — реализует интерфейс репозитория для конкретного источника данных
- **Entity** — объект домена, с которым работает репозиторий
- **Client** — использует репозиторий для доступа к объектам домена

## Пример на Python

```python
from abc import ABC, abstractmethod
from typing import List, Optional

# Entity
class User:
    def __init__(self, id: int, name: str, email: str):
        self.id = id
        self.name = name
        self.email = email

# Repository Interface
class UserRepository(ABC):
    @abstractmethod
    def get_all(self) -> List[User]:
        pass
    
    @abstractmethod
    def get_by_id(self, id: int) -> Optional[User]:
        pass
    
    @abstractmethod
    def create(self, user: User) -> User:
        pass
    
    @abstractmethod
    def update(self, user: User) -> User:
        pass
    
    @abstractmethod
    def delete(self, id: int) -> bool:
        pass

# Concrete Repository - SQLite implementation
class SQLiteUserRepository(UserRepository):
    def __init__(self, connection):
        self.connection = connection
        self._create_table()
    
    def _create_table(self):
        cursor = self.connection.cursor()
        cursor.execute('''
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY,
            name TEXT NOT NULL,
            email TEXT NOT NULL
        )
        ''')
        self.connection.commit()
    
    def get_all(self) -> List[User]:
        cursor = self.connection.cursor()
        cursor.execute('SELECT id, name, email FROM users')
        return [User(id, name, email) for id, name, email in cursor.fetchall()]
    
    def get_by_id(self, id: int) -> Optional[User]:
        cursor = self.connection.cursor()
        cursor.execute('SELECT id, name, email FROM users WHERE id = ?', (id,))
        row = cursor.fetchone()
        return User(row[0], row[1], row[2]) if row else None
    
    def create(self, user: User) -> User:
        cursor = self.connection.cursor()
        cursor.execute(
            'INSERT INTO users (name, email) VALUES (?, ?)',
            (user.name, user.email)
        )
        self.connection.commit()
        user.id = cursor.lastrowid
        return user
    
    def update(self, user: User) -> User:
        cursor = self.connection.cursor()
        cursor.execute(
            'UPDATE users SET name = ?, email = ? WHERE id = ?',
            (user.name, user.email, user.id)
        )
        self.connection.commit()
        return user
    
    def delete(self, id: int) -> bool:
        cursor = self.connection.cursor()
        cursor.execute('DELETE FROM users WHERE id = ?', (id,))
        self.connection.commit()
        return cursor.rowcount > 0

# Client code
class UserService:
    def __init__(self, user_repository: UserRepository):
        self.user_repository = user_repository
    
    def register_user(self, name: str, email: str) -> User:
        user = User(0, name, email)  # ID будет назначен репозиторием
        return self.user_repository.create(user)
    
    def get_all_users(self) -> List[User]:
        return self.user_repository.get_all()
```

## Применение
Репозиторий часто используется в:
- Корпоративных приложениях с доменной моделью
- Проектах с микросервисной архитектурой
- Приложениях, требующих гибкости в изменении источников данных
- Clean Architecture и Hexagonal Architecture
- Системах с большой доменной логикой
- Приложениях, где требуется тестирование в изоляции от реальных данных

## Плюсы
1. Изоляция домена от деталей хранения данных
2. Упрощение юнит-тестирования (можно использовать моки репозитория)
3. Централизация логики доступа к данным
4. Упрощение переключения между разными источниками данных
5. Улучшение читаемости кода за счет явного разделения ответственности

## Минусы
1. Дополнительный слой абстракции, который может быть избыточен для простых приложений
2. Необходимость создания и поддержки дополнительных интерфейсов
3. Потенциальное создание "репозиториев-анемиков" с минимальной функциональностью
4. Избыточность для простых CRUD-операций
5. Может привести к проблемам производительности при неправильной реализации

## Когда использовать
- Когда нужно изолировать доменную модель от деталей хранения данных
- При работе с несколькими источниками данных
- Когда требуется тестируемость кода без зависимости от реальных источников данных
- В проектах с Domain-Driven Design (DDD)
- Когда логика доступа к данным должна быть централизована и переиспользуема