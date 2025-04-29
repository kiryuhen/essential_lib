# Сетевое программирование в Python

## Введение

Сетевое программирование — это создание программ, которые взаимодействуют с другими программами через компьютерные сети. Python предоставляет множество инструментов для работы с сетями, от низкоуровневых модулей для работы с сокетами до высокоуровневых библиотек для работы с веб-сервисами.

В этом уроке мы рассмотрим:

1. Основы сетевого программирования и модель OSI
2. Низкоуровневое сетевое программирование с модулем `socket`
3. Работу с HTTP-запросами с помощью библиотеки `requests`
4. Создание API-клиентов для взаимодействия с веб-сервисами
5. Асинхронное сетевое программирование с `asyncio` и `aiohttp`

## Основы сетевого программирования

### Модель OSI

Модель OSI (Open Systems Interconnection) — это концептуальная модель, которая стандартизирует функции коммуникационной системы, разделяя их на абстрактные уровни. Она состоит из 7 уровней:

1. **Физический уровень** — передача битов через физический носитель
2. **Канальный уровень** — надежная передача данных между двумя узлами
3. **Сетевой уровень** — маршрутизация пакетов по сети (IP)
4. **Транспортный уровень** — надежная передача данных между конечными устройствами (TCP, UDP)
5. **Сеансовый уровень** — управление сеансами связи
6. **Представительский уровень** — преобразование данных
7. **Прикладной уровень** — взаимодействие с пользовательскими приложениями (HTTP, FTP, SMTP, DNS)

В Python сетевое программирование обычно происходит на транспортном (сокеты) и прикладном уровнях (HTTP, FTP и т.д.).

### Протоколы TCP и UDP

Два основных протокола транспортного уровня:

- **TCP (Transmission Control Protocol)** — ориентированный на соединение, гарантирует доставку и порядок пакетов
- **UDP (User Datagram Protocol)** — без установления соединения, не гарантирует доставку и порядок пакетов, но имеет меньшие накладные расходы

### Порты и сокеты

- **Порт** — числовой идентификатор (0-65535), который используется для идентификации конкретного процесса или службы
- **Сокет** — конечная точка соединения, которая состоит из IP-адреса и номера порта

## Низкоуровневое сетевое программирование с модулем socket

Модуль `socket` предоставляет доступ к низкоуровневому сетевому интерфейсу операционной системы.

### Создание клиента TCP

```python
import socket

def tcp_client():
    # Создаем TCP-сокет
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    try:
        # Устанавливаем соединение с сервером
        server_address = ('localhost', 8000)
        print(f"Подключение к {server_address[0]}:{server_address[1]}")
        client_socket.connect(server_address)
        
        # Отправляем данные
        message = "Привет, сервер!"
        print(f"Отправка: {message}")
        client_socket.sendall(message.encode('utf-8'))
        
        # Получаем ответ
        data = client_socket.recv(1024)
        print(f"Получено: {data.decode('utf-8')}")
    
    finally:
        # Закрываем соединение
        print("Закрытие соединения")
        client_socket.close()

if __name__ == '__main__':
    tcp_client()
```

### Создание сервера TCP

```python
import socket

def tcp_server():
    # Создаем TCP-сокет
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    # Привязываем сокет к адресу и порту
    server_address = ('localhost', 8000)
    print(f"Запуск сервера на {server_address[0]}:{server_address[1]}")
    server_socket.bind(server_address)
    
    # Слушаем входящие соединения (максимум 5 в очереди)
    server_socket.listen(5)
    
    try:
        while True:
            print("Ожидание соединения...")
            client_socket, client_address = server_socket.accept()
            
            try:
                print(f"Соединение с {client_address[0]}:{client_address[1]}")
                
                # Получаем данные от клиента
                while True:
                    data = client_socket.recv(1024)
                    if data:
                        print(f"Получено: {data.decode('utf-8')}")
                        
                        # Отправляем ответ
                        response = "Сервер получил ваше сообщение"
                        client_socket.sendall(response.encode('utf-8'))
                    else:
                        print("Нет данных от клиента")
                        break
            
            finally:
                # Закрываем соединение с клиентом
                client_socket.close()
    
    finally:
        # Закрываем серверный сокет
        print("Закрытие сервера")
        server_socket.close()

if __name__ == '__main__':
    tcp_server()
```

### Создание клиента UDP

```python
import socket

def udp_client():
    # Создаем UDP-сокет
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    
    try:
        # Адрес сервера
        server_address = ('localhost', 8001)
        
        # Отправляем данные
        message = "Привет, UDP-сервер!"
        print(f"Отправка: {message}")
        client_socket.sendto(message.encode('utf-8'), server_address)
        
        # Получаем ответ
        data, server = client_socket.recvfrom(1024)
        print(f"Получено от {server[0]}:{server[1]}: {data.decode('utf-8')}")
    
    finally:
        # Закрываем сокет
        print("Закрытие соединения")
        client_socket.close()

if __name__ == '__main__':
    udp_client()
```

### Создание сервера UDP

```python
import socket

def udp_server():
    # Создаем UDP-сокет
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    
    # Привязываем сокет к адресу и порту
    server_address = ('localhost', 8001)
    print(f"Запуск UDP-сервера на {server_address[0]}:{server_address[1]}")
    server_socket.bind(server_address)
    
    try:
        while True:
            print("Ожидание дейтаграммы...")
            
            # Получаем данные и адрес клиента
            data, client_address = server_socket.recvfrom(1024)
            print(f"Получено {len(data)} байт от {client_address[0]}:{client_address[1]}")
            print(f"Данные: {data.decode('utf-8')}")
            
            # Отправляем ответ
            if data:
                response = "Сервер получил вашу дейтаграмму"
                sent = server_socket.sendto(response.encode('utf-8'), client_address)
                print(f"Отправлено {sent} байт обратно клиенту")
    
    finally:
        # Закрываем сокет
        print("Закрытие сервера")
        server_socket.close()

if __name__ == '__main__':
    udp_server()
```

### Работа с доменными именами

```python
import socket

def dns_lookup(host):
    """Поиск IP-адреса по доменному имени"""
    try:
        ip_address = socket.gethostbyname(host)
        print(f"IP-адрес {host}: {ip_address}")
        return ip_address
    except socket.gaierror:
        print(f"Не удалось получить IP-адрес для {host}")
        return None

def get_host_info(host):
    """Получение информации о хосте"""
    try:
        host_info = socket.gethostbyname_ex(host)
        print(f"Имя хоста: {host_info[0]}")
        print(f"Алиасы: {host_info[1]}")
        print(f"IP-адреса: {host_info[2]}")
        return host_info
    except socket.gaierror:
        print(f"Не удалось получить информацию о хосте {host}")
        return None

def get_service_info(port, protocol='tcp'):
    """Получение информации о сервисе по номеру порта"""
    try:
        service_name = socket.getservbyport(port, protocol)
        print(f"Сервис на порту {port}/{protocol}: {service_name}")
        return service_name
    except socket.error:
        print(f"Неизвестный сервис на порту {port}/{protocol}")
        return None

# Пример использования
if __name__ == '__main__':
    dns_lookup('www.python.org')
    get_host_info('www.python.org')
    get_service_info(80, 'tcp')  # HTTP
    get_service_info(443, 'tcp')  # HTTPS
    get_service_info(53, 'udp')  # DNS
```

### Создание многопоточного эхо-сервера

```python
import socket
import threading

def handle_client(client_socket, client_address):
    """Обработка клиентского соединения"""
    print(f"Новое соединение с {client_address[0]}:{client_address[1]}")
    
    try:
        # Получаем данные от клиента
        while True:
            data = client_socket.recv(1024)
            if not data:
                break
            
            # Выводим полученные данные
            print(f"Получено от {client_address[0]}:{client_address[1]}: {data.decode('utf-8')}")
            
            # Отправляем данные обратно (эхо)
            client_socket.sendall(data)
    
    except Exception as e:
        print(f"Ошибка при обработке клиента: {str(e)}")
    
    finally:
        # Закрываем соединение
        client_socket.close()
        print(f"Соединение с {client_address[0]}:{client_address[1]} закрыто")

def echo_server(host='localhost', port=8002):
    """Запуск многопоточного эхо-сервера"""
    # Создаем серверный сокет
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    # Устанавливаем опцию для повторного использования адреса
    server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    
    # Привязываем сокет к адресу и порту
    server_address = (host, port)
    server_socket.bind(server_address)
    
    # Слушаем входящие соединения
    server_socket.listen(5)
    print(f"Сервер запущен на {host}:{port}")
    
    try:
        while True:
            # Принимаем входящее соединение
            client_socket, client_address = server_socket.accept()
            
            # Создаем новый поток для обработки клиента
            client_thread = threading.Thread(
                target=handle_client,
                args=(client_socket, client_address)
            )
            client_thread.daemon = True
            client_thread.start()
    
    except KeyboardInterrupt:
        print("Сервер остановлен")
    
    finally:
        # Закрываем серверный сокет
        server_socket.close()

if __name__ == '__main__':
    echo_server()
```

## Работа с HTTP-запросами с помощью библиотеки requests

Библиотека `requests` предоставляет простой и элегантный интерфейс для отправки HTTP-запросов.

### Установка requests

```
pip install requests
```

### Отправка GET-запроса

```python
import requests

def simple_get_request(url):
    """Отправка простого GET-запроса"""
    try:
        # Отправка запроса
        response = requests.get(url)
        
        # Проверка статуса
        response.raise_for_status()  # Вызывает исключение, если статус не 2xx
        
        # Вывод информации о запросе
        print(f"URL: {url}")
        print(f"Статус: {response.status_code}")
        print(f"Заголовки ответа: {response.headers}")
        print(f"Тип содержимого: {response.headers.get('content-type')}")
        print(f"Кодировка: {response.encoding}")
        
        # Содержимое ответа
        print(f"Текст ответа (первые 100 символов): {response.text[:100]}...")
        
        return response
    
    except requests.exceptions.RequestException as e:
        print(f"Ошибка при выполнении запроса: {str(e)}")
        return None

# Пример использования
if __name__ == '__main__':
    simple_get_request('https://www.python.org')
```

### Отправка GET-запроса с параметрами

```python
import requests

def get_with_params(url, params):
    """Отправка GET-запроса с параметрами"""
    try:
        # Отправка запроса с параметрами
        response = requests.get(url, params=params)
        
        # Проверка статуса
        response.raise_for_status()
        
        # Вывод информации о запросе
        print(f"URL с параметрами: {response.url}")
        print(f"Статус: {response.status_code}")
        
        # Возвращаем ответ
        return response
    
    except requests.exceptions.RequestException as e:
        print(f"Ошибка при выполнении запроса: {str(e)}")
        return None

# Пример использования
if __name__ == '__main__':
    # Поиск на сайте httpbin
    response = get_with_params('https://httpbin.org/get', {'q': 'python', 'lang': 'ru'})
    
    if response:
        # Содержимое ответа в формате JSON
        data = response.json()
        print(f"Аргументы запроса: {data['args']}")
        print(f"Заголовки запроса: {data['headers']}")
```

### Отправка POST-запроса

```python
import requests

def post_request(url, data=None, json=None):
    """Отправка POST-запроса"""
    try:
        # Отправка запроса
        response = requests.post(url, data=data, json=json)
        
        # Проверка статуса
        response.raise_for_status()
        
        # Вывод информации о запросе
        print(f"URL: {url}")
        print(f"Статус: {response.status_code}")
        
        # Возвращаем ответ
        return response
    
    except requests.exceptions.RequestException as e:
        print(f"Ошибка при выполнении запроса: {str(e)}")
        return None

# Пример использования
if __name__ == '__main__':
    # Отправка данных формы
    form_data = {'name': 'John', 'email': 'john@example.com'}
    response1 = post_request('https://httpbin.org/post', data=form_data)
    
    if response1:
        data1 = response1.json()
        print(f"Форма: {data1['form']}")
    
    # Отправка JSON-данных
    json_data = {'name': 'Alice', 'age': 25, 'is_student': True}
    response2 = post_request('https://httpbin.org/post', json=json_data)
    
    if response2:
        data2 = response2.json()
        print(f"JSON: {data2['json']}")
```

### Отправка запросов с заголовками и cookies

```python
import requests

def request_with_headers_and_cookies(url):
    """Отправка запроса с заголовками и cookies"""
    # Заголовки запроса
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36',
        'Accept-Language': 'ru-RU,ru;q=0.9,en-US;q=0.8,en;q=0.7',
        'X-Custom-Header': 'PythonScript'
    }
    
    # Cookies
    cookies = {
        'session_id': '12345',
        'user_id': 'abc123',
    }
    
    try:
        # Отправка запроса
        response = requests.get(url, headers=headers, cookies=cookies)
        
        # Проверка статуса
        response.raise_for_status()
        
        # Вывод информации о запросе
        print(f"URL: {url}")
        print(f"Статус: {response.status_code}")
        
        # Возвращаем ответ
        return response
    
    except requests.exceptions.RequestException as e:
        print(f"Ошибка при выполнении запроса: {str(e)}")
        return None

# Пример использования
if __name__ == '__main__':
    response = request_with_headers_and_cookies('https://httpbin.org/headers')
    
    if response:
        data = response.json()
        print(f"Заголовки запроса: {data['headers']}")
        
    response = request_with_headers_and_cookies('https://httpbin.org/cookies')
    
    if response:
        data = response.json()
        print(f"Cookies запроса: {data['cookies']}")
```

### Загрузка файлов

```python
import requests

def upload_file(url, file_path):
    """Загрузка файла на сервер"""
    try:
        # Открываем файл для чтения
        with open(file_path, 'rb') as file:
            # Создаем словарь файлов
            files = {'file': (file_path, file, 'application/octet-stream')}
            
            # Отправка файла
            response = requests.post(url, files=files)
            
            # Проверка статуса
            response.raise_for_status()
            
            # Вывод информации о запросе
            print(f"Файл {file_path} успешно загружен")
            print(f"Статус: {response.status_code}")
            
            # Возвращаем ответ
            return response
    
    except (IOError, requests.exceptions.RequestException) as e:
        print(f"Ошибка при загрузке файла: {str(e)}")
        return None

# Пример использования
if __name__ == '__main__':
    # Для тестирования создаем текстовый файл
    with open('test_upload.txt', 'w') as f:
        f.write('Это тестовый файл для загрузки')
    
    # Загружаем файл на сервис httpbin
    response = upload_file('https://httpbin.org/post', 'test_upload.txt')
    
    if response:
        data = response.json()
        print(f"Информация о загруженном файле: {data['files']}")
```

### Скачивание файлов

```python
import requests
import os

def download_file(url, save_path):
    """Скачивание файла с сервера"""
    try:
        # Отправка запроса с потоковой передачей данных
        print(f"Скачивание файла с {url}")
        response = requests.get(url, stream=True)
        
        # Проверка статуса
        response.raise_for_status()
        
        # Получаем имя файла из URL
        if not os.path.basename(save_path):
            filename = os.path.basename(url.split('?')[0])
            save_path = os.path.join(save_path, filename)
        
        # Получаем общий размер файла (если сервер предоставляет эту информацию)
        total_size = int(response.headers.get('content-length', 0))
        
        # Скачиваем файл
        with open(save_path, 'wb') as file:
            if total_size > 0:
                print(f"Общий размер файла: {total_size / 1024:.1f} КБ")
                downloaded = 0
                for chunk in response.iter_content(chunk_size=8192):
                    if chunk:
                        file.write(chunk)
                        downloaded += len(chunk)
                        # Отображаем прогресс
                        progress = (downloaded / total_size) * 100
                        print(f"Скачано: {downloaded / 1024:.1f} КБ ({progress:.1f}%)", end='\r')
            else:
                print("Скачивание файла...")
                for chunk in response.iter_content(chunk_size=8192):
                    if chunk:
                        file.write(chunk)
        
        print(f"\nФайл успешно скачан и сохранен как {save_path}")
        return save_path
    
    except requests.exceptions.RequestException as e:
        print(f"Ошибка при скачивании файла: {str(e)}")
        return None

# Пример использования
if __name__ == '__main__':
    # Скачиваем логотип Python
    url = 'https://www.python.org/static/img/python-logo.png'
    download_file(url, 'python-logo.png')
```

### Работа с сессиями

```python
import requests

def session_example():
    """Пример использования сессии requests"""
    # Создаем сессию
    session = requests.Session()
    
    # Устанавливаем заголовки и куки для всех запросов в сессии
    session.headers.update({
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
        'Accept-Language': 'ru-RU,ru;q=0.9,en-US;q=0.8,en;q=0.7'
    })
    
    session.cookies.update({
        'session_id': 'abc123'
    })
    
    try:
        # Первый запрос с сессией
        response1 = session.get('https://httpbin.org/cookies')
        print("Запрос 1 - Cookies:")
        print(response1.json())
        
        # Второй запрос - добавляем новый куки
        session.cookies.update({'user_id': 'xyz789'})
        response2 = session.get('https://httpbin.org/cookies')
        print("\nЗапрос 2 - Cookies (после обновления):")
        print(response2.json())
        
        # Третий запрос - отправляем форму
        response3 = session.post(
            'https://httpbin.org/post',
            data={'key': 'value'}
        )
        print("\nЗапрос 3 - Отправка формы:")
        print(f"Форма: {response3.json()['form']}")
        print(f"Cookies: {response3.json()['headers'].get('Cookie')}")
    
    finally:
        # Закрываем сессию
        session.close()

# Пример использования
if __name__ == '__main__':
    session_example()
```

### Обработка различных форматов ответа

```python
import requests
import json
from bs4 import BeautifulSoup
import xml.etree.ElementTree as ET

def handle_json_response(url):
    """Обработка JSON-ответа"""
    try:
        response = requests.get(url)
        response.raise_for_status()
        
        # Проверяем, что ответ содержит JSON
        if 'application/json' in response.headers.get('Content-Type', ''):
            data = response.json()
            print(f"JSON-данные: {json.dumps(data, indent=2)}")
            return data
        else:
            print("Ответ не содержит JSON-данные")
            return None
    
    except (requests.exceptions.RequestException, json.JSONDecodeError) as e:
        print(f"Ошибка при обработке JSON-ответа: {str(e)}")
        return None

def handle_html_response(url):
    """Обработка HTML-ответа с помощью BeautifulSoup"""
    try:
        response = requests.get(url)
        response.raise_for_status()
        
        # Проверяем, что ответ содержит HTML
        if 'text/html' in response.headers.get('Content-Type', ''):
            # Создаем объект BeautifulSoup
            soup = BeautifulSoup(response.text, 'html.parser')
            
            # Извлекаем информацию
            title = soup.title.string if soup.title else "Нет заголовка"
            links = [a.get('href') for a in soup.find_all('a', href=True)]
            
            print(f"Заголовок страницы: {title}")
            print(f"Количество ссылок: {len(links)}")
            print("Первые 5 ссылок:")
            for link in links[:5]:
                print(f"  - {link}")
            
            return {
                'title': title,
                'links': links
            }
        else:
            print("Ответ не содержит HTML-данные")
            return None
    
    except requests.exceptions.RequestException as e:
        print(f"Ошибка при обработке HTML-ответа: {str(e)}")
        return None

def handle_xml_response(url):
    """Обработка XML-ответа"""
    try:
        response = requests.get(url)
        response.raise_for_status()
        
        # Проверяем, что ответ содержит XML
        if 'application/xml' in response.headers.get('Content-Type', '') or 'text/xml' in response.headers.get('Content-Type', ''):
            # Парсим XML
            root = ET.fromstring(response.text)
            
            # Здесь можно извлекать данные из XML
            print(f"Корневой элемент: {root.tag}")
            print("Дочерние элементы:")
            for child in root:
                print(f"  - {child.tag}")
            
            return root
        else:
            print("Ответ не содержит XML-данные")
            return None
    
    except (requests.exceptions.RequestException, ET.ParseError) as e:
        print(f"Ошибка при обработке XML-ответа: {str(e)}")
        return None

# Пример использования
if __name__ == '__main__':
    # JSON-пример
    handle_json_response('https://httpbin.org/json')
    
    # HTML-пример
    handle_html_response('https://www.python.org')
    
    # XML-пример
    # Примечание: не все сервисы возвращают чистый XML
    handle_xml_response('https://www.w3schools.com/xml/note.xml')
```

## Создание API-клиентов для взаимодействия с веб-сервисами

### Простой API-клиент для GitHub

```python
import requests
import json

class GitHubAPI:
    """Простой клиент для работы с API GitHub"""
    
    def __init__(self, token=None):
        """Инициализация клиента"""
        self.base_url = "https://api.github.com"
        self.session = requests.Session()
        
        # Устанавливаем заголовки по умолчанию
        self.session.headers.update({
            'Accept': 'application/vnd.github.v3+json',
            'User-Agent': 'Python GitHub API Client'
        })
        
        # Если предоставлен токен, добавляем его в заголовки
        if token:
            self.session.headers.update({
                'Authorization': f'token {token}'
            })
    
    def get_user(self, username):
        """Получение информации о пользователе"""
        url = f"{self.base_url}/users/{username}"
        
        try:
            response = self.session.get(url)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Ошибка при получении информации о пользователе: {str(e)}")
            return None
    
    def get_repos(self, username, sort='updated', direction='desc'):
        """Получение репозиториев пользователя"""
        url = f"{self.base_url}/users/{username}/repos"
        params = {
            'sort': sort,
            'direction': direction
        }
        
        try:
            response = self.session.get(url, params=params)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Ошибка при получении репозиториев: {str(e)}")
            return None
    
    def get_repo(self, owner, repo):
        """Получение информации о репозитории"""
        url = f"{self.base_url}/repos/{owner}/{repo}"
        
        try:
            response = self.session.get(url)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Ошибка при получении информации о репозитории: {str(e)}")
            return None
    
    def search_repositories(self, query, sort='stars', order='desc', per_page=10):
        """Поиск репозиториев"""
        url = f"{self.base_url}/search/repositories"
        params = {
            'q': query,
            'sort': sort,
            'order': order,
            'per_page': per_page
        }
        
        try:
            response = self.session.get(url, params=params)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Ошибка при поиске репозиториев: {str(e)}")
            return None
    
    def create_gist(self, files, description="", public=True):
        """Создание Gist"""
        url = f"{self.base_url}/gists"
        
        # Преобразуем словарь файлов в формат, ожидаемый API
        gist_files = {}
        for filename, content in files.items():
            gist_files[filename] = {"content": content}
        
        data = {
            "description": description,
            "public": public,
            "files": gist_files
        }
        
        try:
            response = self.session.post(url, json=data)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Ошибка при создании Gist: {str(e)}")
            return None
    
    def close(self):
        """Закрытие сессии"""
        self.session.close()

# Пример использования
if __name__ == '__main__':
    # Создаем клиент (можно указать свой токен для авторизации)
    github = GitHubAPI()
    
    try:
        # Получаем информацию о пользователе
        user = github.get_user('octocat')
        if user:
            print(f"Информация о пользователе {user['login']}:")
            print(f"  Имя: {user.get('name', 'Не указано')}")
            print(f"  Компания: {user.get('company', 'Не указано')}")
            print(f"  Местоположение: {user.get('location', 'Не указано')}")
            print(f"  Публичные репозитории: {user['public_repos']}")
            print(f"  Подписчики: {user['followers']}")
        
        # Поиск репозиториев по Python
        search_results = github.search_repositories('language:python stars:>1000', per_page=5)
        if search_results:
            print("\nПопулярные Python-репозитории:")
            for repo in search_results['items']:
                print(f"  - {repo['full_name']}: {repo['description']}")
                print(f"    Звезды: {repo['stargazers_count']}, Форки: {repo['forks_count']}")
    
    finally:
        # Закрываем сессию
        github.close()
```

### Клиент для работы с погодным API

```python
import requests
import json
from datetime import datetime

class WeatherAPI:
    """Клиент для работы с OpenWeatherMap API"""
    
    def __init__(self, api_key):
        """Инициализация клиента"""
        self.api_key = api_key
        self.base_url = "https://api.openweathermap.org/data/2.5"
        self.session = requests.Session()
    
    def get_current_weather(self, city, units='metric', lang='ru'):
        """Получение текущей погоды в городе"""
        url = f"{self.base_url}/weather"
        params = {
            'q': city,
            'appid': self.api_key,
            'units': units,
            'lang': lang
        }
        
        try:
            response = self.session.get(url, params=params)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Ошибка при получении погоды: {str(e)}")
            return None
    
    def get_forecast(self, city, units='metric', lang='ru', cnt=40):
        """Получение прогноза погоды на 5 дней"""
        url = f"{self.base_url}/forecast"
        params = {
            'q': city,
            'appid': self.api_key,
            'units': units,
            'lang': lang,
            'cnt': cnt  # Количество временных точек (8 в день, т.е. каждые 3 часа)
        }
        
        try:
            response = self.session.get(url, params=params)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Ошибка при получении прогноза погоды: {str(e)}")
            return None
    
    def get_weather_by_coords(self, lat, lon, units='metric', lang='ru'):
        """Получение погоды по координатам"""
        url = f"{self.base_url}/weather"
        params = {
            'lat': lat,
            'lon': lon,
            'appid': self.api_key,
            'units': units,
            'lang': lang
        }
        
        try:
            response = self.session.get(url, params=params)
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Ошибка при получении погоды по координатам: {str(e)}")
            return None
    
    def format_weather_data(self, data):
        """Форматирование данных о погоде"""
        if not data:
            return "Данные о погоде недоступны"
        
        try:
            city = data['name']
            country = data['sys']['country']
            temp = data['main']['temp']
            feels_like = data['main']['feels_like']
            humidity = data['main']['humidity']
            pressure = data['main']['pressure']
            wind_speed = data['wind']['speed']
            wind_direction = self._get_wind_direction(data['wind']['deg'])
            weather_desc = data['weather'][0]['description']
            
            sunrise = datetime.fromtimestamp(data['sys']['sunrise']).strftime('%H:%M')
            sunset = datetime.fromtimestamp(data['sys']['sunset']).strftime('%H:%M')
            
            return (
                f"Текущая погода в {city}, {country}:\n"
                f"Температура: {temp}°C (ощущается как {feels_like}°C)\n"
                f"Погодные условия: {weather_desc}\n"
                f"Влажность: {humidity}%\n"
                f"Давление: {pressure} гПа\n"
                f"Ветер: {wind_speed} м/с, направление: {wind_direction}\n"
                f"Восход солнца: {sunrise}, закат: {sunset}"
            )
        except KeyError as e:
            return f"Ошибка при форматировании данных о погоде: отсутствует ключ {str(e)}"
    
    def format_forecast_data(self, data):
        """Форматирование данных о прогнозе погоды"""
        if not data:
            return "Данные о прогнозе погоды недоступны"
        
        try:
            city = data['city']['name']
            country = data['city']['country']
            
            forecast_text = f"Прогноз погоды на 5 дней для {city}, {country}:\n\n"
            
            # Группируем прогноз по дням
            days = {}
            for item in data['list']:
                dt = datetime.fromtimestamp(item['dt'])
                day = dt.date()
                
                if day not in days:
                    days[day] = []
                
                days[day].append({
                    'time': dt.strftime('%H:%M'),
                    'temp': item['main']['temp'],
                    'weather': item['weather'][0]['description'],
                    'wind_speed': item['wind']['speed'],
                    'wind_dir': self._get_wind_direction(item['wind']['deg'])
                })
            
            # Форматируем прогноз по дням
            for day, items in sorted(days.items()):
                forecast_text += f"{day.strftime('%d.%m.%Y')}:\n"
                
                for item in items:
                    forecast_text += f"  {item['time']}: {item['temp']}°C, {item['weather']}, "
                    forecast_text += f"ветер {item['wind_speed']} м/с ({item['wind_dir']})\n"
                
                forecast_text += "\n"
            
            return forecast_text
        
        except KeyError as e:
            return f"Ошибка при форматировании данных о прогнозе: отсутствует ключ {str(e)}"
    
    def _get_wind_direction(self, degrees):
        """Преобразование градусов направления ветра в текстовое представление"""
        directions = [
            "С", "ССВ", "СВ", "ВСВ", "В", "ВЮВ", "ЮВ", "ЮЮВ",
            "Ю", "ЮЮЗ", "ЮЗ", "ЗЮЗ", "З", "ЗСЗ", "СЗ", "ССЗ"
        ]
        index = round(degrees / 22.5) % 16
        return directions[index]
    
    def close(self):
        """Закрытие сессии"""
        self.session.close()

# Пример использования
if __name__ == '__main__':
    # Для работы примера нужен API-ключ OpenWeatherMap
    API_KEY = "ваш_api_ключ"  # Замените на свой ключ
    
    # Создаем клиент
    weather_api = WeatherAPI(API_KEY)
    
    try:
        # Получаем текущую погоду
        weather_data = weather_api.get_current_weather("Moscow")
        if weather_data:
            print(weather_api.format_weather_data(weather_data))
        
        # Получаем прогноз погоды
        forecast_data = weather_api.get_forecast("Moscow", cnt=16)  # 2 дня (8 точек в день)
        if forecast_data:
            print("\n" + weather_api.format_forecast_data(forecast_data))
    
    finally:
        # Закрываем сессию
        weather_api.close()
```

## Асинхронное сетевое программирование с asyncio и aiohttp

### Введение в асинхронное программирование

Асинхронное программирование позволяет выполнять несколько операций ввода-вывода параллельно без использования потоков или процессов. Это особенно полезно для сетевых приложений, которые тратят много времени на ожидание ответов от серверов.

### Основы asyncio

```python
import asyncio
import time

async def say_after(delay, what):
    """Асинхронная функция, которая выводит сообщение после задержки"""
    await asyncio.sleep(delay)
    print(what)

async def main():
    """Основная асинхронная функция"""
    print(f"Начало в {time.strftime('%X')}")
    
    # Выполнение задач последовательно
    await say_after(1, "hello")
    await say_after(2, "world")
    
    print(f"Последовательное выполнение завершено в {time.strftime('%X')}")
    
    # Выполнение задач параллельно
    task1 = asyncio.create_task(say_after(1, "hello again"))
    task2 = asyncio.create_task(say_after(2, "world again"))
    
    # Ожидаем завершения обеих задач
    await task1
    await task2
    
    print(f"Параллельное выполнение завершено в {time.strftime('%X')}")

# Запуск асинхронной функции
if __name__ == '__main__':
    asyncio.run(main())
```

## Практические задания

### Задание 1: Разработка утилиты для проверки доступности веб-сайтов

Создайте инструмент командной строки, который проверяет доступность списка веб-сайтов и выводит отчет о их состоянии.

**Требования:**
1. Утилита должна принимать список URL в качестве аргументов командной строки или из файла
2. Для каждого URL необходимо проверить: доступность, HTTP-статус, время отклика, заголовок страницы
3. Реализуйте как синхронную, так и асинхронную версии и сравните их производительность
4. Добавьте возможность сохранения отчета в различных форматах (TXT, CSV, JSON)
5. Реализуйте проверку заголовков безопасности (HTTPS, HSTS, CSP, X-XSS-Protection и т.д.)

**Решение:**

```python
import argparse
import csv
import json
import requests
import time
import asyncio
import aiohttp
from urllib.parse import urlparse
from bs4 import BeautifulSoup
import socket
from concurrent.futures import ThreadPoolExecutor

class WebsiteChecker:
    """Базовый класс для проверки доступности веб-сайтов"""
    
    def __init__(self):
        """Инициализация проверки"""
        self.results = []
    
    def check_website(self, url):
        """Проверяет доступность веб-сайта и возвращает результаты"""
        # Добавляем схему, если она отсутствует
        if not url.startswith(('http://', 'https://')):
            url = 'https://' + url
        
        start_time = time.time()
        result = {
            'url': url,
            'timestamp': time.strftime('%Y-%m-%d %H:%M:%S'),
            'is_available': False,
            'status_code': None,
            'response_time': None,
            'title': None,
            'security_headers': {}
        }
        
        try:
            # Проверяем DNS
            domain = urlparse(url).netloc
            socket.gethostbyname(domain)
            
            # Отправляем запрос
            response = requests.get(url, timeout=10, allow_redirects=True)
            
            # Вычисляем время отклика
            response_time = time.time() - start_time
            
            # Получаем заголовок страницы
            soup = BeautifulSoup(response.text, 'html.parser')
            title = soup.title.string if soup.title else "No Title"
            
            # Обновляем результат
            result.update({
                'is_available': True,
                'status_code': response.status_code,
                'response_time': round(response_time, 3),
                'title': title,
                'final_url': response.url
            })
            
            # Проверяем заголовки безопасности
            security_headers = {
                'Strict-Transport-Security': response.headers.get('Strict-Transport-Security'),
                'Content-Security-Policy': response.headers.get('Content-Security-Policy'),
                'X-Content-Type-Options': response.headers.get('X-Content-Type-Options'),
                'X-Frame-Options': response.headers.get('X-Frame-Options'),
                'X-XSS-Protection': response.headers.get('X-XSS-Protection')
            }
            result['security_headers'] = security_headers
            
        except requests.exceptions.RequestException as e:
            result['error'] = str(e)
        except socket.gaierror:
            result['error'] = f"DNS resolution failed for {domain}"
        except Exception as e:
            result['error'] = str(e)
        
        return result
    
    def check_websites(self, urls):
        """Синхронная проверка списка веб-сайтов"""
        self.results = []
        start_time = time.time()
        
        for url in urls:
            result = self.check_website(url)
            self.results.append(result)
            print(f"Проверен {url}: {'доступен' if result['is_available'] else 'недоступен'}")
        
        elapsed = time.time() - start_time
        print(f"Синхронная проверка {len(urls)} сайтов завершена за {elapsed:.2f} секунд")
        
        return self.results
    
    def check_websites_parallel(self, urls, max_workers=10):
        """Параллельная проверка списка веб-сайтов с помощью потоков"""
        self.results = []
        start_time = time.time()
        
        with ThreadPoolExecutor(max_workers=max_workers) as executor:
            # Запускаем проверки параллельно
            future_to_url = {executor.submit(self.check_website, url): url for url in urls}
            
            # Собираем результаты по мере их готовности
            for future in future_to_url:
                url = future_to_url[future]
                try:
                    result = future.result()
                    self.results.append(result)
                    print(f"Проверен {url}: {'доступен' if result['is_available'] else 'недоступен'}")
                except Exception as e:
                    print(f"Ошибка при проверке {url}: {str(e)}")
        
        elapsed = time.time() - start_time
        print(f"Параллельная проверка {len(urls)} сайтов завершена за {elapsed:.2f} секунд")
        
        return self.results
    
    def save_results_txt(self, filename):
        """Сохраняет результаты в текстовый файл"""
        with open(filename, 'w', encoding='utf-8') as file:
            file.write("ОТЧЕТ О ПРОВЕРКЕ ДОСТУПНОСТИ ВЕБА-САЙТОВ\n")
            file.write("=" * 50 + "\n\n")
            
            for result in self.results:
                file.write(f"URL: {result['url']}\n")
                file.write(f"Временная метка: {result['timestamp']}\n")
                file.write(f"Доступен: {result['is_available']}\n")
                
                if result['is_available']:
                    file.write(f"Статус: {result['status_code']}\n")
                    file.write(f"Время отклика: {result['response_time']} секунд\n")
                    file.write(f"Заголовок: {result['title']}\n")
                    
                    file.write("Заголовки безопасности:\n")
                    for header, value in result['security_headers'].items():
                        file.write(f"  {header}: {value or 'Отсутствует'}\n")
                else:
                    file.write(f"Ошибка: {result.get('error', 'Неизвестная ошибка')}\n")
                
                file.write("\n" + "-" * 50 + "\n\n")
            
            file.write(f"Всего проверено сайтов: {len(self.results)}")
        
        print(f"Результаты сохранены в {filename}")
    
    def save_results_csv(self, filename):
        """Сохраняет результаты в CSV-файл"""
        with open(filename, 'w', encoding='utf-8', newline='') as file:
            # Определяем поля CSV
            fieldnames = [
                'url', 'timestamp', 'is_available', 'status_code', 
                'response_time', 'title', 'error'
            ]
            
            # Создаем объект writer
            writer = csv.DictWriter(file, fieldnames=fieldnames)
            
            # Записываем заголовки
            writer.writeheader()
            
            # Записываем данные
            for result in self.results:
                # Создаем копию результата с необходимыми полями
                row = {k: result.get(k) for k in fieldnames if k in result}
                writer.writerow(row)
        
        print(f"Результаты сохранены в {filename}")
    
    def save_results_json(self, filename):
        """Сохраняет результаты в JSON-файл"""
        with open(filename, 'w', encoding='utf-8') as file:
            json.dump(self.results, file, ensure_ascii=False, indent=2)
        
        print(f"Результаты сохранены в {filename}")

class AsyncWebsiteChecker(WebsiteChecker):
    """Асинхронная версия проверки доступности веб-сайтов"""
    
    async def check_website_async(self, session, url):
        """Асинхронно проверяет доступность веб-сайта"""
        # Добавляем схему, если она отсутствует
        if not url.startswith(('http://', 'https://')):
            url = 'https://' + url
        
        start_time = time.time()
        result = {
            'url': url,
            'timestamp': time.strftime('%Y-%m-%d %H:%M:%S'),
            'is_available': False,
            'status_code': None,
            'response_time': None,
            'title': None,
            'security_headers': {}
        }
        
        try:
            # Проверяем DNS (здесь можно использовать aiodns для асинхронной проверки)
            domain = urlparse(url).netloc
            # Это может блокировать, но обычно это быстрая операция
            socket.gethostbyname(domain)
            
            # Отправляем запрос
            async with session.get(url, timeout=10, allow_redirects=True) as response:
                # Вычисляем время отклика
                response_time = time.time() - start_time
                
                # Получаем содержимое страницы
                html = await response.text()
                
                # Получаем заголовок страницы
                soup = BeautifulSoup(html, 'html.parser')
                title = soup.title.string if soup.title else "No Title"
                
                # Обновляем результат
                result.update({
                    'is_available': True,
                    'status_code': response.status,
                    'response_time': round(response_time, 3),
                    'title': title,
                    'final_url': str(response.url)
                })
                
                # Проверяем заголовки безопасности
                security_headers = {
                    'Strict-Transport-Security': response.headers.get('Strict-Transport-Security'),
                    'Content-Security-Policy': response.headers.get('Content-Security-Policy'),
                    'X-Content-Type-Options': response.headers.get('X-Content-Type-Options'),
                    'X-Frame-Options': response.headers.get('X-Frame-Options'),
                    'X-XSS-Protection': response.headers.get('X-XSS-Protection')
                }
                result['security_headers'] = security_headers
            
        except aiohttp.ClientError as e:
            result['error'] = str(e)
        except socket.gaierror:
            result['error'] = f"DNS resolution failed for {domain}"
        except Exception as e:
            result['error'] = str(e)
        
        print(f"Проверен {url}: {'доступен' if result['is_available'] else 'недоступен'}")
        return result
    
    async def check_websites_async(self, urls, max_concurrent=10):
        """Асинхронная проверка списка веб-сайтов"""
        self.results = []
        start_time = time.time()
        
        # Создаем семафор для ограничения количества одновременных запросов
        semaphore = asyncio.Semaphore(max_concurrent)
        
        async with aiohttp.ClientSession() as session:
            async def check_with_semaphore(url):
                async with semaphore:
                    return await self.check_website_async(session, url)
            
            # Создаем задачи для каждого URL
            tasks = [check_with_semaphore(url) for url in urls]
            
            # Дожидаемся выполнения всех задач
            self.results = await asyncio.gather(*tasks)
        
        elapsed = time.time() - start_time
        print(f"Асинхронная проверка {len(urls)} сайтов завершена за {elapsed:.2f} секунд")
        
        return self.results

def read_urls_from_file(filename):
    """Читает список URL из файла"""
    with open(filename, 'r', encoding='utf-8') as file:
        urls = [line.strip() for line in file if line.strip()]
    return urls

def main():
    """Основная функция"""
    # Создаем парсер аргументов командной строки
    parser = argparse.ArgumentParser(description='Утилита для проверки доступности веб-сайтов')
    
    # Добавляем аргументы
    parser.add_argument('urls', nargs='*', help='Список URL для проверки')
    parser.add_argument('-f', '--file', help='Файл со списком URL (по одному URL на строку)')
    parser.add_argument('-o', '--output', help='Базовое имя файла для сохранения результатов')
    parser.add_argument('-a', '--async', dest='async_mode', action='store_true', help='Использовать асинхронную версию')
    parser.add_argument('-p', '--parallel', action='store_true', help='Использовать параллельную версию (с потоками)')
    parser.add_argument('-c', '--concurrent', type=int, default=10, help='Максимальное количество одновременных запросов')
    
    # Парсим аргументы
    args = parser.parse_args()
    
    # Получаем список URL
    urls = args.urls
    if args.file:
        file_urls = read_urls_from_file(args.file)
        urls.extend(file_urls)
    
    if not urls:
        parser.error("Необходимо указать хотя бы один URL или файл с URL")
    
    # Выбираем метод проверки
    if args.async_mode:
        # Асинхронная проверка
        checker = AsyncWebsiteChecker()
        
        async def run_async_check():
            await checker.check_websites_async(urls, args.concurrent)
        
        # Запускаем асинхронную проверку
        asyncio.run(run_async_check())
    elif args.parallel:
        # Параллельная проверка с потоками
        checker = WebsiteChecker()
        checker.check_websites_parallel(urls, args.concurrent)
    else:
        # Синхронная проверка
        checker = WebsiteChecker()
        checker.check_websites(urls)
    
    # Сохраняем результаты, если указан выходной файл
    if args.output:
        checker.save_results_txt(f"{args.output}.txt")
        checker.save_results_csv(f"{args.output}.csv")
        checker.save_results_json(f"{args.output}.json")

if __name__ == '__main__':
    main()
```

### Задание 2: Разработка чат-сервера и клиента с помощью сокетов

Создайте простой чат-сервер и клиент, позволяющие нескольким пользователям обмениваться сообщениями в режиме реального времени.

**Требования:**
1. Сервер должен поддерживать подключение нескольких клиентов одновременно
2. Клиенты должны иметь возможность выбрать никнейм при подключении
3. Сервер должен транслировать сообщения от одного клиента всем остальным
4. Добавьте поддержку приватных сообщений между клиентами
5. Реализуйте простое шифрование для защиты сообщений (например, с помощью библиотеки `cryptography`)

**Решение (Сервер):**

```python
import socket
import threading
import json
import time
import logging
import argparse
import base64
from cryptography.fernet import Fernet
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC

# Настройка логирования
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

class ChatServer:
    """Простой чат-сервер с поддержкой шифрования"""
    
    def __init__(self, host='0.0.0.0', port=9999, use_encryption=True):
        """Инициализация сервера"""
        self.host = host
        self.port = port
        self.use_encryption = use_encryption
        
        # Сокет сервера
        self.server_socket = None
        
        # Словарь подключенных клиентов: {socket: {'nickname': nickname, 'key': encryption_key}}
        self.clients = {}
        
        # Блокировка для синхронизации доступа к списку клиентов
        self.clients_lock = threading.Lock()
        
        # Флаг работы сервера
        self.running = False
        
        # Если используем шифрование, генерируем мастер-ключ сервера
        if self.use_encryption:
            # Создаем ключ шифрования на основе пароля (в реальном приложении нужно более надежное решение)
            password = b"server_secret_password"
            salt = b"server_salt"
            kdf = PBKDF2HMAC(
                algorithm=hashes.SHA256(),
                length=32,
                salt=salt,
                iterations=100000,
            )
            key = base64.urlsafe_b64encode(kdf.derive(password))
            self.server_key = key
            self.cipher = Fernet(key)
        else:
            self.server_key = None
            self.cipher = None
    
    def start(self):
        """Запуск сервера"""
        self.server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        
        try:
            self.server_socket.bind((self.host, self.port))
            self.server_socket.listen(5)
            self.running = True
            
            logger.info(f"Сервер запущен на {self.host}:{self.port}")
            
            if self.use_encryption:
                logger.info("Шифрование включено")
            
            # Принимаем входящие соединения
            self._accept_connections()
        
        except Exception as e:
            logger.error(f"Ошибка при запуске сервера: {str(e)}")
            self.stop()
    
    def stop(self):
        """Остановка сервера"""
        self.running = False
        
        # Закрываем соединения со всеми клиентами
        with self.clients_lock:
            for client_socket in list(self.clients.keys()):
                try:
                    client_socket.close()
                except:
                    pass
            self.clients.clear()
        
        # Закрываем серверный сокет
        if self.server_socket:
            try:
                self.server_socket.close()
            except:
                pass
        
        logger.info("Сервер остановлен")
    
    def _accept_connections(self):
        """Принимаем входящие соединения"""
        while self.running:
            try:
                client_socket, client_address = self.server_socket.accept()
                logger.info(f"Новое соединение с {client_address[0]}:{client_address[1]}")
                
                # Запускаем обработку клиента в отдельном потоке
                client_thread = threading.Thread(
                    target=self._handle_client,
                    args=(client_socket, client_address)
                )
                client_thread.daemon = True
                client_thread.start()
            
            except Exception as e:
                if self.running:
                    logger.error(f"Ошибка при принятии соединения: {str(e)}")
                break
    
    def _handle_client(self, client_socket, client_address):
        """Обрабатывает соединение с клиентом"""
        # Клиент должен первым отправить свой никнейм
        try:
            # Если используем шифрование, обмениваемся ключами
            if self.use_encryption:
                # Отправляем публичный ключ сервера
                client_socket.sendall(self.server_key + b'\n')
                
                # Получаем ключ клиента
                client_key = client_socket.recv(1024).strip()
                client_cipher = Fernet(client_key)
            else:
                client_cipher = None
            
            # Получаем никнейм клиента
            nickname = self._receive_message(client_socket, client_cipher)
            
            # Проверяем, что никнейм не занят
            with self.clients_lock:
                if any(client_data['nickname'] == nickname for client_data in self.clients.values()):
                    # Отправляем сообщение об ошибке
                    self._send_message(client_socket, {'type': 'error', 'message': 'Этот никнейм уже занят'}, client_cipher)
                    client_socket.close()
                    return
                
                # Добавляем клиента в словарь
                self.clients[client_socket] = {
                    'nickname': nickname,
                    'address': client_address,
                    'cipher': client_cipher
                }
            
            # Отправляем клиенту сообщение о успешном подключении
            self._send_message(client_socket, {'type': 'system', 'message': f'Добро пожаловать, {nickname}!'}, client_cipher)
            
            # Оповещаем всех клиентов о новом пользователе
            self._broadcast_message({
                'type': 'system',
                'message': f'{nickname} присоединился к чату'
            }, None)
            
            # Отправляем список пользователей
            self._send_user_list(client_socket)
            
            # Обрабатываем сообщения от клиента
            while self.running:
                message_data = self._receive_message(client_socket, client_cipher)
                if not message_data:
                    break
                
                # Обрабатываем сообщение
                self._process_message(client_socket, message_data)
        
        except ConnectionResetError:
            pass
        except Exception as e:
            logger.error(f"Ошибка при обработке клиента: {str(e)}")
        
        finally:
            # Удаляем клиента из словаря и закрываем соединение
            with self.clients_lock:
                if client_socket in self.clients:
                    nickname = self.clients[client_socket]['nickname']
                    del self.clients[client_socket]
                    
                    # Оповещаем всех клиентов о выходе пользователя
                    self._broadcast_message({
                        'type': 'system',
                        'message': f'{nickname} покинул чат'
                    }, None)
            
            try:
                client_socket.close()
            except:
                pass
            
            logger.info(f"Соединение с {client_address[0]}:{client_address[1]} закрыто")
    
    def _process_message(self, client_socket, message_data):
        """Обрабатывает полученное сообщение"""
        try:
            # Получаем данные клиента
            with self.clients_lock:
                if client_socket not in self.clients:
                    return
                
                sender = self.clients[client_socket]['nickname']
            
            # Обрабатываем сообщение в зависимости от типа
            if isinstance(message_data, dict) and 'type' in message_data:
                message_type = message_data['type']
                
                if message_type == 'chat':
                    # Общее сообщение в чат
                    message = message_data.get('message', '')
                    
                    if message.strip():
                        # Добавляем информацию о отправителе
                        message_data['sender'] = sender
                        message_data['timestamp'] = time.time()
                        
                        # Отправляем сообщение всем клиентам
                        self._broadcast_message(message_data, client_socket)
                
                elif message_type == 'private':
                    # Приватное сообщение
                    recipient = message_data.get('recipient', '')
                    message = message_data.get('message', '')
                    
                    if recipient and message.strip():
                        # Добавляем информацию о отправителе
                        message_data['sender'] = sender
                        message_data['timestamp'] = time.time()
                        
                        # Находим сокет получателя
                        recipient_socket = None
                        with self.clients_lock:
                            for socket, data in self.clients.items():
                                if data['nickname'] == recipient:
                                    recipient_socket = socket
                                    break
                        
                        if recipient_socket:
                            # Отправляем сообщение получателю
                            self._send_message(
                                recipient_socket, 
                                message_data, 
                                self.clients[recipient_socket]['cipher']
                            )
                            
                            # Отправляем копию отправителю
                            self._send_message(
                                client_socket,
                                message_data,
                                self.clients[client_socket]['cipher']
                            )
                        else:
                            # Отправляем сообщение об ошибке
                            self._send_message(
                                client_socket,
                                {
                                    'type': 'error',
                                    'message': f'Пользователь {recipient} не найден'
                                },
                                self.clients[client_socket]['cipher']
                            )
                
                elif message_type == 'command':
                    # Обработка команд
                    command = message_data.get('command', '')
                    
                    if command == 'list':
                        # Отправляем список пользователей
                        self._send_user_list(client_socket)
        
        except Exception as e:
            logger.error(f"Ошибка при обработке сообщения: {str(e)}")
    
    def _send_user_list(self, client_socket):
        """Отправляет список пользователей клиенту"""
        with self.clients_lock:
            if client_socket not in self.clients:
                return
            
            # Формируем список пользователей
            users = [data['nickname'] for data in self.clients.values()]
            
            # Отправляем список
            self._send_message(
                client_socket,
                {
                    'type': 'user_list',
                    'users': users
                },
                self.clients[client_socket]['cipher']
            )
    
    def _broadcast_message(self, message_data, exclude_socket):
        """Отправляет сообщение всем клиентам, кроме указанного"""
        with self.clients_lock:
            for client_socket, client_data in list(self.clients.items()):
                if client_socket != exclude_socket:
                    try:
                        self._send_message(client_socket, message_data, client_data['cipher'])
                    except:
                        # Если не удалось отправить сообщение, закрываем соединение
                        try:
                            client_socket.close()
                        except:
                            pass
    
    def _send_message(self, client_socket, message_data, client_cipher):
        """Отправляет сообщение клиенту"""
        try:
            # Преобразуем сообщение в JSON
            message_json = json.dumps(message_data).encode('utf-8')
            
            # Если используем шифрование, шифруем сообщение
            if self.use_encryption and client_cipher:
                message_json = client_cipher.encrypt(message_json)
            
            # Добавляем символ новой строки в качестве разделителя
            message_json += b'\n'
            
            # Отправляем сообщение
            client_socket.sendall(message_json)
        
        except Exception as e:
            logger.error(f"Ошибка при отправке сообщения: {str(e)}")
            raise
    
    def _receive_message(self, client_socket, client_cipher):
        """Получает сообщение от клиента"""
        try:
            # Получаем данные
            data = b''
            while True:
                chunk = client_socket.recv(4096)
                if not chunk:
                    return None
                
                data += chunk
                if b'\n' in data:
                    break
            
            # Удаляем символ новой строки
            data = data.strip()
            
            # Если используем шифрование, расшифровываем сообщение
            if self.use_encryption and client_cipher and data and data != self.server_key:
                data = self.cipher.decrypt(data)
            
            # Преобразуем JSON в словарь
            if data:
                return json.loads(data.decode('utf-8'))
            
            return None
        
        except Exception as e:
            logger.error(f"Ошибка при получении сообщения: {str(e)}")
            raise

def main():
    """Основная функция"""
    # Создаем парсер аргументов командной строки
    parser = argparse.ArgumentParser(description='Простой чат-сервер')
    
    # Добавляем аргументы
    parser.add_argument('-H', '--host', default='0.0.0.0', help='Хост для прослушивания (по умолчанию: 0.0.0.0)')
    parser.add_argument('-p', '--port', type=int, default=9999, help='Порт для прослушивания (по умолчанию: 9999)')
    parser.add_argument('-e', '--encryption', action='store_true', help='Использовать шифрование')
    
    # Парсим аргументы
    args = parser.parse_args()
    
    # Создаем и запускаем сервер
    server = ChatServer(args.host, args.port, args.encryption)
    
    try:
        server.start()
    except KeyboardInterrupt:
        print("\nЗавершение работы сервера...")
    finally:
        server.stop()

if __name__ == '__main__':
    main()
```

**Решение (Клиент):**

```python
import socket
import threading
import json
import time
import logging
import argparse
import sys
import os
import base64
from cryptography.fernet import Fernet
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC

# Настройка логирования
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

class ChatClient:
    """Клиент для чат-сервера"""
    
    def __init__(self, host='localhost', port=9999, nickname=None, use_encryption=True):
        """Инициализация клиента"""
        self.host = host
        self.port = port
        self.nickname = nickname or f"User_{int(time.time()) % 10000}"
        self.use_encryption = use_encryption
        
        # Сокет клиента
        self.client_socket = None
        
        # Флаг подключения
        self.connected = False
        
        # Флаг работы клиента
        self.running = False
        
        # Список пользователей в чате
        self.users = []
        
        # Если используем шифрование, генерируем ключи
        if self.use_encryption:
            # Создаем ключ шифрования
            self.key = Fernet.generate_key()
            self.cipher = Fernet(self.key)
            self.server_cipher = None
        else:
            self.key = None
            self.cipher = None
            self.server_cipher = None
    
    def connect(self):
        """Подключение к серверу"""
        try:
            # Создаем сокет
            self.client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            
            # Подключаемся к серверу
            logger.info(f"Подключение к серверу {self.host}:{self.port}...")
            self.client_socket.connect((self.host, self.port))
            
            # Если используем шифрование, обмениваемся ключами
            if self.use_encryption:
                # Получаем публичный ключ сервера
                server_key = self.client_socket.recv(1024).strip()
                self.server_cipher = Fernet(server_key)
                
                # Отправляем свой публичный ключ
                self.client_socket.sendall(self.key + b'\n')
            
            # Отправляем никнейм
            self._send_message({'type': 'nickname', 'nickname': self.nickname})
            
            # Запускаем поток для чтения сообщений от сервера
            self.running = True
            self.connected = True
            
            receive_thread = threading.Thread(target=self._receive_messages)
            receive_thread.daemon = True
            receive_thread.start()
            
            logger.info(f"Подключение установлено. Никнейм: {self.nickname}")
            
            return True
        
        except Exception as e:
            logger.error(f"Ошибка при подключении к серверу: {str(e)}")
            self.disconnect()
            return False
    
    def disconnect(self):
        """Отключение от сервера"""
        self.running = False
        self.connected = False
        
        if self.client_socket:
            try:
                self.client_socket.close()
            except:
                pass
            self.client_socket = None
        
        logger.info("Отключено от сервера")
    
    def send_message(self, message):
        """Отправляет сообщение в общий чат"""
        if not self.connected:
            logger.error("Нет подключения к серверу")
            return False
        
        try:
            self._send_message({
                'type': 'chat',
                'message': message
            })
            return True
        except Exception as e:
            logger.error(f"Ошибка при отправке сообщения: {str(e)}")
            return False
    
    def send_private_message(self, recipient, message):
        """Отправляет приватное сообщение"""
        if not self.connected:
            logger.error("Нет подключения к серверу")
            return False
        
        try:
            self._send_message({
                'type': 'private',
                'recipient': recipient,
                'message': message
            })
            return True
        except Exception as e:
            logger.error(f"Ошибка при отправке приватного сообщения: {str(e)}")
            return False
    
    def request_user_list(self):
        """Запрашивает список пользователей в чате"""
        if not self.connected:
            logger.error("Нет подключения к серверу")
            return False
        
        try:
            self._send_message({
                'type': 'command',
                'command': 'list'
            })
            return True
        except Exception as e:
            logger.error(f"Ошибка при запросе списка пользователей: {str(e)}")
            return False
    
    def _send_message(self, message_data):
        """Отправляет сообщение серверу"""
        try:
            # Преобразуем сообщение в JSON
            message_json = json.dumps(message_data).encode('utf-8')
            
            # Если используем шифрование, шифруем сообщение
            if self.use_encryption and self.server_cipher:
                message_json = self.server_cipher.encrypt(message_json)
            
            # Добавляем символ новой строки в качестве разделителя
            message_json += b'\n'
            
            # Отправляем сообщение
            self.client_socket.sendall(message_json)
        
        except Exception as e:
            logger.error(f"Ошибка при отправке сообщения: {str(e)}")
            self.disconnect()
            raise
    
    def _receive_messages(self):
        """Поток для чтения сообщений от сервера"""
        while self.running:
            try:
                # Получаем данные
                data = b''
                while self.running:
                    try:
                        chunk = self.client_socket.recv(4096)
                        if not chunk:
                            raise ConnectionResetError("Соединение с сервером потеряно")
                        
                        data += chunk
                        if b'\n' in data:
                            break
                    except socket.timeout:
                        continue
                
                # Удаляем символ новой строки
                data = data.strip()
                
                # Если используем шифрование, расшифровываем сообщение
                if self.use_encryption and self.cipher and data:
                    data = self.cipher.decrypt(data)
                
                # Преобразуем JSON в словарь
                if data:
                    message_data = json.loads(data.decode('utf-8'))
                    self._process_message(message_data)
            
            except ConnectionResetError:
                logger.error("Соединение с сервером потеряно")
                self.disconnect()
                break
            except Exception as e:
                if self.running:
                    logger.error(f"Ошибка при получении сообщения: {str(e)}")
                break
    
    def _process_message(self, message_data):
        """Обрабатывает полученное сообщение"""
        if not isinstance(message_data, dict) or 'type' not in message_data:
            return
        
        message_type = message_data['type']
        
        if message_type == 'chat':
            # Общее сообщение в чат
            sender = message_data.get('sender', 'Unknown')
            message = message_data.get('message', '')
            timestamp = message_data.get('timestamp', time.time())
            
            # Преобразуем timestamp в читаемый формат
            time_str = time.strftime('%H:%M:%S', time.localtime(timestamp))
            
            print(f"[{time_str}] {sender}: {message}")
        
        elif message_type == 'private':
            # Приватное сообщение
            sender = message_data.get('sender', 'Unknown')
            recipient = message_data.get('recipient', 'Unknown')
            message = message_data.get('message', '')
            timestamp = message_data.get('timestamp', time.time())
            
            # Преобразуем timestamp в читаемый формат
            time_str = time.strftime('%H:%M:%S', time.localtime(timestamp))
            
            if sender == self.nickname:
                print(f"[{time_str}] (you → {recipient}): {message}")
            else:
                print(f"[{time_str}] ({sender} → you): {message}")
        
        elif message_type == 'system':
            # Системное сообщение
            message = message_data.get('message', '')
            print(f"*** {message} ***")
        
        elif message_type == 'error':
            # Сообщение об ошибке
            message = message_data.get('message', '')
            print(f"!!! Ошибка: {message} !!!")
        
        elif message_type == 'user_list':
            # Список пользователей
            self.users = message_data.get('users', [])
            print("--- Пользователи в чате ---")
            for user in self.users:
                if user == self.nickname:
                    print(f"  * {user} (you)")
                else:
                    print(f"  * {user}")
            print("-------------------------")

def run_interactive_client():
    """Запускает интерактивного клиента в командной строке"""
    # Создаем парсер аргументов командной строки
    parser = argparse.ArgumentParser(description='Клиент для чат-сервера')
    
    # Добавляем аргументы
    parser.add_argument('-H', '--host', default='localhost', help='Хост сервера (по умолчанию: localhost)')
    parser.add_argument('-p', '--port', type=int, default=9999, help='Порт сервера (по умолчанию: 9999)')
    parser.add_argument('-n', '--nickname', help='Ваш никнейм')
    parser.add_argument('-e', '--encryption', action='store_true', help='Использовать шифрование')
    
    # Парсим аргументы
    args = parser.parse_args()
    
    # Запрашиваем никнейм, если он не указан
    nickname = args.nickname
    if not nickname:
        nickname = input("Введите ваш никнейм: ")
        if not nickname:
            nickname = f"User_{int(time.time()) % 10000}"
    
    # Создаем клиента
    client = ChatClient(args.host, args.port, nickname, args.encryption)
    
    # Подключаемся к серверу
    if not client.connect():
        print("Не удалось подключиться к серверу. Выход.")
        return
    
    # Выводим справку
    print("\n=== Команды чата ===")
    print("/help - вывести справку")
    print("/list - получить список пользователей")
    print("/msg <пользователь> <сообщение> - отправить приватное сообщение")
    print("/quit - выйти из чата")
    print("===================\n")
    
    # Цикл обработки ввода пользователя
    try:
        while client.connected:
            try:
                user_input = input().strip()
                
                if not user_input:
                    continue
                
                if user_input.startswith('/'):
                    # Обрабатываем команды
                    command = user_input[1:].split(' ', 1)[0].lower()
                    
                    if command == 'quit':
                        # Выход из чата
                        break
                    
                    elif command == 'list':
                        # Запрос списка пользователей
                        client.request_user_list()
                    
                    elif command == 'msg':
                        # Отправка приватного сообщения
                        try:
                            args = user_input[5:].strip().split(' ', 1)
                            if len(args) < 2:
                                print("Использование: /msg <пользователь> <сообщение>")
                                continue
                            
                            recipient, message = args
                            client.send_private_message(recipient, message)
                        except Exception as e:
                            print(f"Ошибка: {str(e)}")
                    
                    elif command == 'help':
                        # Вывод справки
                        print("\n=== Команды чата ===")
                        print("/help - вывести справку")
                        print("/list - получить список пользователей")
                        print("/msg <пользователь> <сообщение> - отправить приватное сообщение")
                        print("/quit - выйти из чата")
                        print("===================\n")
                    
                    else:
                        print(f"Неизвестная команда: {command}")
                
                else:
                    # Отправляем обычное сообщение
                    client.send_message(user_input)
            
            except KeyboardInterrupt:
                break
            except Exception as e:
                print(f"Ошибка: {str(e)}")
    
    finally:
        # Отключаемся от сервера
        client.disconnect()
        print("До свидания!")

if __name__ == '__main__':
    run_interactive_client()
```

### Задание 3: Создание HTTP-клиента для работы с API

Разработайте HTTP-клиент для работы с API погодных данных, который позволяет получать прогноз погоды для различных городов.

**Требования:**
1. Клиент должен поддерживать получение данных о текущей погоде и прогнозе на несколько дней
2. Реализуйте кэширование результатов для уменьшения количества запросов
3. Добавьте возможность вывода погоды в различных форматах (текст, JSON, HTML)
4. Реализуйте обработку ошибок и таймаутов
5. Добавьте возможность использования нескольких API погоды с автоматическим переключением в случае ошибок

**Решение:**

```python
import requests
import json
import time
import os
import argparse
from datetime import datetime, timedelta
import html
import logging

# Настройка логирования
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

class WeatherCache:
    """Класс для кэширования результатов запросов погоды"""
    
    def __init__(self, cache_dir='.weather_cache', ttl=3600):
        """
        Инициализация кэша
        
        Args:
            cache_dir: Директория для хранения кэша
            ttl: Время жизни кэша в секундах (по умолчанию: 1 час)
        """
        self.cache_dir = cache_dir
        self.ttl = ttl
        
        # Создаем директорию для кэша, если ее нет
        os.makedirs(cache_dir, exist_ok=True)
    
    def get(self, key):
        """
        Получение данных из кэша
        
        Args:
            key: Ключ кэша
        
        Returns:
            Данные из кэша или None, если кэш отсутствует или устарел
        """
        # Формируем путь к файлу кэша
        cache_file = os.path.join(self.cache_dir, f"{key}.json")
        
        # Проверяем, существует ли файл
        if not os.path.exists(cache_file):
            return None
        
        try:
            # Проверяем время модификации файла
            mtime = os.path.getmtime(cache_file)
            if time.time() - mtime > self.ttl:
                # Кэш устарел
                return None
            
            # Читаем данные из файла
            with open(cache_file, 'r', encoding='utf-8') as f:
                data = json.load(f)
            
            return data
        
        except Exception as e:
            logger.error(f"Ошибка при чтении кэша: {str(e)}")
            return None
    
    def set(self, key, data):
        """
        Сохранение данных в кэш
        
        Args:
            key: Ключ кэша
            data: Данные для сохранения
        """
        # Формируем путь к файлу кэша
        cache_file = os.path.join(self.cache_dir, f"{key}.json")
        
        try:
            # Записываем данные в файл
            with open(cache_file, 'w', encoding='utf-8') as f:
                json.dump(data, f, ensure_ascii=False, indent=2)
        
        except Exception as e:
            logger.error(f"Ошибка при записи в кэш: {str(e)}")
    
    def clear(self):
        """Очистка всего кэша"""
        try:
            for file in os.listdir(self.cache_dir):
                file_path = os.path.join(self.cache_dir, file)
                if os.path.isfile(file_path):
                    os.unlink(file_path)
            
            logger.info("Кэш очищен")
        
        except Exception as e:
            logger.error(f"Ошибка при очистке кэша: {str(e)}")

class WeatherProvider:
    """Базовый класс для провайдеров погоды"""
    
    def __init__(self, api_key=None):
        """Инициализация провайдера"""
        self.api_key = api_key
        self.name = "Base Provider"
    
    def get_current_weather(self, city):
        """
        Получение текущей погоды для города
        
        Args:
            city: Название города
        
        Returns:
            Словарь с данными о погоде или None в случае ошибки
        """
        raise NotImplementedError("Subclasses must implement this method")
    
    def get_forecast(self, city, days=5):
        """
        Получение прогноза погоды для города
        
        Args:
            city: Название города
            days: Количество дней (по умолчанию: 5)
        
        Returns:
            Словарь с данными о прогнозе или None в случае ошибки
        """
        raise NotImplementedError("Subclasses must implement this method")
    
    def _make_request(self, url, params=None):
        """
        Выполнение HTTP-запроса
        
        Args:
            url: URL для запроса
            params: Параметры запроса
        
        Returns:
            Ответ сервера в формате JSON или None в случае ошибки
        """
        try:
            response = requests.get(url, params=params, timeout=10)
            response.raise_for_status()
            return response.json()
        
        except requests.exceptions.RequestException as e:
            logger.error(f"Ошибка при выполнении запроса: {str(e)}")
            return None
        except json.JSONDecodeError:
            logger.error("Ошибка при разборе JSON-ответа")
            return None
        except Exception as e:
            logger.error(f"Неизвестная ошибка: {str(e)}")
            return None

class OpenWeatherMapProvider(WeatherProvider):
    """Провайдер погоды на основе OpenWeatherMap API"""
    
    def __init__(self, api_key):
        """Инициализация провайдера"""
        super().__init__(api_key)
        self.base_url = "https://api.openweathermap.org/data/2.5"
        self.name = "OpenWeatherMap"
    
    def get_current_weather(self, city):
        """Получение текущей погоды для города"""
        url = f"{self.base_url}/weather"
        params = {
            'q': city,
            'appid': self.api_key,
            'units': 'metric',
            'lang': 'ru'
        }
        
        response = self._make_request(url, params)
        if not response:
            return None
        
        try:
            # Преобразуем ответ в единый формат
            weather_data = {
                'provider': self.name,
                'city': response['name'],
                'country': response['sys']['country'],
                'timestamp': response['dt'],
                'datetime': datetime.fromtimestamp(response['dt']).strftime('%Y-%m-%d %H:%M:%S'),
                'temperature': {
                    'current': response['main']['temp'],
                    'feels_like': response['main']['feels_like'],
                    'min': response['main']['temp_min'],
                    'max': response['main']['temp_max']
                },
                'weather': {
                    'main': response['weather'][0]['main'],
                    'description': response['weather'][0]['description'],
                    'icon': response['weather'][0]['icon']
                },
                'wind': {
                    'speed': response['wind']['speed'],
                    'deg': response['wind'].get('deg'),
                    'direction': self._get_wind_direction(response['wind'].get('deg'))
                },
                'humidity': response['main']['humidity'],
                'pressure': response['main']['pressure'],
                'visibility': response.get('visibility'),
                'clouds': response['clouds']['all'],
                'sunrise': response['sys']['sunrise'],
                'sunset': response['sys']['sunset']
            }
            
            return weather_data
        
        except KeyError as e:
            logger.error(f"Ошибка в структуре ответа: {str(e)}")
            return None
    
    def get_forecast(self, city, days=5):
        """Получение прогноза погоды для города"""
        url = f"{self.base_url}/forecast"
        params = {
            'q': city,
            'appid': self.api_key,
            'units': 'metric',
            'lang': 'ru',
            'cnt': days * 8  # 8 точек в день (каждые 3 часа)
        }
        
        response = self._make_request(url, params)
        if not response:
            return None
        
        try:
            # Преобразуем ответ в единый формат
            forecast_data = {
                'provider': self.name,
                'city': response['city']['name'],
                'country': response['city']['country'],
                'timezone': response['city']['timezone'],
                'forecast': []
            }
            
            # Группируем прогноз по дням
            forecasts_by_day = {}
            
            for item in response['list']:
                dt = datetime.fromtimestamp(item['dt'])
                day = dt.date()
                
                if day not in forecasts_by_day:
                    forecasts_by_day[day] = []
                
                forecasts_by_day[day].append({
                    'timestamp': item['dt'],
                    'datetime': dt.strftime('%Y-%m-%d %H:%M:%S'),
                    'temperature': {
                        'current': item['main']['temp'],
                        'feels_like': item['main']['feels_like'],
                        'min': item['main']['temp_min'],
                        'max': item['main']['temp_max']
                    },
                    'weather': {
                        'main': item['weather'][0]['main'],
                        'description': item['weather'][0]['description'],
                        'icon': item['weather'][0]['icon']
                    },
                    'wind': {
                        'speed': item['wind']['speed'],
                        'deg': item['wind'].get('deg'),
                        'direction': self._get_wind_direction(item['wind'].get('deg'))
                    },
                    'humidity': item['main']['humidity'],
                    'pressure': item['main']['pressure'],
                    'clouds': item['clouds']['all'],
                    'precipitation': item.get('pop', 0)  # Вероятность осадков
                })
            
            # Формируем прогноз по дням
            for day, items in sorted(forecasts_by_day.items()):
                # Вычисляем средние значения
                temp_min = min(item['temperature']['min'] for item in items)
                temp_max = max(item['temperature']['max'] for item in items)
                
                # Определяем основное состояние погоды (наиболее частое)
                weather_counts = {}
                for item in items:
                    weather = item['weather']['main']
                    weather_counts[weather] = weather_counts.get(weather, 0) + 1
                
                main_weather = max(weather_counts.items(), key=lambda x: x[1])[0]
                
                # Формируем прогноз на день
                day_forecast = {
                    'date': day.strftime('%Y-%m-%d'),
                    'day_of_week': day.strftime('%A'),
                    'temperature': {
                        'min': temp_min,
                        'max': temp_max
                    },
                    'weather': {
                        'main': main_weather,
                        'description': next(item['weather']['description'] for item in items if item['weather']['main'] == main_weather),
                        'icon': next(item['weather']['icon'] for item in items if item['weather']['main'] == main_weather)
                    },
                    'hours': items
                }
                
                forecast_data['forecast'].append(day_forecast)
            
            return forecast_data
        
        except KeyError as e:
            logger.error(f"Ошибка в структуре ответа: {str(e)}")
            return None
    
    def _get_wind_direction(self, degrees):
        """Преобразование градусов в направление ветра"""
        if degrees is None:
            return None
        
        directions = [
            "С", "ССВ", "СВ", "ВСВ", "В", "ВЮВ", "ЮВ", "ЮЮВ",
            "Ю", "ЮЮЗ", "ЮЗ", "ЗЮЗ", "З", "ЗСЗ", "СЗ", "ССЗ"
        ]
        
        index = round(degrees / 22.5) % 16
        return directions[index]

class WeatherStackProvider(WeatherProvider):
    """Провайдер погоды на основе WeatherStack API"""
    
    def __init__(self, api_key):
        """Инициализация провайдера"""
        super().__init__(api_key)
        self.base_url = "http://api.weatherstack.com"
        self.name = "WeatherStack"
    
    def get_current_weather(self, city):
        """Получение текущей погоды для города"""
        url = f"{self.base_url}/current"
        params = {
            'access_key': self.api_key,
            'query': city,
            'units': 'm'  # Метрическая система
        }
        
        response = self._make_request(url, params)
        if not response or 'current' not in response:
            return None
        
        try:
            # Преобразуем ответ в единый формат
            weather_data = {
                'provider': self.name,
                'city': response['location']['name'],
                'country': response['location']['country'],
                'timestamp': int(datetime.strptime(
                    response['location']['localtime'], 
                    '%Y-%m-%d %H:%M'
                ).timestamp()),
                'datetime': response['location']['localtime'],
                'temperature': {
                    'current': response['current']['temperature'],
                    'feels_like': response['current']['feelslike'],
                    'min': None,  # WeatherStack не предоставляет min/max
                    'max': None
                },
                'weather': {
                    'main': response['current']['weather_descriptions'][0],
                    'description': response['current']['weather_descriptions'][0],
                    'icon': None  # WeatherStack использует другую систему иконок
                },
                'wind': {
                    'speed': response['current']['wind_speed'],
                    'deg': None,  # WeatherStack использует текстовое направление
                    'direction': response['current']['wind_dir']
                },
                'humidity': response['current']['humidity'],
                'pressure': response['current']['pressure'],
                'visibility': response['current']['visibility'],
                'clouds': response['current']['cloudcover'],
                'sunrise': None,  # WeatherStack не предоставляет времени восхода/заката
                'sunset': None
            }
            
            return weather_data
        
        except KeyError as e:
            logger.error(f"Ошибка в структуре ответа: {str(e)}")
            return None
    
    def get_forecast(self, city, days=5):
        """
        Получение прогноза погоды для города
        
        Примечание: Бесплатный план WeatherStack не включает прогноз погоды,
        поэтому этот метод возвращает None.
        """
        logger.warning("Прогноз погоды не поддерживается в бесплатном плане WeatherStack")
        return None

class WeatherClient:
    """Клиент для работы с API погоды"""
    
    def __init__(self, providers=None, cache_ttl=3600):
        """
        Инициализация клиента
        
        Args:
            providers: Список провайдеров погоды
            cache_ttl: Время жизни кэша в секундах (по умолчанию: 1 час)
        """
        self.providers = providers or []
        self.cache = WeatherCache(ttl=cache_ttl)
    
    def add_provider(self, provider):
        """Добавление провайдера погоды"""
        if provider not in self.providers:
            self.providers.append(provider)
    
    def get_current_weather(self, city):
        """
        Получение текущей погоды для города
        
        Args:
            city: Название города
        
        Returns:
            Словарь с данными о погоде или None в случае ошибки
        """
        # Проверяем кэш
        cache_key = f"current_{city.lower()}"
        cached_data = self.cache.get(cache_key)
        
        if cached_data:
            logger.info(f"Данные о текущей погоде для {city} получены из кэша")
            return cached_data
        
        # Пробуем получить данные от провайдеров
        for provider in self.providers:
            logger.info(f"Запрос текущей погоды для {city} от провайдера {provider.name}")
            
            weather_data = provider.get_current_weather(city)
            if weather_data:
                # Сохраняем в кэш
                self.cache.set(cache_key, weather_data)
                return weather_data
        
        logger.error(f"Не удалось получить данные о текущей погоде для {city}")
        return None
    
    def get_forecast(self, city, days=5):
        """
        Получение прогноза погоды для города
        
        Args:
            city: Название города
            days: Количество дней (по умолчанию: 5)
        
        Returns:
            Словарь с данными о прогнозе или None в случае ошибки
        """
        # Проверяем кэш
        cache_key = f"forecast_{city.lower()}_{days}"
        cached_data = self.cache.get(cache_key)
        
        if cached_data:
            logger.info(f"Данные о прогнозе погоды для {city} на {days} дней получены из кэша")
            return cached_data
        
        # Пробуем получить данные от провайдеров
        for provider in self.providers:
            logger.info(f"Запрос прогноза погоды для {city} на {days} дней от провайдера {provider.name}")
            
            forecast_data = provider.get_forecast(city, days)
            if forecast_data:
                # Сохраняем в кэш
                self.cache.set(cache_key, forecast_data)
                return forecast_data
        
        logger.error(f"Не удалось получить данные о прогнозе погоды для {city} на {days} дней")
        return None
    
    def clear_cache(self):
        """Очистка кэша"""
        self.cache.clear()
    
    def format_current_weather(self, weather_data, format_type='text'):
        """
        Форматирование данных о текущей погоде
        
        Args:
            weather_data: Данные о погоде
            format_type: Тип форматирования ('text', 'json', 'html')
        
        Returns:
            Отформатированные данные
        """
        if not weather_data:
            return "Нет данных о погоде"
        
        if format_type == 'json':
            return json.dumps(weather_data, ensure_ascii=False, indent=2)
        
        elif format_type == 'html':
            html_output = f"""
            <!DOCTYPE html>
            <html>
            <head>
                <meta charset="UTF-8">
                <title>Погода в {html.escape(weather_data['city'])}</title>
                <style>
                    body {{ font-family: Arial, sans-serif; margin: 0; padding: 20px; }}
                    .weather-card {{ max-width: 600px; margin: 0 auto; padding: 20px; border-radius: 10px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }}
                    .header {{ text-align: center; margin-bottom: 20px; }}
                    .main-info {{ display: flex; justify-content: space-between; margin-bottom: 20px; }}
                    .temperature {{ font-size: 48px; font-weight: bold; }}
                    .details {{ display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }}
                    .detail-item {{ display: flex; align-items: center; }}
                    .detail-label {{ font-weight: bold; margin-right: 10px; }}
                </style>
            </head>
            <body>
                <div class="weather-card">
                    <div class="header">
                        <h1>Погода в {html.escape(weather_data['city'])}, {html.escape(weather_data['country'])}</h1>
                        <p>{html.escape(weather_data['datetime'])}</p>
                        <p>{html.escape(weather_data['weather']['description'])}</p>
                    </div>
                    
                    <div class="main-info">
                        <div class="temperature">{weather_data['temperature']['current']}°C</div>
                        <div>
                            <p>Ощущается как: {weather_data['temperature']['feels_like']}°C</p>
                        </div>
                    </div>
                    
                    <div class="details">
                        <div class="detail-item">
                            <span class="detail-label">Влажность:</span>
                            <span>{weather_data['humidity']}%</span>
                        </div>
                        <div class="detail-item">
                            <span class="detail-label">Давление:</span>
                            <span>{weather_data['pressure']} гПа</span>
                        </div>
                        <div class="detail-item">
                            <span class="detail-label">Ветер:</span>
                            <span>{weather_data['wind']['speed']} м/с, {weather_data['wind']['direction']}</span>
                        </div>
                        <div class="detail-item">
                            <span class="detail-label">Облачность:</span>
                            <span>{weather_data['clouds']}%</span>
                        </div>
                    </div>
                    
                    <div style="margin-top: 20px; text-align: center; font-size: 0.8em; color: #888;">
                        Данные предоставлены: {html.escape(weather_data['provider'])}
                    </div>
                </div>
            </body>
            </html>
            """
            return html_output
        
        else:  # format_type == 'text'
            text_output = [
                f"Погода в {weather_data['city']}, {weather_data['country']}",
                f"Дата и время: {weather_data['datetime']}",
                f"Температура: {weather_data['temperature']['current']}°C (ощущается как {weather_data['temperature']['feels_like']}°C)",
                f"Погодные условия: {weather_data['weather']['description']}",
                f"Ветер: {weather_data['wind']['speed']} м/с, направление: {weather_data['wind']['direction']}",
                f"Влажность: {weather_data['humidity']}%",
                f"Давление: {weather_data['pressure']} гПа",
                f"Облачность: {weather_data['clouds']}%"
            ]
            
            if weather_data['visibility']:
                text_output.append(f"Видимость: {weather_data['visibility']} м")
            
            if weather_data['sunrise'] and weather_data['sunset']:
                sunrise = datetime.fromtimestamp(weather_data['sunrise']).strftime('%H:%M')
                sunset = datetime.fromtimestamp(weather_data['sunset']).strftime('%H:%M')
                text_output.append(f"Восход солнца: {sunrise}, закат: {sunset}")
            
            text_output.append(f"\nДанные предоставлены: {weather_data['provider']}")
            
            return "\n".join(text_output)
    
    def format_forecast(self, forecast_data, format_type='text'):
        """
        Форматирование данных о прогнозе погоды
        
        Args:
            forecast_data: Данные о прогнозе
            format_type: Тип форматирования ('text', 'json', 'html')
        
        Returns:
            Отформатированные данные
        """
        if not forecast_data:
            return "Нет данных о прогнозе погоды"
        
        if format_type == 'json':
            return json.dumps(forecast_data, ensure_ascii=False, indent=2)
        
        elif format_type == 'html':
            html_output = f"""
            <!DOCTYPE html>
            <html>
            <head>
                <meta charset="UTF-8">
                <title>Прогноз погоды для {html.escape(forecast_data['city'])}</title>
                <style>
                    body {{ font-family: Arial, sans-serif; margin: 0; padding: 20px; }}
                    .forecast-card {{ max-width: 800px; margin: 0 auto; padding: 20px; border-radius: 10px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }}
                    .header {{ text-align: center; margin-bottom: 20px; }}
                    .forecast-days {{ display: flex; flex-wrap: wrap; gap: 20px; justify-content: center; }}
                    .day-card {{ padding: 15px; border-radius: 8px; box-shadow: 0 0 5px rgba(0,0,0,0.1); min-width: 150px; }}
                    .day-header {{ text-align: center; margin-bottom: 10px; }}
                    .day-details {{ font-size: 0.9em; }}
                    .detail-item {{ margin-bottom: 5px; }}
                </style>
            </head>
            <body>
                <div class="forecast-card">
                    <div class="header">
                        <h1>Прогноз погоды для {html.escape(forecast_data['city'])}, {html.escape(forecast_data['country'])}</h1>
                    </div>
                    
                    <div class="forecast-days">
            """
            
            for day in forecast_data['forecast']:
                html_output += f"""
                        <div class="day-card">
                            <div class="day-header">
                                <h3>{html.escape(day['day_of_week'])}</h3>
                                <p>{html.escape(day['date'])}</p>
                                <p>{html.escape(day['weather']['description'])}</p>
                            </div>
                            <div class="day-details">
                                <div class="detail-item">
                                    <span>Температура: {day['temperature']['min']}°C - {day['temperature']['max']}°C</span>
                                </div>
                            </div>
                        </div>
                """
            
            html_output += f"""
                    </div>
                    
                    <div style="margin-top: 20px; text-align: center; font-size: 0.8em; color: #888;">
                        Данные предоставлены: {html.escape(forecast_data['provider'])}
                    </div>
                </div>
            </body>
            </html>
            """
            
            return html_output
        
        else:  # format_type == 'text'
            text_output = [
                f"Прогноз погоды для {forecast_data['city']}, {forecast_data['country']}",
                "=" * 50
            ]
            
            for day in forecast_data['forecast']:
                text_output.append(f"\n{day['day_of_week']}, {day['date']}")
                text_output.append(f"Погода: {day['weather']['description']}")
                text_output.append(f"Температура: {day['temperature']['min']}°C - {day['temperature']['max']}°C")
                text_output.append("-" * 30)
            
            text_output.append(f"\nДанные предоставлены: {forecast_data['provider']}")
            
            return "\n".join(text_output)

def main():
    """Основная функция"""
    # Создаем парсер аргументов командной строки
    parser = argparse.ArgumentParser(description='Клиент для получения данных о погоде')
    
    # Добавляем аргументы
    parser.add_argument('city', help='Город для получения погоды')
    parser.add_argument('-f', '--forecast', action='store_true', help='Получить прогноз погоды')
    parser.add_argument('-d', '--days', type=int, default=5, help='Количество дней для прогноза (по умолчанию: 5)')
    parser.add_argument('-o', '--output', choices=['text', 'json', 'html'], default='text', help='Формат вывода (по умолчанию: text)')
    parser.add_argument('-c', '--clear-cache', action='store_true', help='Очистить кэш перед запросом')
    parser.add_argument('--owm-key', help='API-ключ OpenWeatherMap')
    parser.add_argument('--ws-key', help='API-ключ WeatherStack')
    
    # Парсим аргументы
    args = parser.parse_args()
    
    # Получаем API-ключи
    owm_key = args.owm_key or os.environ.get('OWM_API_KEY')
    ws_key = args.ws_key or os.environ.get('WS_API_KEY')
    
    if not owm_key and not ws_key:
        print("Ошибка: Необходимо указать хотя бы один API-ключ")
        print("Укажите ключ OpenWeatherMap с помощью аргумента --owm-key или переменной окружения OWM_API_KEY")
        print("Укажите ключ WeatherStack с помощью аргумента --ws-key или переменной окружения WS_API_KEY")
        return
    
    # Создаем клиент
    client = WeatherClient()
    
    # Добавляем провайдеры
    if owm_key:
        client.add_provider(OpenWeatherMapProvider(owm_key))
    if ws_key:
        client.add_provider(WeatherStackProvider(ws_key))
    
    # Очищаем кэш, если нужно
    if args.clear_cache:
        client.clear_cache()
    
    # Получаем данные о погоде
    if args.forecast:
        # Получаем прогноз
        data = client.get_forecast(args.city, args.days)
        if data:
            formatted_data = client.format_forecast(data, args.output)
            print(formatted_data)
            
            # Сохраняем в файл, если формат HTML
            if args.output == 'html':
                with open(f"{args.city}_forecast.html", 'w', encoding='utf-8') as f:
                    f.write(formatted_data)
                print(f"Результат сохранен в файл {args.city}_forecast.html")
        else:
            print(f"Не удалось получить прогноз погоды для {args.city}")
    else:
        # Получаем текущую погоду
        data = client.get_current_weather(args.city)
        if data:
            formatted_data = client.format_current_weather(data, args.output)
            print(formatted_data)
            
            # Сохраняем в файл, если формат HTML
            if args.output == 'html':
                with open(f"{args.city}_weather.html", 'w', encoding='utf-8') as f:
                    f.write(formatted_data)
                print(f"Результат сохранен в файл {args.city}_weather.html")
        else:
            print(f"Не удалось получить текущую погоду для {args.city}")

if __name__ == '__main__':
    main()
```

## Идеи для улучшения

### Для утилиты проверки доступности веб-сайтов:

1. **Расширенные проверки**:
   - Добавьте проверку SSL-сертификатов (срок действия, алгоритм шифрования, проблемы с цепочкой)
   - Реализуйте анализ времени загрузки различных ресурсов (HTML, CSS, JS, изображения)
   - Добавьте проверку доступности по IPv6

2. **Визуализация**:
   - Создайте веб-интерфейс для отображения результатов
   - Добавьте графики изменения времени отклика с течением времени
   - Реализуйте интерактивную карту для визуализации географического расположения сайтов и их состояния

3. **Мониторинг**:
   - Добавьте периодические проверки с настраиваемым интервалом
   - Реализуйте систему оповещений (по email, SMS, в мессенджеры) при недоступности сайтов
   - Создайте API для интеграции с другими системами мониторинга

### Для чат-сервера и клиента:

1. **Расширение функциональности**:
   - Добавьте поддержку комнат (каналов)
   - Реализуйте передачу файлов
   - Добавьте хранение истории сообщений

2. **Улучшение интерфейса**:
   - Создайте графический интерфейс для клиента с помощью tkinter, PyQt или веб-интерфейса
   - Добавьте поддержку эмодзи и форматирования текста
   - Реализуйте отображение статуса пользователей (онлайн, не в сети, печатает...)

3. **Безопасность**:
   - Улучшите систему аутентификации (логин/пароль, двухфакторная аутентификация)
   - Реализуйте обмен ключами по алгоритму Диффи-Хеллмана
   - Добавьте поддержку сквозного шифрования для приватных сообщений

### Для клиента API погоды:

1. **Расширение источников данных**:
   - Добавьте поддержку дополнительных погодных API (AccuWeather, Weather.gov, Яндекс.Погода)
   - Реализуйте агрегацию данных из нескольких источников для повышения точности
   - Добавьте поддержку исторических данных о погоде

2. **Персонализация**:
   - Добавьте сохранение избранных городов
   - Реализуйте настраиваемые оповещения (о штормовых предупреждениях, резких изменениях температуры)
   - Создайте профили пользователей с предпочтениями по отображению данных

3. **Визуализация и геолокация**:
   - Добавьте интерактивные карты погоды с помощью библиотек Folium или Plotly
   - Реализуйте автоматическое определение местоположения пользователя
   - Добавьте анимированные радарные карты осадков

## Заключение

В этом уроке мы изучили основы сетевого программирования в Python, начиная с низкоуровневых сокетов и заканчивая высокоуровневыми библиотеками для работы с HTTP и асинхронным программированием. Мы рассмотрели:

1. **Основы сетевого программирования** и модель OSI
2. **Низкоуровневое программирование с сокетами**:
   - Создание TCP и UDP клиентов и серверов
   - Обработка нескольких подключений с помощью потоков
   - Работа с DNS и другими сетевыми сервисами

3. **Работу с HTTP-запросами**:
   - Использование библиотеки `requests` для отправки запросов
   - Обработка различных типов ответов (JSON, HTML, XML)
   - Работа с сессиями, заголовками и cookies

4. **Создание API-клиентов**:
   - Разработка удобных абстракций для работы с веб-сервисами
   - Обработка ошибок и повторные попытки
   - Кэширование результатов

5. **Асинхронное сетевое программирование**:
   - Основы работы с `asyncio`
   - Использование `aiohttp` для асинхронных HTTP-запросов
   - Создание эффективных асинхронных серверов и клиентов

Сетевое программирование — это обширная область, которая находит применение во множестве задач: от веб-скрапинга и работы с API до разработки распределенных систем и чат-приложений. Понимание основ сетевого взаимодействия и умение использовать соответствующие инструменты Python позволяет создавать эффективные и надежные сетевые приложения.

В следующих темах мы рассмотрим работу с базами данных, которые часто используются вместе с сетевыми приложениями для хранения и обработки данных.

```

### Установка aiohttp

```
pip install aiohttp
```

### Простой асинхронный HTTP-клиент

```python
import asyncio
import aiohttp
import time

async def fetch(session, url):
    """Асинхронное получение содержимого URL"""
    async with session.get(url) as response:
        return await response.text()

async def fetch_all(urls):
    """Асинхронное получение содержимого нескольких URL"""
    async with aiohttp.ClientSession() as session:
        tasks = []
        for url in urls:
            task = asyncio.create_task(fetch(session, url))
            tasks.append(task)
        
        results = await asyncio.gather(*tasks)
        return results

async def main():
    """Основная асинхронная функция"""
    # Список URL для запросов
    urls = [
        'http://python.org',
        'http://google.com',
        'http://github.com',
        'http://stackoverflow.com',
        'http://httpbin.org/delay/3'  # URL с задержкой в 3 секунды
    ]
    
    # Измерение времени выполнения
    start_time = time.time()
    
    # Получаем содержимое всех URL
    results = await fetch_all(urls)
    
    # Вывод информации о результатах
    for i, (url, html) in enumerate(zip(urls, results)):
        print(f"{i+1}. {url}: получено {len(html)} символов")
    
    elapsed = time.time() - start_time
    print(f"Всего затрачено времени: {elapsed:.2f} секунд")

if __name__ == '__main__':
    asyncio.run(main())
```

### Асинхронные HTTP-запросы с параметрами и заголовками

```python
import asyncio
import aiohttp
import json

async def fetch_json(session, url, params=None, headers=None):
    """Асинхронное получение JSON-данных"""
    try:
        async with session.get(url, params=params, headers=headers) as response:
            response.raise_for_status()
            return await response.json()
    except (aiohttp.ClientError, json.JSONDecodeError) as e:
        print(f"Ошибка при получении {url}: {str(e)}")
        return None

async def post_json(session, url, data, headers=None):
    """Асинхронная отправка JSON-данных"""
    try:
        async with session.post(url, json=data, headers=headers) as response:
            response.raise_for_status()
            return await response.json()
    except (aiohttp.ClientError, json.JSONDecodeError) as e:
        print(f"Ошибка при отправке данных на {url}: {str(e)}")
        return None

async def main():
    """Основная асинхронная функция"""
    # Заголовки запросов
    headers = {
        'User-Agent': 'Python aiohttp Client',
        'Accept': 'application/json'
    }
    
    async with aiohttp.ClientSession() as session:
        # GET-запрос с параметрами
        params = {'q': 'python', 'lang': 'ru'}
        get_result = await fetch_json(session, 'https://httpbin.org/get', params=params, headers=headers)
        if get_result:
            print("GET-запрос:")
            print(f"URL с параметрами: {get_result['url']}")
            print(f"Заголовки: {get_result['headers']}")
            print(f"Аргументы: {get_result['args']}")
        
        # POST-запрос с JSON-данными
        post_data = {'name': 'John', 'age': 30, 'city': 'New York'}
        post_result = await post_json(session, 'https://httpbin.org/post', post_data, headers=headers)
        if post_result:
            print("\nPOST-запрос:")
            print(f"Отправленные данные: {post_result['json']}")
            print(f"Заголовки: {post_result['headers']}")

if __name__ == '__main__':
    asyncio.run(main())
```

### Асинхронная загрузка нескольких файлов

```python
import asyncio
import aiohttp
import aiofiles
import os
import time

async def download_file(session, url, save_path):
    """Асинхронная загрузка файла"""
    try:
        # Создаем директорию, если она не существует
        os.makedirs(os.path.dirname(save_path), exist_ok=True)
        
        # Получаем файл
        async with session.get(url) as response:
            response.raise_for_status()
            
            # Сохраняем файл
            async with aiofiles.open(save_path, 'wb') as file:
                while True:
                    chunk = await response.content.read(8192)
                    if not chunk:
                        break
                    await file.write(chunk)
        
        return save_path
    except Exception as e:
        print(f"Ошибка при загрузке {url}: {str(e)}")
        return None

async def download_all_files(urls, save_dir):
    """Асинхронная загрузка нескольких файлов"""
    async with aiohttp.ClientSession() as session:
        tasks = []
        for i, url in enumerate(urls):
            # Генерируем имя файла из URL или используем порядковый номер
            filename = os.path.basename(url.split('?')[0]) or f"file_{i+1}.dat"
            save_path = os.path.join(save_dir, filename)
            
            # Создаем задачу
            task = asyncio.create_task(download_file(session, url, save_path))
            tasks.append((url, task))
        
        # Дожидаемся завершения всех задач
        results = []
        for url, task in tasks:
            try:
                save_path = await task
                results.append((url, save_path))
            except Exception as e:
                print(f"Задача для {url} завершилась с ошибкой: {str(e)}")
        
        return results

async def main():
    """Основная асинхронная функция"""
    # Список URL для загрузки
    urls = [
        'https://www.python.org/static/img/python-logo.png',
        'https://www.python.org/static/img/python-logo-only.png',
        'https://www.python.org/static/community_logos/python-powered-h-140x182.png',
        'https://www.python.org/static/community_logos/python-powered-h-50x65.png',
        'https://www.python.org/static/community_logos/python-powered-h.png',
    ]
    
    # Директория для сохранения файлов
    save_dir = 'downloads'
    
    # Измерение времени выполнения
    start_time = time.time()
    
    # Загружаем файлы
    results = await download_all_files(urls, save_dir)
    
    # Вывод информации о результатах
    print(f"Загружено {len(results)} файлов:")
    for url, save_path in results:
        if save_path:
            size = os.path.getsize(save_path)
            print(f"  {url} -> {save_path} ({size} байт)")
    
    elapsed = time.time() - start_time
    print(f"Всего затрачено времени: {elapsed:.2f} секунд")

if __name__ == '__main__':
    # Устанавливаем aiofiles, если еще не установлен
    # pip install aiofiles
    
    asyncio.run(main())
```

### Асинхронный API-клиент

```python
import asyncio
import aiohttp
import json
from typing import Dict, List, Optional, Any

class AsyncGitHubAPI:
    """Асинхронный клиент для GitHub API"""
    
    def __init__(self, token: Optional[str] = None):
        """Инициализация клиента"""
        self.base_url = "https://api.github.com"
        self.headers = {
            'Accept': 'application/vnd.github.v3+json',
            'User-Agent': 'Python Async GitHub API Client'
        }
        
        # Если предоставлен токен, добавляем его в заголовки
        if token:
            self.headers['Authorization'] = f'token {token}'
        
        self.session = None
    
    async def __aenter__(self):
        """Вход в контекстный менеджер"""
        self.session = aiohttp.ClientSession(headers=self.headers)
        return self
    
    async def __aexit__(self, exc_type, exc_val, exc_tb):
        """Выход из контекстного менеджера"""
        if self.session:
            await self.session.close()
    
    async def get_user(self, username: str) -> Optional[Dict]:
        """Получение информации о пользователе"""
        url = f"{self.base_url}/users/{username}"
        
        try:
            async with self.session.get(url) as response:
                response.raise_for_status()
                return await response.json()
        except aiohttp.ClientError as e:
            print(f"Ошибка при получении информации о пользователе: {str(e)}")
            return None
    
    async def get_repos(self, username: str, sort: str = 'updated', direction: str = 'desc') -> Optional[List[Dict]]:
        """Получение репозиториев пользователя"""
        url = f"{self.base_url}/users/{username}/repos"
        params = {
            'sort': sort,
            'direction': direction
        }
        
        try:
            async with self.session.get(url, params=params) as response:
                response.raise_for_status()
                return await response.json()
        except aiohttp.ClientError as e:
            print(f"Ошибка при получении репозиториев: {str(e)}")
            return None
    
    async def search_repositories(self, query: str, sort: str = 'stars', order: str = 'desc', per_page: int = 10) -> Optional[Dict]:
        """Поиск репозиториев"""
        url = f"{self.base_url}/search/repositories"
        params = {
            'q': query,
            'sort': sort,
            'order': order,
            'per_page': per_page
        }
        
        try:
            async with self.session.get(url, params=params) as response:
                response.raise_for_status()
                return await response.json()
        except aiohttp.ClientError as e:
            print(f"Ошибка при поиске репозиториев: {str(e)}")
            return None
    
    async def get_user_and_repos(self, username: str) -> Dict[str, Any]:
        """Получение информации о пользователе и его репозиториях"""
        # Создаем две задачи и выполняем их параллельно
        user_task = asyncio.create_task(self.get_user(username))
        repos_task = asyncio.create_task(self.get_repos(username))
        
        # Ждем завершения обеих задач
        user = await user_task
        repos = await repos_task
        
        return {
            'user': user,
            'repos': repos
        }

async def main():
    """Основная асинхронная функция"""
    # Используем API-клиент с контекстным менеджером
    async with AsyncGitHubAPI() as github:
        # Получаем информацию о пользователе и его репозиториях
        result = await github.get_user_and_repos('octocat')
        
        user = result['user']
        repos = result['repos']
        
        if user:
            print(f"Информация о пользователе {user['login']}:")
            print(f"  Имя: {user.get('name', 'Не указано')}")
            print(f"  Компания: {user.get('company', 'Не указано')}")
            print(f"  Местоположение: {user.get('location', 'Не указано')}")
            print(f"  Публичные репозитории: {user['public_repos']}")
            print(f"  Подписчики: {user['followers']}")
        
        if repos:
            print("\nРепозитории пользователя:")
            for repo in repos[:5]:  # Показываем только первые 5
                print(f"  - {repo['name']}: {repo['description'] or 'Без описания'}")
                print(f"    Язык: {repo.get('language', 'Не указан')}, Звезды: {repo['stargazers_count']}")
        
        # Поиск репозиториев
        search_results = await github.search_repositories('language:python stars:>1000', per_page=5)
        
        if search_results:
            print("\nПопулярные Python-репозитории:")
            for repo in search_results['items']:
                print(f"  - {repo['full_name']}: {repo['description']}")
                print(f"    Звезды: {repo['stargazers_count']}, Форки: {repo['forks_count']}")

if __name__ == '__main__':
    asyncio.run(main())
```

### Простой асинхронный HTTP-сервер с aiohttp

```python
import asyncio
from aiohttp import web
import json
import datetime

# Обработчики запросов
async def handle_index(request):
    """Обработка запроса к корневому пути"""
    return web.Response(text="Welcome to the Async API Server", content_type="text/plain")

async def handle_time(request):
    """Возвращает текущее время"""
    current_time = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    data = {"time": current_time}
    return web.json_response(data)

async def handle_echo(request):
    """Эхо-сервис, который возвращает полученные данные"""
    # Получаем параметры запроса
    params = dict(request.query)
    
    # Если есть тело запроса, парсим его
    try:
        if request.body_exists:
            if request.content_type == 'application/json':
                body = await request.json()
            else:
                body = await request.text()
        else:
            body = None
    except Exception as e:
        body = f"Error parsing body: {str(e)}"
    
    # Формируем ответ
    data = {
        "method": request.method,
        "path": request.path,
        "params": params,
        "headers": dict(request.headers),
        "body": body
    }
    
    return web.json_response(data)

async def handle_delay(request):
    """Обработчик с задержкой"""
    # Получаем время задержки из URL или используем значение по умолчанию
    try:
        delay = float(request.match_info.get('seconds', 1))
        delay = min(delay, 10)  # Ограничиваем максимальное время задержки
    except ValueError:
        delay = 1
    
    # Имитируем работу с задержкой
    await asyncio.sleep(delay)
    
    data = {
        "delay": delay,
        "time": datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    }
    
    return web.json_response(data)

async def handle_status(request):
    """Возвращает указанный HTTP-статус"""
    try:
        status = int(request.match_info.get('status', 200))
    except ValueError:
        status = 200
    
    data = {
        "status": status,
        "message": f"Returned status code: {status}"
    }
    
    return web.json_response(data, status=status)

# Создание приложения
def create_app():
    """Создание и настройка веб-приложения"""
    app = web.Application()
    
    # Настраиваем маршруты
    app.add_routes([
        web.get('/', handle_index),
        web.get('/time', handle_time),
        web.route('*', '/echo', handle_echo),  # Любой HTTP-метод
        web.get('/delay/{seconds}', handle_delay),
        web.get('/status/{status}', handle_status),
    ])
    
    return app

# Запуск сервера
if __name__ == '__main__':
    app = create_app()
    print("Сервер запускается на http://127.0.0.1:8080")
    web.run_app(app, host='127.0.0.1', port=8080)
```

### Асинхронный веб-краулер

Комбинируя асинхронное программирование и HTTP-запросы, можно создать эффективный веб-краулер:

```python
import asyncio
import aiohttp
from bs4 import BeautifulSoup
import re
from urllib.parse import urljoin, urlparse
import time
import logging

# Настройка логирования
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

class AsyncWebCrawler:
    """Асинхронный веб-краулер"""
    
    def __init__(self, start_url, max_depth=2, max_urls=100, same_domain=True):
        """Инициализация краулера"""
        self.start_url = start_url
        self.max_depth = max_depth
        self.max_urls = max_urls
        self.same_domain = same_domain
        
        # Извлекаем домен начального URL
        self.domain = urlparse(start_url).netloc
        
        # Множество для отслеживания посещенных URL
        self.visited_urls = set()
        
        # Семафор для ограничения количества одновременных запросов
        self.semaphore = asyncio.Semaphore(10)
        
        # Словарь для хранения результатов
        self.results = {}
    
    async def crawl(self):
        """Запуск процесса обхода"""
        start_time = time.time()
        
        # Создаем сессию для HTTP-запросов
        async with aiohttp.ClientSession() as session:
            # Начинаем с начального URL
            await self._crawl_url(session, self.start_url, depth=0)
        
        elapsed = time.time() - start_time
        logger.info(f"Обход завершен. Посещено {len(self.visited_urls)} URL за {elapsed:.2f} секунд")
        
        return self.results
    
    async def _crawl_url(self, session, url, depth):
        """Рекурсивный обход URL"""
        # Проверяем, не превышена ли максимальная глубина
        if depth > self.max_depth:
            return
        
        # Проверяем, не превышено ли максимальное количество URL
        if len(self.visited_urls) >= self.max_urls:
            return
        
        # Проверяем, не посещали ли мы уже этот URL
        if url in self.visited_urls:
            return
        
        # Добавляем URL в множество посещенных
        self.visited_urls.add(url)
        
        logger.info(f"Обход URL: {url} (глубина: {depth})")
        
        # Ограничиваем количество одновременных запросов
        async with self.semaphore:
            # Получаем содержимое страницы
            html = await self._fetch_url(session, url)
        
        if html is None:
            return
        
        # Извлекаем информацию из HTML
        title, links = self._parse_html(html, url)
        
        # Сохраняем результаты
        self.results[url] = {
            'title': title,
            'depth': depth,
            'links': links
        }
        
        # Рекурсивно обходим найденные ссылки
        tasks = []
        for link in links:
            # Создаем задачу для каждой ссылки
            task = asyncio.create_task(self._crawl_url(session, link, depth + 1))
            tasks.append(task)
        
        # Дожидаемся завершения всех задач
        if tasks:
            await asyncio.gather(*tasks)
    
    async def _fetch_url(self, session, url):
        """Получение содержимого URL"""
        try:
            async with session.get(url, timeout=10) as response:
                if response.status == 200:
                    if 'text/html' in response.headers.get('Content-Type', ''):
                        return await response.text()
                else:
                    logger.warning(f"Статус {response.status} для {url}")
        except Exception as e:
            logger.error(f"Ошибка при получении {url}: {str(e)}")
        
        return None
    
    def _parse_html(self, html, base_url):
        """Извлечение информации из HTML"""
        soup = BeautifulSoup(html, 'html.parser')
        
        # Извлекаем заголовок страницы
        title = soup.title.string if soup.title else "No Title"
        
        # Извлекаем ссылки
        links = []
        for a_tag in soup.find_all('a', href=True):
            href = a_tag['href']
            
            # Преобразуем относительные URL в абсолютные
            absolute_url = urljoin(base_url, href)
            
            # Проверяем, должны ли мы ограничиться тем же доменом
            if self.same_domain and urlparse(absolute_url).netloc != self.domain:
                continue
            
            # Фильтруем URL-фрагменты и некоторые расширения файлов
            if '#' in absolute_url:
                absolute_url = absolute_url.split('#')[0]
            
            if absolute_url.endswith(('.jpg', '.jpeg', '.png', '.gif', '.pdf', '.zip')):
                continue
            
            if absolute_url not in self.visited_urls and absolute_url not in links:
                links.append(absolute_url)
        
        return title, links
    
    def generate_report(self):
        """Генерация отчета"""
        if not self.results:
            return "Нет данных для отчета"
        
        report = []
        report.append("ОТЧЕТ ОБ ОБХОДЕ САЙТА")
        report.append("=" * 50)
        report.append(f"Начальный URL: {self.start_url}")
        report.append(f"Максимальная глубина: {self.max_depth}")
        report.append(f"Всего посещено URL: {len(self.visited_urls)}")
        report.append("")
        
        # Группируем URL по глубине
        urls_by_depth = {}
        for url, data in self.results.items():
            depth = data['depth']
            if depth not in urls_by_depth:
                urls_by_depth[depth] = []
            urls_by_depth[depth].append((url, data['title']))
        
        # Выводим URL по уровням
        for depth in sorted(urls_by_depth.keys()):
            report.append(f"Уровень {depth}:")
            for url, title in urls_by_depth[depth]:
                report.append(f"  - {title}: {url}")
            report.append("")
        
        return "\n".join(report)

# Пример использования
async def main():
    """Основная асинхронная функция"""
    # Создаем краулер
    crawler = AsyncWebCrawler(
        start_url="https://www.python.org",
        max_depth=1,
        max_urls=20,
        same_domain=True
    )
    
    # Запускаем обход
    await crawler.crawl()
    
    # Генерируем отчет
    report = crawler.generate_report()
    print(report)

if __name__ == '__main__':
    asyncio.run(main())
```
