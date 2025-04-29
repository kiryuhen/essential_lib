# Тестирование: модульное, интеграционное, E2E

Тестирование программного обеспечения — это процесс оценки и проверки того, что программа или приложение делает то, для чего оно было разработано, и выполняет свои требования корректно. Различные уровни тестирования позволяют охватить разные аспекты системы, от отдельных компонентов до целостной работы приложения.

## Основные уровни тестирования

### 1. Модульное тестирование (Unit Testing)

Модульное тестирование фокусируется на проверке отдельных компонентов или "модулей" программного обеспечения. Целью является убедиться, что каждый компонент работает корректно в изоляции.

#### Особенности модульного тестирования

- Тестирует наименьшие тестируемые части приложения (функции, методы, классы)
- Изолирует модуль от внешних зависимостей с помощью заглушек (mocks, stubs)
- Быстрое выполнение и простота в отладке
- Обычно создается и поддерживается разработчиками
- Автоматизируется с помощью фреймворков для модульного тестирования

#### Пример модульного теста на Python с использованием pytest

```python
# файл: calculator.py
class Calculator:
    def add(self, a, b):
        return a + b
    
    def subtract(self, a, b):
        return a - b
    
    def multiply(self, a, b):
        return a * b
    
    def divide(self, a, b):
        if b == 0:
            raise ValueError("Cannot divide by zero")
        return a / b
```

```python
# файл: test_calculator.py
import pytest
from calculator import Calculator

class TestCalculator:
    def setup_method(self):
        self.calc = Calculator()
    
    def test_add(self):
        assert self.calc.add(1, 2) == 3
        assert self.calc.add(-1, 1) == 0
        assert self.calc.add(-1, -1) == -2
    
    def test_subtract(self):
        assert self.calc.subtract(5, 2) == 3
        assert self.calc.subtract(2, 5) == -3
        assert self.calc.subtract(0, 0) == 0
    
    def test_multiply(self):
        assert self.calc.multiply(3, 4) == 12
        assert self.calc.multiply(-3, 4) == -12
        assert self.calc.multiply(0, 5) == 0
    
    def test_divide(self):
        assert self.calc.divide(10, 2) == 5
        assert self.calc.divide(8, 4) == 2
        assert self.calc.divide(0, 5) == 0
    
    def test_divide_by_zero(self):
        with pytest.raises(ValueError):
            self.calc.divide(10, 0)
```

#### Использование моков для изоляции модулей

Моки (mocks) и заглушки (stubs) используются для изоляции тестируемого кода от его зависимостей.

```python
# файл: user_service.py
import requests

class UserService:
    def __init__(self, api_url):
        self.api_url = api_url
    
    def get_user(self, user_id):
        response = requests.get(f"{self.api_url}/users/{user_id}")
        if response.status_code == 200:
            return response.json()
        return None
    
    def get_user_name(self, user_id):
        user = self.get_user(user_id)
        if user:
            return user.get('name')
        return "Unknown User"
```

```python
# файл: test_user_service.py
import pytest
from unittest.mock import patch, MagicMock
from user_service import UserService

class TestUserService:
    def setup_method(self):
        self.user_service = UserService('https://api.example.com')
    
    @patch('user_service.requests.get')
    def test_get_user_successful(self, mock_get):
        # Настройка мока
        mock_response = MagicMock()
        mock_response.status_code = 200
        mock_response.json.return_value = {'id': 1, 'name': 'John Doe'}
        mock_get.return_value = mock_response
        
        # Вызов тестируемого метода
        user = self.user_service.get_user(1)
        
        # Проверки
        mock_get.assert_called_once_with('https://api.example.com/users/1')
        assert user['name'] == 'John Doe'
    
    @patch('user_service.requests.get')
    def test_get_user_not_found(self, mock_get):
        # Настройка мока для случая, когда пользователь не найден
        mock_response = MagicMock()
        mock_response.status_code = 404
        mock_get.return_value = mock_response
        
        # Вызов тестируемого метода
        user = self.user_service.get_user(999)
        
        # Проверки
        mock_get.assert_called_once_with('https://api.example.com/users/999')
        assert user is None
    
    @patch('user_service.UserService.get_user')
    def test_get_user_name(self, mock_get_user):
        # Мокирование метода get_user вместо внешнего API
        mock_get_user.return_value = {'id': 1, 'name': 'John Doe'}
        
        # Вызов тестируемого метода
        name = self.user_service.get_user_name(1)
        
        # Проверки
        assert name == 'John Doe'
        mock_get_user.assert_called_once_with(1)
    
    @patch('user_service.UserService.get_user')
    def test_get_user_name_unknown(self, mock_get_user):
        # Пользователь не найден
        mock_get_user.return_value = None
        
        # Вызов тестируемого метода
        name = self.user_service.get_user_name(999)
        
        # Проверки
        assert name == 'Unknown User'
        mock_get_user.assert_called_once_with(999)
```

#### Инструменты для модульного тестирования в Python

- **pytest** — мощный и популярный фреймворк для тестирования
- **unittest** — стандартная библиотека для тестирования в Python
- **nose2** — расширение для unittest
- **mock** (часть стандартной библиотеки с Python 3.3) — для создания моков

### 2. Интеграционное тестирование (Integration Testing)

Интеграционное тестирование проверяет взаимодействие между различными модулями или сервисами, которые используются вместе. Оно фокусируется на проверке того, что интерфейсы и потоки данных между компонентами работают как ожидается.

#### Особенности интеграционного тестирования

- Тестирует взаимодействие нескольких компонентов или систем
- Может требовать взаимодействия с внешними системами (базы данных, API)
- Сложнее и медленнее, чем модульное тестирование
- Обычно выполняется после успешного модульного тестирования
- Может потребовать настройки тестовой среды или контейнеров

#### Подходы к интеграционному тестированию

1. **Инкрементальное интеграционное тестирование:**
   - **Снизу вверх (Bottom-Up)**: начинается с тестирования модулей нижнего уровня, затем постепенно поднимается до высокоуровневых компонентов
   - **Сверху вниз (Top-Down)**: начинается с высокоуровневых модулей, постепенно интегрирует низкоуровневые компоненты
   - **Сэндвич (Sandwich)**: комбинирует подходы снизу вверх и сверху вниз

2. **Большой взрыв (Big Bang)**: все или большинство компонентов интегрируются одновременно, затем тестируются как единое целое

#### Пример интеграционного теста для веб-приложения на Flask и базы данных

```python
# файл: app.py
from flask import Flask, jsonify, request
import sqlite3

app = Flask(__name__)

def get_db_connection():
    conn = sqlite3.connect('database.db')
    conn.row_factory = sqlite3.Row
    return conn

@app.route('/users', methods=['GET'])
def get_users():
    conn = get_db_connection()
    users = conn.execute('SELECT * FROM users').fetchall()
    conn.close()
    return jsonify([dict(user) for user in users])

@app.route('/users', methods=['POST'])
def create_user():
    user_data = request.json
    if not user_data or not 'name' in user_data or not 'email' in user_data:
        return jsonify({'error': 'Name and email are required'}), 400
    
    conn = get_db_connection()
    conn.execute('INSERT INTO users (name, email) VALUES (?, ?)',
                (user_data['name'], user_data['email']))
    conn.commit()
    conn.close()
    return jsonify({'message': 'User created successfully'}), 201

def init_db():
    conn = get_db_connection()
    conn.execute('CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT, email TEXT)')
    conn.commit()
    conn.close()

if __name__ == '__main__':
    init_db()
    app.run(debug=True)
```

```python
# файл: test_app_integration.py
import os
import tempfile
import pytest
from app import app, init_db

@pytest.fixture
def client():
    # Создаем временный файл базы данных
    db_fd, db_path = tempfile.mkstemp()
    app.config['DATABASE'] = db_path
    
    # Настраиваем тестового клиента
    with app.test_client() as client:
        # Создаем таблицы
        with app.app_context():
            init_db()
        yield client
    
    # Удаляем временный файл базы данных после завершения
    os.close(db_fd)
    os.unlink(db_path)

def test_get_users_empty(client):
    # Проверяем, что изначально список пользователей пуст
    response = client.get('/users')
    assert response.status_code == 200
    assert response.json == []

def test_create_user(client):
    # Создаем нового пользователя
    response = client.post('/users', 
                         json={'name': 'John Doe', 'email': 'john@example.com'})
    assert response.status_code == 201
    assert response.json['message'] == 'User created successfully'

def test_get_users_after_creation(client):
    # Создаем пользователя
    client.post('/users', json={'name': 'John Doe', 'email': 'john@example.com'})
    
    # Проверяем, что пользователь появился в списке
    response = client.get('/users')
    assert response.status_code == 200
    assert len(response.json) == 1
    assert response.json[0]['name'] == 'John Doe'
    assert response.json[0]['email'] == 'john@example.com'

def test_create_user_invalid_data(client):
    # Проверяем валидацию данных при создании пользователя
    response = client.post('/users', json={'name': 'John Doe'})  # Без email
    assert response.status_code == 400
    assert 'error' in response.json
```

#### Интеграционное тестирование с реальными базами данных

Для более полного интеграционного тестирования иногда необходимо использовать реальные базы данных. Существуют инструменты для создания временных баз данных для тестов:

```python
# файл: test_with_postgresql.py
import pytest
import psycopg2
from psycopg2.extensions import ISOLATION_LEVEL_AUTOCOMMIT
import testing.postgresql
from your_app import create_app

@pytest.fixture(scope='session')
def postgresql():
    # Создаем временную базу данных PostgreSQL
    with testing.postgresql.Postgresql() as postgresql:
        yield postgresql

@pytest.fixture
def app(postgresql):
    # Настраиваем приложение с временной базой данных
    app = create_app({
        'TESTING': True,
        'DATABASE_URI': postgresql.url(),
    })
    
    # Создаем схему базы данных
    with app.app_context():
        with psycopg2.connect(postgresql.url()) as conn:
            conn.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
            with conn.cursor() as cursor:
                cursor.execute('''
                    CREATE TABLE users (
                        id SERIAL PRIMARY KEY,
                        name TEXT NOT NULL,
                        email TEXT NOT NULL UNIQUE
                    )
                ''')
    
    yield app

@pytest.fixture
def client(app):
    return app.test_client()

def test_create_and_get_user(client):
    # Создаем пользователя
    response = client.post('/api/users', json={
        'name': 'John Doe',
        'email': 'john@example.com'
    })
    assert response.status_code == 201
    
    # Получаем список пользователей
    response = client.get('/api/users')
    assert response.status_code == 200
    data = response.json
    
    # Проверяем, что пользователь создан
    assert len(data) == 1
    assert data[0]['name'] == 'John Doe'
    assert data[0]['email'] == 'john@example.com'
```

#### Инструменты для интеграционного тестирования в Python

- **pytest** — универсальный фреймворк, подходит и для интеграционного тестирования
- **testing.postgresql, testing.mongodb** — для создания временных баз данных
- **responses** или **requests-mock** — для мокирования HTTP-запросов
- **pytest-docker** — для запуска тестов с Docker-контейнерами
- **pytest-flask** — расширения для тестирования Flask-приложений

### 3. Тестирование E2E (End-to-End)

E2E-тестирование проверяет работу всего приложения от начала до конца, эмулируя реальное взаимодействие пользователя с системой. Эти тесты позволяют убедиться, что пользовательские сценарии работают корректно через все слои приложения.

#### Особенности E2E-тестирования

- Проверяет полный рабочий процесс системы, как со стороны пользователя
- Охватывает все компоненты системы, включая интерфейс, бэкенд, базу данных
- Обычно автоматизирует действия пользователя в браузере или мобильном приложении
- Тесты медленные, сложные в поддержке, но очень ценные
- Используется для проверки критических бизнес-процессов

#### Пример E2E теста с использованием Selenium и Python

```python
# файл: test_login_flow.py
import pytest
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

@pytest.fixture
def browser():
    # Инициализация драйвера браузера
    driver = webdriver.Chrome()
    driver.implicitly_wait(10)  # Ожидание до 10 секунд для поиска элементов
    yield driver
    # Закрытие браузера после завершения теста
    driver.quit()

def test_successful_login(browser):
    # Открытие страницы входа
    browser.get("https://example.com/login")
    
    # Заполнение формы входа
    username_input = browser.find_element(By.ID, "username")
    password_input = browser.find_element(By.ID, "password")
    submit_button = browser.find_element(By.ID, "login-button")
    
    username_input.send_keys("test_user")
    password_input.send_keys("password123")
    submit_button.click()
    
    # Ожидание перенаправления на страницу профиля
    WebDriverWait(browser, 10).until(
        EC.url_contains("/profile")
    )
    
    # Проверка, что мы успешно вошли
    welcome_message = browser.find_element(By.ID, "welcome-message")
    assert "Welcome, test_user" in welcome_message.text
    
    # Проверка наличия элементов, доступных только авторизованным пользователям
    assert browser.find_element(By.ID, "logout-button").is_displayed()

def test_failed_login(browser):
    # Открытие страницы входа
    browser.get("https://example.com/login")
    
    # Заполнение формы входа с неверными данными
    username_input = browser.find_element(By.ID, "username")
    password_input = browser.find_element(By.ID, "password")
    submit_button = browser.find_element(By.ID, "login-button")
    
    username_input.send_keys("test_user")
    password_input.send_keys("wrong_password")
    submit_button.click()
    
    # Проверка наличия сообщения об ошибке
    error_message = WebDriverWait(browser, 10).until(
        EC.visibility_of_element_located((By.CLASS_NAME, "error-message"))
    )
    assert "Invalid username or password" in error_message.text
    
    # Проверка, что мы остались на странице входа
    assert "/login" in browser.current_url
```

#### Пример E2E теста с использованием Playwright и Python

Playwright — более современная альтернатива Selenium от Microsoft:

```python
# файл: test_checkout_flow.py
import pytest
from playwright.sync_api import sync_playwright, Page, expect

@pytest.fixture(scope="session")
def browser_context():
    with sync_playwright() as playwright:
        browser = playwright.chromium.launch(headless=True)
        context = browser.new_context()
        yield context
        context.close()
        browser.close()

@pytest.fixture
def page(browser_context):
    page = browser_context.new_page()
    yield page
    page.close()

def test_product_purchase_flow(page: Page):
    # Открытие главной страницы
    page.goto("https://example.com")
    
    # Поиск продукта
    page.fill("input[name='search']", "laptop")
    page.click("button[type='submit']")
    
    # Ожидание результатов поиска
    page.wait_for_selector(".product-list")
    
    # Выбор первого продукта
    page.click(".product-item:first-child a")
    
    # Добавление в корзину
    page.click("#add-to-cart-button")
    
    # Переход в корзину
    page.click("#cart-icon")
    
    # Проверка, что продукт в корзине
    expect(page.locator(".cart-items")).to_contain_text("laptop")
    
    # Переход к оформлению заказа
    page.click("#checkout-button")
    
    # Заполнение формы оформления заказа
    page.fill("#first-name", "John")
    page.fill("#last-name", "Doe")
    page.fill("#email", "john@example.com")
    page.fill("#address", "123 Test St")
    page.fill("#city", "Test City")
    page.fill("#postal-code", "12345")
    page.select_option("#country", "United States")
    
    # Переход к оплате
    page.click("#continue-button")
    
    # Заполнение данных оплаты
    page.fill("#card-number", "4111111111111111")
    page.fill("#card-expiry", "12/25")
    page.fill("#card-cvc", "123")
    
    # Завершение заказа
    page.click("#place-order-button")
    
    # Проверка успешного оформления заказа
    page.wait_for_selector(".order-confirmation")
    expect(page.locator(".order-confirmation")).to_contain_text("Thank you for your order")
    expect(page.locator(".order-number")).to_be_visible()
```

#### Тестирование API с использованием pytest и requests

```python
# файл: test_api_e2e.py
import pytest
import requests

BASE_URL = "https://api.example.com"

@pytest.fixture
def auth_token():
    # Получение токена авторизации
    response = requests.post(f"{BASE_URL}/auth/login", json={
        "username": "test_user",
        "password": "test_password"
    })
    assert response.status_code == 200
    data = response.json()
    assert "token" in data
    return data["token"]

def test_create_read_update_delete_user(auth_token):
    headers = {"Authorization": f"Bearer {auth_token}"}
    
    # 1. Создание пользователя
    create_response = requests.post(
        f"{BASE_URL}/users",
        json={"name": "Jane Doe", "email": "jane@example.com", "role": "user"},
        headers=headers
    )
    assert create_response.status_code == 201
    user_data = create_response.json()
    user_id = user_data["id"]
    
    # 2. Чтение данных пользователя
    get_response = requests.get(f"{BASE_URL}/users/{user_id}", headers=headers)
    assert get_response.status_code == 200
    get_data = get_response.json()
    assert get_data["name"] == "Jane Doe"
    assert get_data["email"] == "jane@example.com"
    
    # 3. Обновление данных пользователя
    update_response = requests.put(
        f"{BASE_URL}/users/{user_id}",
        json={"name": "Jane Smith", "email": "jane.smith@example.com", "role": "admin"},
        headers=headers
    )
    assert update_response.status_code == 200
    
    # Проверка обновленных данных
    get_updated = requests.get(f"{BASE_URL}/users/{user_id}", headers=headers)
    updated_data = get_updated.json()
    assert updated_data["name"] == "Jane Smith"
    assert updated_data["email"] == "jane.smith@example.com"
    assert updated_data["role"] == "admin"
    
    # 4. Удаление пользователя
    delete_response = requests.delete(f"{BASE_URL}/users/{user_id}", headers=headers)
    assert delete_response.status_code == 204
    
    # Проверка, что пользователь удален
    get_deleted = requests.get(f"{BASE_URL}/users/{user_id}", headers=headers)
    assert get_deleted.status_code == 404
```

#### Инструменты для E2E-тестирования в Python

- **Selenium** — классический инструмент для автоматизации браузера
- **Playwright** — современный инструмент от Microsoft для автоматизации браузера
- **Cypress** — популярный JavaScript-фреймворк, который также можно использовать с Python
- **Pytest-BDD** — для написания BDD-тестов (Behavior-Driven Development)
- **Requests** — для тестирования REST API
- **Locust** — для нагрузочного тестирования

## Пирамида тестирования

Пирамида тестирования — это концепция, которая описывает соотношение различных типов тестов в проекте.

```
         /\
        /  \
       /E2E \
      /      \
     /--------\
    /Integration\
   /            \
  /---------------\
 /      Unit       \
/___________________\
```

- **Основание (Unit)**: большое количество юнит-тестов, которые быстрые, дешёвые и простые
- **Середина (Integration)**: среднее количество интеграционных тестов
- **Вершина (E2E)**: небольшое количество E2E-тестов, которые медленные и сложные

### Преимущества следования пирамиде тестирования

1. **Эффективность**: сосредоточение на быстрых модульных тестах
2. **Раннее обнаружение ошибок**: большинство ошибок будет обнаружено на уровне модульных тестов
3. **Снижение затрат**: отладка ошибок дешевле на ранних этапах
4. **Повышение устойчивости**: меньше ненадежных тестов (E2E-тесты часто бывают хрупкими)
5. **Улучшение архитектуры**: необходимость писать модульные тесты способствует лучшему дизайну кода

## Методологии и подходы к тестированию

### TDD (Test-Driven Development)

TDD — это методология разработки, при которой тесты пишутся до написания кода. Цикл разработки по TDD:

1. **Red**: написать тест, который не проходит
2. **Green**: написать минимальный код, достаточный для прохождения теста
3. **Refactor**: улучшить код, сохраняя его работоспособность

#### Пример TDD-цикла на Python

```python
# 1. Red: Пишем тест, который не проходит
# файл: test_user.py
import pytest
from user import User

def test_user_creation():
    user = User(name="John", email="john@example.com")
    assert user.name == "John"
    assert user.email == "john@example.com"

def test_user_greeting():
    user = User(name="John", email="john@example.com")
    assert user.greeting() == "Hello, John!"
```

```python
# 2. Green: Пишем минимальный код для прохождения теста
# файл: user.py
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email
    
    def greeting(self):
        return f"Hello, {self.name}!"
```

```python
# 3. Refactor: Улучшаем код (например, добавляем валидацию)
# файл: user.py
import re

class User:
    def __init__(self, name, email):
        if not name or not isinstance(name, str):
            raise ValueError("Name must be a non-empty string")
        
        email_pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
        if not re.match(email_pattern, email):
            raise ValueError("Invalid email format")
        
        self.name = name
        self.email = email
    
    def greeting(self):
        return f"Hello, {self.name}!"
```

```python
# Добавляем тесты для проверки валидации
# файл: test_user.py (дополненный)
def test_invalid_name():
    with pytest.raises(ValueError):
        User(name="", email="john@example.com")
    
    with pytest.raises(ValueError):
        User(name=None, email="john@example.com")

def test_invalid_email():
    with pytest.raises(ValueError):
        User(name="John", email="invalid-email")
    
    with pytest.raises(ValueError):
        User(name="John", email="")
```

### BDD (Behavior-Driven Development)

BDD — это расширение TDD, которое фокусируется на поведении системы с точки зрения бизнеса. BDD использует язык, понятный не только разработчикам, но и не техническим заинтересованным сторонам.

#### Пример BDD-теста с использованием pytest-bdd

```gherkin
# файл: features/login.feature
Feature: User Login
  As a registered user
  I want to login to the application
  So that I can access my account

  Scenario: Successful login
    Given I am on the login page
    When I enter valid credentials
    And I click the login button
    Then I should be redirected to the dashboard
    And I should see a welcome message

  Scenario: Failed login
    Given I am on the login page
    When I enter invalid credentials
    And I click the login button
    Then I should see an error message
    And I should remain on the login page
```

```python
# файл: test_login_bdd.py
from pytest_bdd import scenarios, given, when, then, parsers
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

# Загрузка сценариев из feature-файла
scenarios('features/login.feature')

@pytest.fixture
def browser():
    driver = webdriver.Chrome()
    driver.implicitly_wait(10)
    yield driver
    driver.quit()

@given("I am on the login page")
def login_page(browser):
    browser.get("https://example.com/login")

@when("I enter valid credentials")
def enter_valid_credentials(browser):
    username_input = browser.find_element(By.ID, "username")
    password_input = browser.find_element(By.ID, "password")
    username_input.send_keys("valid_user")
    password_input.send_keys("valid_password")

@when("I enter invalid credentials")
def enter_invalid_credentials(browser):
    username_input = browser.find_element(By.ID, "username")
    password_input = browser.find_element(By.ID, "password")
    username_input.send_keys("valid_user")
    password_input.send_keys("invalid_password")

@when("I click the login button")
def click_login(browser):
    login_button = browser.find_element(By.ID, "login-button")
    login_button.click()

@then("I should be redirected to the dashboard")
def check_dashboard_redirect(browser):
    WebDriverWait(browser, 10).until(
        EC.url_contains("/dashboard")
    )

@then("I should see a welcome message")
def check_welcome_message(browser):
    welcome_element = browser.find_element(By.ID, "welcome-message")
    assert "Welcome" in welcome_element.text

@then("I should see an error message")
def check_error_message(browser):
    error_element = WebDriverWait(browser, 10).until(
        EC.visibility_of_element_located((By.CLASS_NAME, "error-message"))
    )
    assert "Invalid credentials" in error_element.text

@then("I should remain on the login page")
def check_still_on_login_page(browser):
    assert "/login" in browser.current_url
```

## Лучшие практики тестирования

### 1. Написание эффективных тестов

- **Независимость тестов**: каждый тест должен быть независимым от других
- **Детерминированность**: тесты должны давать одинаковый результат при каждом запуске
- **Очевидность**: должно быть ясно, что именно тестируется и каков ожидаемый результат
- **Простота**: тесты должны быть простыми и фокусироваться на одной функциональности
- **Быстрота**: тесты должны выполняться быстро для обеспечения быстрой обратной связи

### 2. Организация тестов

```python
# Структура проекта
project/
  ├── src/
  │   ├── module1/
  │   │   ├── __init__.py
  │   │   └── file1.py
  │   └── module2/
  │       ├── __init__.py
  │       └── file2.py
  ├── tests/
  │   ├── unit/
  │   │   ├── test_module1.py
  │   │   └── test_module2.py
  │   ├── integration/
  │   │   └── test_module_integration.py
  │   └── e2e/
  │       └── test_feature1_e2e.py
  ├── conftest.py  # Общие фикстуры для pytest
  └── pytest.ini   # Конфигурация pytest
```

### 3. Фикстуры и настройка окружения

Фикстуры позволяют настраивать и очищать окружение для тестов:

```python
# файл: conftest.py
import pytest
import os
import tempfile
from app import create_app, db

@pytest.fixture(scope="function")
def app():
    # Создаем временную базу данных
    db_fd, db_path = tempfile.mkstemp()
    
    app = create_app({
        'TESTING': True,
        'SQLALCHEMY_DATABASE_URI': f'sqlite:///{db_path}',
    })
    
    # Создаем контекст приложения
    with app.app_context():
        # Создаем таблицы и начальные данные
        db.create_all()
        yield app
        # Очистка после теста
        db.session.remove()
        db.drop_all()
    
    # Удаляем временный файл
    os.close(db_fd)
    os.unlink(db_path)

@pytest.fixture
def client(app):
    return app.test_client()

@pytest.fixture
def runner(app):
    return app.test_cli_runner()

@pytest.fixture
def auth(client):
    # Хелпер для аутентификации
    class AuthActions:
        def login(self, username="test", password="test"):
            return client.post(
                "/auth/login",
                data={"username": username, "password": password}
            )
        
        def logout(self):
            return client.get("/auth/logout")
    
    return AuthActions()
```

### 4. Мокирование и изоляция

Мокирование — это техника, которая позволяет заменить зависимости на контролируемые объекты для изоляции тестируемого кода.

#### Использование моков для HTTP-запросов

```python
# файл: test_api_client.py
import pytest
import requests
from unittest.mock import patch, MagicMock
from api_client import APIClient

@pytest.fixture
def api_client():
    return APIClient(base_url="https://api.example.com")

@patch("api_client.requests.get")
def test_get_user(mock_get, api_client):
    # Настройка мока
    mock_response = MagicMock()
    mock_response.status_code = 200
    mock_response.json.return_value = {"id": 1, "name": "John Doe"}
    mock_get.return_value = mock_response
    
    # Вызов тестируемого метода
    user = api_client.get_user(1)
    
    # Проверки
    mock_get.assert_called_once_with("https://api.example.com/users/1")
    assert user["name"] == "John Doe"
```

#### Использование контекстных менеджеров для патчинга

```python
# файл: test_with_context_manager.py
def test_send_email():
    with patch("smtplib.SMTP") as mock_smtp:
        # Настройка поведения мока
        instance = mock_smtp.return_value
        
        # Вызов функции, которая отправляет email
        send_notification_email("user@example.com", "Test subject", "Test body")
        
        # Проверки
        mock_smtp.assert_called_once_with("smtp.example.com", 587)
        instance.starttls.assert_called_once()
        instance.login.assert_called_once_with("sender@example.com", "password")
        
        # Проверка аргументов вызова sendmail
        args, _ = instance.sendmail.call_args
        assert args[0] == "sender@example.com"
        assert args[1] == "user@example.com"
        assert "Test subject" in args[2]
        assert "Test body" in args[2]
```

### 5. Параметризация тестов

Параметризация позволяет запускать один и тот же тест с разными входными данными:

```python
# файл: test_with_params.py
@pytest.mark.parametrize("input_val, expected", [
    (1, 1),
    (2, 4),
    (3, 9),
    (4, 16),
    (5, 25),
])
def test_square_function(input_val, expected):
    assert square(input_val) == expected

@pytest.mark.parametrize("email, is_valid", [
    ("user@example.com", True),
    ("invalid-email", False),
    ("user@.com", False),
    ("user@example", False),
    ("user@example.co.uk", True),
])
def test_email_validation(email, is_valid):
    validator = EmailValidator()
    assert validator.is_valid(email) == is_valid
```

### 6. Покрытие кода (Code Coverage)

Покрытие кода показывает, какая часть кода была выполнена во время тестов:

```bash
# Установка pytest-cov
pip install pytest-cov

# Запуск тестов с отчетом о покрытии
pytest --cov=src tests/

# Генерация HTML-отчета
pytest --cov=src --cov-report=html tests/
```

```python
# файл: .coveragerc
[run]
source = src
omit = tests/*

[report]
exclude_lines =
    pragma: no cover
    def __repr__
    raise NotImplementedError
    if __name__ == .__main__.:
    pass
```

## Тестирование в CI/CD

### 1. Настройка CI для тестирования

#### Пример конфигурации GitHub Actions

```yaml
# файл: .github/workflows/tests.yml
name: Tests

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.8, 3.9, 3.10]

    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install pytest pytest-cov flake8
        if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
    
    - name: Lint with flake8
      run: |
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
    
    - name: Test with pytest
      run: |
        pytest --cov=src tests/
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v1
```

#### Пример конфигурации GitLab CI

```yaml
# файл: .gitlab-ci.yml
image: python:3.9

stages:
  - lint
  - test

variables:
  PIP_CACHE_DIR: "$CI_PROJECT_DIR/.pip-cache"

cache:
  paths:
    - .pip-cache/

before_script:
  - python -m pip install --upgrade pip
  - pip install -r requirements.txt

lint:
  stage: lint
  script:
    - pip install flake8
    - flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics

unit-test:
  stage: test
  script:
    - pip install pytest pytest-cov
    - pytest tests/unit/

integration-test:
  stage: test
  services:
    - postgres:13
  variables:
    POSTGRES_DB: test_db
    POSTGRES_USER: test_user
    POSTGRES_PASSWORD: test_password
    DATABASE_URL: "postgresql://test_user:test_password@postgres:5432/test_db"
  script:
    - pip install pytest pytest-cov
    - pytest tests/integration/

e2e-test:
  stage: test
  image: python:3.9-selenium
  script:
    - pip install pytest playwright
    - playwright install
    - pytest tests/e2e/
```

### 2. Стратегии запуска тестов в CI/CD

- **Запуск всех тестов для каждого Pull Request**
  - Обеспечивает максимальную надежность
  - Может занимать много времени для больших проектов

- **Ограниченный набор тестов для Pull Request, полный набор для main**
  - Быстрая обратная связь для разработчиков
  - Компромисс между скоростью и надежностью

- **Выборочное тестирование на основе изменений**
  - Анализ зависимостей для запуска только затронутых тестов
  - Сложнее настроить, но обеспечивает оптимальную скорость

## Заключение

Тестирование является неотъемлемой частью процесса разработки программного обеспечения, обеспечивая надежность, соответствие требованиям и возможность безопасного внесения изменений. Понимание и применение различных уровней тестирования — модульного, интеграционного и E2E — позволяет создавать качественные и устойчивые к ошибкам приложения.

Ключевые факторы успешной стратегии тестирования:
1. **Баланс между различными типами тестов** в соответствии с концепцией пирамиды тестирования
2. **Автоматизация тестов** для быстрой обратной связи и повторяемости результатов
3. **Интеграция тестирования в CI/CD** для обеспечения непрерывного качества
4. **Использование правильных инструментов и методологий** для эффективного тестирования
5. **Культура тестирования в команде**, поощряющая написание тестов на ранних этапах разработки

Инвестиции в хорошо продуманную и комплексную стратегию тестирования в долгосрочной перспективе повышают производительность, снижают количество ошибок и улучшают общее качество программного продукта.
