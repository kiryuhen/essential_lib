# Архитектурный паттерн Layered Architecture (Многослойная архитектура)

## Описание
Многослойная архитектура — это архитектурный паттерн, который разделяет приложение на несколько горизонтальных слоев, каждый из которых выполняет определенную роль. Каждый слой предоставляет сервисы слою выше и использует сервисы слоя ниже.

## Проблема
Разработка сложных приложений без четкого разделения функциональности приводит к спагетти-коду, где различные аспекты (представление, бизнес-логика, доступ к данным) переплетаются, что усложняет сопровождение, тестирование и масштабирование приложения.

## Решение
Многослойная архитектура разделяет приложение на отдельные логические слои с четкими обязанностями. Обычно взаимодействие между слоями строго регламентировано — верхние слои могут обращаться только к непосредственно нижележащим слоям, а не ко всем сразу.

## Структура
Классическая многослойная архитектура обычно включает следующие слои:
- **Presentation Layer (Слой представления)** — пользовательский интерфейс и взаимодействие с пользователем
- **Application Layer (Слой приложения)** — координирует действия приложения, не содержит бизнес-логики
- **Business Layer (Слой бизнес-логики)** — реализует основную функциональность приложения и бизнес-правила
- **Data Access Layer (Слой доступа к данным)** — взаимодействует с базами данных и другими хранилищами
- **Infrastructure Layer (Инфраструктурный слой)** — предоставляет техническую функциональность (логирование, кэширование и т.д.)

## Пример на Python

```python
# Инфраструктурный слой
class Logger:
    @staticmethod
    def log(message):
        print(f"LOG: {message}")

# Слой доступа к данным
class UserRepository:
    def __init__(self, connection_string):
        self.connection_string = connection_string
    
    def get_user(self, user_id):
        # Здесь был бы код для получения пользователя из БД
        Logger.log(f"Getting user with id {user_id}")
        return {"id": user_id, "name": "John Doe", "email": "john@example.com"}
    
    def save_user(self, user):
        # Здесь был бы код для сохранения пользователя в БД
        Logger.log(f"Saving user {user['name']}")
        return True

# Слой бизнес-логики
class UserService:
    def __init__(self, user_repository):
        self.user_repository = user_repository
    
    def get_user(self, user_id):
        return self.user_repository.get_user(user_id)
    
    def update_user_email(self, user_id, new_email):
        user = self.user_repository.get_user(user_id)
        
        # Проверка бизнес-правил
        if not "@" in new_email:
            raise ValueError("Invalid email format")
        
        user["email"] = new_email
        self.user_repository.save_user(user)
        return user

# Слой приложения
class UserController:
    def __init__(self, user_service):
        self.user_service = user_service
    
    def get_user_profile(self, user_id):
        try:
            user = self.user_service.get_user(user_id)
            return {"success": True, "data": user}
        except Exception as e:
            Logger.log(f"Error getting user profile: {str(e)}")
            return {"success": False, "error": str(e)}
    
    def update_email(self, user_id, new_email):
        try:
            user = self.user_service.update_user_email(user_id, new_email)
            return {"success": True, "data": user}
        except ValueError as e:
            Logger.log(f"Validation error: {str(e)}")
            return {"success": False, "error": str(e)}
        except Exception as e:
            Logger.log(f"System error: {str(e)}")
            return {"success": False, "error": "System error occurred"}

# Слой представления
class UserView:
    def __init__(self, controller):
        self.controller = controller
    
    def show_user_profile(self, user_id):
        result = self.controller.get_user_profile(user_id)
        if result["success"]:
            user = result["data"]
            print(f"User Profile:")
            print(f"ID: {user['id']}")
            print(f"Name: {user['name']}")
            print(f"Email: {user['email']}")
        else:
            print(f"Error: {result['error']}")
    
    def change_email(self, user_id):
        new_email = input("Enter new email: ")
        result = self.controller.update_email(user_id, new_email)
        if result["success"]:
            print("Email updated successfully!")
        else:
            print(f"Failed to update email: {result['error']}")

# Пример использования
def main():
    # Создание и связывание слоев
    repo = UserRepository("db_connection_string")
    service = UserService(repo)
    controller = UserController(service)
    view = UserView(controller)
    
    # Использование приложения
    view.show_user_profile(1)
    view.change_email(1)
```

## Применение
Многослойная архитектура используется в:
- Корпоративных приложениях
- Веб-приложениях (Django, Flask с расширениями)
- Десктопных приложениях
- Микросервисных архитектурах (внутри отдельных сервисов)
- Банковских и финансовых системах
- ERP и CRM системах

## Плюсы
1. Четкое разделение ответственности
2. Возможность повторного использования кода (особенно нижних слоев)
3. Упрощение тестирования (каждый слой можно тестировать отдельно)
4. Возможность независимой разработки и замены слоев
5. Упрощает понимание системы и навигацию по коду
6. Возможность распределения слоев по физическим серверам

## Минусы
1. Может быть избыточной для простых приложений
2. Увеличение накладных расходов из-за передачи данных между слоями
3. Риск превращения в "слоеный торт" с большим количеством лишних абстракций
4. Сложность определения правильного числа слоев и их ответственности
5. Потенциальное снижение производительности при неправильном проектировании

## Когда использовать
- В сложных корпоративных приложениях
- Когда команда разработчиков большая и каждый может работать над своим слоем
- Когда требуется четкое разделение ответственности
- В системах с высокими требованиями к сопровождаемости кода
- Когда необходима возможность независимого масштабирования компонентов системы