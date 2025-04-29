# Алгоритмы ограничения скорости (Rate Limiting)

Ограничение скорости (Rate limiting) — это фундаментальная техника в веб-разработке, которая позволяет контролировать интенсивность запросов, поступающих к API или веб-сервису. Эта стратегия особенно важна для защиты серверных ресурсов от перегрузки, предотвращения DoS-атак и обеспечения справедливого распределения ресурсов между всеми клиентами.

## Зачем нужно ограничение скорости?

Rate limiting необходим по нескольким причинам:

1. **Защита от DoS-атак** — предотвращает злонамеренное или случайное истощение серверных ресурсов
2. **Контроль качества обслуживания** — обеспечивает справедливый доступ к API для всех пользователей
3. **Соблюдение SLA (соглашений об уровне обслуживания)** — помогает поддерживать обещанный уровень доступности и производительности
4. **Монетизация API** — позволяет создавать разные уровни доступа с разными ограничениями скорости
5. **Снижение пиков нагрузки** — сглаживает пики трафика, обеспечивая более предсказуемую производительность

## Основные алгоритмы ограничения скорости

Существует несколько алгоритмов ограничения скорости, каждый со своими преимуществами и ограничениями. Рассмотрим наиболее распространенные из них.

### 1. Фиксированное окно (Fixed Window)

Самый простой алгоритм, который разделяет время на равные интервалы (окна) и отслеживает количество запросов в текущем окне. Когда счетчик запросов превышает предел, последующие запросы отклоняются до начала следующего окна.

#### Реализация на Python:

```python
import time

class FixedWindowRateLimiter:
    """
    Реализация алгоритма ограничения скорости с фиксированным окном.
    
    Атрибуты:
        window_size (int): Размер окна в секундах
        max_requests (int): Максимальное количество запросов в окне
        clients (dict): Словарь {client_id: (current_window_start, request_count)}
    """
    
    def __init__(self, window_size=60, max_requests=100):
        """
        Инициализирует ограничитель скорости.
        
        Args:
            window_size: Размер окна в секундах (по умолчанию 60 секунд)
            max_requests: Максимальное количество запросов в окне (по умолчанию 100)
        """
        self.window_size = window_size
        self.max_requests = max_requests
        self.clients = {}  # {client_id: (current_window_start, request_count)}
    
    def is_allowed(self, client_id):
        """
        Проверяет, разрешен ли запрос для данного клиента.
        
        Args:
            client_id: Идентификатор клиента (IP-адрес, токен API и т.д.)
            
        Returns:
            bool: True, если запрос разрешен, иначе False
        """
        current_time = int(time.time())
        
        # Если клиент отправляет запрос впервые или окно истекло
        if client_id not in self.clients or current_time - self.clients[client_id][0] >= self.window_size:
            # Начинаем новое окно
            self.clients[client_id] = (current_time, 1)
            return True
        
        # Получаем текущее состояние клиента
        window_start, request_count = self.clients[client_id]
        
        # Проверяем, не превышен ли предел запросов
        if request_count < self.max_requests:
            # Увеличиваем счетчик запросов
            self.clients[client_id] = (window_start, request_count + 1)
            return True
        
        # Предел запросов превышен
        return False
    
    def get_client_status(self, client_id):
        """
        Возвращает текущий статус клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            dict: Информация о статусе клиента
        """
        if client_id not in self.clients:
            return {
                "requests_made": 0,
                "requests_remaining": self.max_requests,
                "window_reset_in": self.window_size
            }
        
        window_start, request_count = self.clients[client_id]
        current_time = int(time.time())
        time_elapsed = current_time - window_start
        
        # Если окно истекло, возвращаем новое окно
        if time_elapsed >= self.window_size:
            return {
                "requests_made": 0,
                "requests_remaining": self.max_requests,
                "window_reset_in": self.window_size
            }
        
        return {
            "requests_made": request_count,
            "requests_remaining": max(0, self.max_requests - request_count),
            "window_reset_in": self.window_size - time_elapsed
        }

# Пример использования
limiter = FixedWindowRateLimiter(window_size=10, max_requests=5)

# Имитация запросов от клиента
client_id = "client123"
for i in range(8):
    allowed = limiter.is_allowed(client_id)
    status = limiter.get_client_status(client_id)
    print(f"Запрос {i+1}: {'Разрешен' if allowed else 'Отклонен'}, Статус: {status}")
    time.sleep(1)
```

#### Преимущества и недостатки:
- ✅ **Простота реализации и понимания**
- ✅ **Малые затраты памяти**
- ❌ **Проблема "краевого эффекта"**: клиент может отправить max_requests запросов в конце одного окна и max_requests запросов в начале следующего, что фактически дает 2*max_requests запросов за короткий промежуток времени
- ❌ **Нестабильный трафик**: запросы могут быть распределены неравномерно внутри окна

### 2. Скользящее окно (Sliding Window)

Улучшение алгоритма с фиксированным окном, которое учитывает запросы в текущем окне и частично в предыдущем окне, используя взвешенное среднее. Это сглаживает "краевой эффект".

#### Реализация на Python:

```python
import time
from collections import deque

class SlidingWindowRateLimiter:
    """
    Реализация алгоритма ограничения скорости со скользящим окном.
    
    Атрибуты:
        window_size (int): Размер окна в секундах
        max_requests (int): Максимальное количество запросов в окне
        clients (dict): Словарь {client_id: deque([(timestamp, count)])}
    """
    
    def __init__(self, window_size=60, max_requests=100):
        """
        Инициализирует ограничитель скорости.
        
        Args:
            window_size: Размер окна в секундах (по умолчанию 60 секунд)
            max_requests: Максимальное количество запросов в окне (по умолчанию 100)
        """
        self.window_size = window_size
        self.max_requests = max_requests
        self.clients = {}  # {client_id: deque([(timestamp, count)])}
    
    def _cleanup_old_timestamps(self, client_id, current_time):
        """
        Удаляет устаревшие временные метки для данного клиента.
        
        Args:
            client_id: Идентификатор клиента
            current_time: Текущее время (в секундах)
        """
        if client_id not in self.clients:
            self.clients[client_id] = deque()
            return
        
        window_start = current_time - self.window_size
        
        # Удаляем все временные метки, которые находятся за пределами окна
        while self.clients[client_id] and self.clients[client_id][0][0] < window_start:
            self.clients[client_id].popleft()
    
    def is_allowed(self, client_id):
        """
        Проверяет, разрешен ли запрос для данного клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            bool: True, если запрос разрешен, иначе False
        """
        current_time = time.time()
        
        # Удаляем устаревшие данные
        self._cleanup_old_timestamps(client_id, current_time)
        
        # Подсчитываем общее количество запросов в текущем скользящем окне
        total_requests = sum(count for _, count in self.clients[client_id])
        
        # Проверяем, не превышен ли предел запросов
        if total_requests < self.max_requests:
            # Добавляем текущий запрос
            self.clients[client_id].append((current_time, 1))
            return True
        
        # Предел запросов превышен
        return False
    
    def get_client_status(self, client_id):
        """
        Возвращает текущий статус клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            dict: Информация о статусе клиента
        """
        current_time = time.time()
        
        # Удаляем устаревшие данные
        self._cleanup_old_timestamps(client_id, current_time)
        
        # Если клиент отсутствует или у него нет записей
        if client_id not in self.clients or not self.clients[client_id]:
            return {
                "requests_made": 0,
                "requests_remaining": self.max_requests,
                "window_status": "empty"
            }
        
        # Подсчитываем общее количество запросов
        total_requests = sum(count for _, count in self.clients[client_id])
        
        # Находим самый старый запрос в окне
        oldest_timestamp = self.clients[client_id][0][0] if self.clients[client_id] else current_time
        time_until_reset = max(0, self.window_size - (current_time - oldest_timestamp))
        
        return {
            "requests_made": total_requests,
            "requests_remaining": max(0, self.max_requests - total_requests),
            "window_reset_in": time_until_reset
        }

# Пример использования
limiter = SlidingWindowRateLimiter(window_size=10, max_requests=5)

# Имитация запросов от клиента
client_id = "client456"
for i in range(8):
    allowed = limiter.is_allowed(client_id)
    status = limiter.get_client_status(client_id)
    print(f"Запрос {i+1}: {'Разрешен' if allowed else 'Отклонен'}, Статус: {status}")
    time.sleep(1)
```

#### Преимущества и недостатки:
- ✅ **Более равномерное распределение запросов**
- ✅ **Устранение "краевого эффекта"**
- ❌ **Требует больше памяти для хранения временных меток всех запросов в окне**
- ❌ **Более сложная реализация**

### 3. Алгоритм маркерного ведра (Token Bucket)

Этот алгоритм использует абстракцию "ведра", которое наполняется маркерами с постоянной скоростью. Каждый запрос потребляет один маркер из ведра. Если ведро пусто, запрос отклоняется.

#### Реализация на Python:

```python
import time

class TokenBucketRateLimiter:
    """
    Реализация алгоритма ограничения скорости с маркерным ведром.
    
    Атрибуты:
        rate (float): Скорость пополнения маркеров (маркеров в секунду)
        max_tokens (int): Максимальное количество маркеров в ведре
        clients (dict): Словарь {client_id: (last_update_time, available_tokens)}
    """
    
    def __init__(self, rate=1.0, max_tokens=10):
        """
        Инициализирует ограничитель скорости.
        
        Args:
            rate: Скорость пополнения маркеров (маркеров в секунду)
            max_tokens: Максимальное количество маркеров в ведре
        """
        self.rate = rate  # маркеров в секунду
        self.max_tokens = max_tokens
        self.clients = {}  # {client_id: (last_update_time, available_tokens)}
    
    def _refill_tokens(self, client_id, current_time):
        """
        Пополняет токены для клиента на основе прошедшего времени.
        
        Args:
            client_id: Идентификатор клиента
            current_time: Текущее время (в секундах)
        """
        if client_id not in self.clients:
            # Новый клиент получает полное ведро
            self.clients[client_id] = (current_time, self.max_tokens)
            return
        
        last_update, tokens = self.clients[client_id]
        
        # Вычисляем количество новых токенов на основе прошедшего времени
        time_passed = current_time - last_update
        new_tokens = time_passed * self.rate
        
        # Обновляем количество доступных токенов (не превышая максимум)
        self.clients[client_id] = (current_time, min(tokens + new_tokens, self.max_tokens))
    
    def is_allowed(self, client_id):
        """
        Проверяет, разрешен ли запрос для данного клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            bool: True, если запрос разрешен, иначе False
        """
        current_time = time.time()
        
        # Пополняем токены
        self._refill_tokens(client_id, current_time)
        
        # Получаем текущее состояние клиента
        _, tokens = self.clients[client_id]
        
        # Проверяем, есть ли доступный токен для запроса
        if tokens >= 1:
            # Потребляем токен
            self.clients[client_id] = (current_time, tokens - 1)
            return True
        
        # Недостаточно токенов
        return False
    
    def get_client_status(self, client_id):
        """
        Возвращает текущий статус клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            dict: Информация о статусе клиента
        """
        current_time = time.time()
        
        # Пополняем токены перед проверкой статуса
        self._refill_tokens(client_id, current_time)
        
        # Если клиент отсутствует
        if client_id not in self.clients:
            return {
                "available_tokens": self.max_tokens,
                "max_tokens": self.max_tokens,
                "refill_rate": self.rate
            }
        
        last_update, tokens = self.clients[client_id]
        
        # Вычисляем время до пополнения одного токена
        time_until_next_token = 0 if tokens >= self.max_tokens else (1 / self.rate)
        
        return {
            "available_tokens": tokens,
            "max_tokens": self.max_tokens,
            "refill_rate": self.rate,
            "time_until_next_token": time_until_next_token
        }

# Пример использования
limiter = TokenBucketRateLimiter(rate=0.5, max_tokens=3)  # 1 токен каждые 2 секунды, максимум 3 токена

# Имитация запросов от клиента
client_id = "client789"
for i in range(6):
    allowed = limiter.is_allowed(client_id)
    status = limiter.get_client_status(client_id)
    print(f"Запрос {i+1}: {'Разрешен' if allowed else 'Отклонен'}, Статус: {status}")
    time.sleep(1)
```

#### Преимущества и недостатки:
- ✅ **Поддержка всплесков трафика** — клиент может накапливать токены и использовать их для всплеска запросов
- ✅ **Гибкость настройки** — можно независимо настраивать скорость пополнения и размер ведра
- ✅ **Равномерное распределение запросов** — алгоритм естественным образом сглаживает трафик
- ❌ **Требуется хранение состояния для каждого клиента**
- ❌ **Небольшая сложность расчетов при каждом запросе**

### 4. Алгоритм негерметичного ведра (Leaky Bucket)

Алгоритм негерметичного ведра работает подобно физическому ведру с отверстием: запросы поступают в ведро, а ведро "протекает" с постоянной скоростью, пропуская запросы на обработку. Если ведро переполняется, новые запросы отклоняются.

#### Реализация на Python:

```python
import time
from collections import deque

class LeakyBucketRateLimiter:
    """
    Реализация алгоритма ограничения скорости с негерметичным ведром.
    
    Атрибуты:
        capacity (int): Размер ведра (максимальное количество запросов в очереди)
        leak_rate (float): Скорость "утечки" запросов (запросов в секунду)
        clients (dict): Словарь {client_id: (last_leak_time, queue)}
    """
    
    def __init__(self, capacity=10, leak_rate=1.0):
        """
        Инициализирует ограничитель скорости.
        
        Args:
            capacity: Размер ведра (максимальное количество запросов в очереди)
            leak_rate: Скорость "утечки" запросов (запросов в секунду)
        """
        self.capacity = capacity
        self.leak_rate = leak_rate  # запросов в секунду
        self.clients = {}  # {client_id: (last_leak_time, queue)}
    
    def _process_leaks(self, client_id, current_time):
        """
        Обрабатывает "утечку" запросов из ведра с момента последнего обновления.
        
        Args:
            client_id: Идентификатор клиента
            current_time: Текущее время (в секундах)
        """
        if client_id not in self.clients:
            self.clients[client_id] = (current_time, deque())
            return
        
        last_leak_time, queue = self.clients[client_id]
        
        # Вычисляем, сколько запросов должно "утечь" с момента последнего обновления
        time_passed = current_time - last_leak_time
        leaks = int(time_passed * self.leak_rate)
        
        # Убираем "утекшие" запросы из очереди
        for _ in range(min(leaks, len(queue))):
            queue.popleft()
        
        # Обновляем время последней "утечки"
        self.clients[client_id] = (
            last_leak_time + leaks / self.leak_rate if leaks > 0 else last_leak_time,
            queue
        )
    
    def is_allowed(self, client_id):
        """
        Проверяет, разрешен ли запрос для данного клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            bool: True, если запрос разрешен (помещен в очередь), иначе False
        """
        current_time = time.time()
        
        # Обрабатываем "утечку" запросов
        self._process_leaks(client_id, current_time)
        
        # Получаем текущее состояние клиента
        _, queue = self.clients[client_id]
        
        # Проверяем, есть ли место в ведре для нового запроса
        if len(queue) < self.capacity:
            # Помещаем запрос в очередь
            queue.append(current_time)
            return True
        
        # Ведро переполнено
        return False
    
    def get_client_status(self, client_id):
        """
        Возвращает текущий статус клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            dict: Информация о статусе клиента
        """
        current_time = time.time()
        
        # Обрабатываем "утечку" запросов перед проверкой статуса
        self._process_leaks(client_id, current_time)
        
        # Если клиент отсутствует
        if client_id not in self.clients:
            return {
                "queue_size": 0,
                "capacity": self.capacity,
                "leak_rate": self.leak_rate
            }
        
        last_leak_time, queue = self.clients[client_id]
        
        # Вычисляем время до "утечки" следующего запроса
        time_until_next_leak = 0 if not queue else (1 / self.leak_rate)
        
        return {
            "queue_size": len(queue),
            "capacity": self.capacity,
            "leak_rate": self.leak_rate,
            "time_until_next_leak": time_until_next_leak
        }

# Пример использования
limiter = LeakyBucketRateLimiter(capacity=3, leak_rate=0.5)  # 1 запрос каждые 2 секунды, размер очереди 3

# Имитация запросов от клиента
client_id = "client101"
for i in range(6):
    allowed = limiter.is_allowed(client_id)
    status = limiter.get_client_status(client_id)
    print(f"Запрос {i+1}: {'Разрешен' if allowed else 'Отклонен'}, Статус: {status}")
    time.sleep(1)
```

#### Преимущества и недостатки:
- ✅ **Равномерный исходящий трафик** — запросы обрабатываются с постоянной скоростью
- ✅ **Буферизация запросов** — запросы не отбрасываются сразу, а ставятся в очередь (если есть место)
- ✅ **Хорошо подходит для защиты бэкенд-сервисов**
- ❌ **Потенциальная задержка обработки запросов** — запросы могут ждать в очереди
- ❌ **Требует больше памяти для хранения очереди запросов**

## Распределенное ограничение скорости

В распределенных системах, когда API обслуживается несколькими серверами, требуется централизованное хранилище для отслеживания использования ресурсов. Обычно для этого используют Redis или другие распределенные хранилища.

### Реализация с использованием Redis

```python
import time
import redis

class RedisRateLimiter:
    """
    Распределенный ограничитель скорости, использующий Redis.
    Реализует алгоритм фиксированного окна с использованием Redis для хранения счетчиков.
    """
    
    def __init__(self, redis_client, window_size=60, max_requests=100):
        """
        Инициализирует ограничитель скорости.
        
        Args:
            redis_client: Клиент Redis
            window_size: Размер окна в секундах (по умолчанию 60 секунд)
            max_requests: Максимальное количество запросов в окне (по умолчанию 100)
        """
        self.redis = redis_client
        self.window_size = window_size
        self.max_requests = max_requests
    
    def is_allowed(self, client_id):
        """
        Проверяет, разрешен ли запрос для данного клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            bool: True, если запрос разрешен, иначе False
        """
        current_time = int(time.time())
        window_key = f"rate_limit:{client_id}:{current_time // self.window_size}"
        
        # Используем транзакцию Redis для атомарного обновления
        pipe = self.redis.pipeline()
        
        # Проверяем и увеличиваем счетчик запросов
        pipe.incr(window_key)
        # Устанавливаем TTL для ключа, если он еще не существует
        pipe.expire(window_key, self.window_size * 2)  # *2 для безопасности
        
        # Выполняем транзакцию
        result = pipe.execute()
        request_count = result[0]
        
        # Возвращаем результат проверки
        return request_count <= self.max_requests
    
    def get_client_status(self, client_id):
        """
        Возвращает текущий статус клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            dict: Информация о статусе клиента
        """
        current_time = int(time.time())
        current_window = current_time // self.window_size
        window_key = f"rate_limit:{client_id}:{current_window}"
        
        # Получаем текущее количество запросов
        request_count = int(self.redis.get(window_key) or 0)
        
        # Вычисляем, сколько времени осталось до сброса окна
        time_in_window = current_time % self.window_size
        reset_in = self.window_size - time_in_window
        
        return {
            "requests_made": request_count,
            "requests_remaining": max(0, self.max_requests - request_count),
            "window_reset_in": reset_in
        }

# Пример использования (требуется Redis)
"""
# Инициализация клиента Redis
redis_client = redis.Redis(host='localhost', port=6379, db=0)

# Создание ограничителя скорости
redis_limiter = RedisRateLimiter(redis_client, window_size=10, max_requests=5)

# Имитация запросов от клиента
client_id = "user12345"
for i in range(8):
    allowed = redis_limiter.is_allowed(client_id)
    status = redis_limiter.get_client_status(client_id)
    print(f"Запрос {i+1}: {'Разрешен' if allowed else 'Отклонен'}, Статус: {status}")
    time.sleep(1)
"""
```

## Практическое применение в веб-разработке

### 1. Middleware для ограничения скорости в веб-фреймворке (Flask)

```python
from functools import wraps
from flask import request, jsonify, Flask

class FlaskRateLimiter:
    """
    Middleware для ограничения скорости запросов в приложении Flask.
    """
    
    def __init__(self, limiter_class, **limiter_args):
        """
        Инициализирует middleware.
        
        Args:
            limiter_class: Класс ограничителя скорости (например, TokenBucketRateLimiter)
            limiter_args: Аргументы для инициализации ограничителя скорости
        """
        self.limiter = limiter_class(**limiter_args)
        self.exempt_routes = set()
    
    def get_client_identifier(self, request):
        """
        Получает идентификатор клиента из запроса.
        По умолчанию используется IP-адрес.
        """
        return request.remote_addr
    
    def exempt(self, route):
        """
        Исключает маршрут из ограничения скорости.
        
        Args:
            route: Маршрут для исключения
        """
        self.exempt_routes.add(route)
    
    def limit(self):
        """
        Декоратор для ограничения скорости запросов к конкретному маршруту.
        """
        def decorator(f):
            @wraps(f)
            def wrapped(*args, **kwargs):
                # Проверяем, исключен ли маршрут
                if request.path in self.exempt_routes:
                    return f(*args, **kwargs)
                
                # Получаем идентификатор клиента
                client_id = self.get_client_identifier(request)
                
                # Проверяем, разрешен ли запрос
                if not self.limiter.is_allowed(client_id):
                    # Получаем статус клиента для HTTP-заголовков
                    status = self.limiter.get_client_status(client_id)
                    
                    # Формируем ответ с ошибкой
                    response = jsonify({
                        "error": "Rate limit exceeded",
                        "status": status
                    })
                    response.status_code = 429  # Too Many Requests
                    
                    # Добавляем заголовки для информирования клиента
                    if hasattr(status, "get") and status.get("window_reset_in") is not None:
                        response.headers["Retry-After"] = str(int(status["window_reset_in"]))
                    
                    return response
                
                # Запрос разрешен, выполняем оригинальную функцию
                return f(*args, **kwargs)
            return wrapped
        return decorator

# Пример использования в приложении Flask
app = Flask(__name__)

# Инициализируем ограничитель скорости
rate_limiter = FlaskRateLimiter(TokenBucketRateLimiter, rate=5, max_tokens=10)

# Исключаем статические маршруты из ограничения
rate_limiter.exempt('/static')
rate_limiter.exempt('/favicon.ico')

@app.route('/')
@rate_limiter.limit()
def index():
    return "Hello, World!"

@app.route('/api/resource')
@rate_limiter.limit()
def api_resource():
    return jsonify({"status": "success", "data": "Some resource data"})

# Маршрут, не ограниченный rate limiter
@app.route('/health')
def health_check():
    return jsonify({"status": "ok"})

if __name__ == '__main__':
    app.run(debug=True)
```

### 2. Управление ограничением скорости с разными уровнями доступа

```python
class TieredRateLimiter:
    """
    Ограничитель скорости с поддержкой разных уровней доступа (тарифных планов).
    """
    
    def __init__(self, tier_limits):
        """
        Инициализирует ограничитель скорости.
        
        Args:
            tier_limits: Словарь {tier_name: (rate, max_tokens)} для каждого уровня доступа
        """
        self.tier_limits = tier_limits
        self.limiters = {
            tier: TokenBucketRateLimiter(rate=rate, max_tokens=max_tokens)
            for tier, (rate, max_tokens) in tier_limits.items()
        }
        self.client_tiers = {}  # {client_id: tier_name}
    
    def set_client_tier(self, client_id, tier_name):
        """
        Устанавливает уровень доступа для клиента.
        
        Args:
            client_id: Идентификатор клиента
            tier_name: Имя уровня доступа
        """
        if tier_name not in self.tier_limits:
            raise ValueError(f"Unknown tier: {tier_name}")
        self.client_tiers[client_id] = tier_name
    
    def get_client_tier(self, client_id):
        """
        Получает уровень доступа клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            str: Имя уровня доступа (по умолчанию 'basic')
        """
        return self.client_tiers.get(client_id, 'basic')
    
    def is_allowed(self, client_id):
        """
        Проверяет, разрешен ли запрос для данного клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            bool: True, если запрос разрешен, иначе False
        """
        tier = self.get_client_tier(client_id)
        limiter = self.limiters[tier]
        return limiter.is_allowed(client_id)
    
    def get_client_status(self, client_id):
        """
        Возвращает текущий статус клиента.
        
        Args:
            client_id: Идентификатор клиента
            
        Returns:
            dict: Информация о статусе клиента
        """
        tier = self.get_client_tier(client_id)
        limiter = self.limiters[tier]
        status = limiter.get_client_status(client_id)
        
        # Добавляем информацию о тарифном плане
        status["tier"] = tier
        status["tier_limits"] = self.tier_limits[tier]
        
        return status

# Пример использования
tier_limits = {
    'basic': (1, 5),       # 1 запрос в секунду, макс. 5 токенов
    'premium': (5, 15),    # 5 запросов в секунду, макс. 15 токенов
    'enterprise': (20, 50) # 20 запросов в секунду, макс. 50 токенов
}

tiered_limiter = TieredRateLimiter(tier_limits)

# Устанавливаем разные уровни доступа для клиентов
tiered_limiter.set_client_tier("user1", "basic")
tiered_limiter.set_client_tier("user2", "premium")
tiered_limiter.set_client_tier("user3", "enterprise")

# Проверяем ограничения для разных клиентов
for i in range(3):
    for user in ["user1", "user2", "user3"]:
        allowed = tiered_limiter.is_allowed(user)
        status = tiered_limiter.get_client_status(user)
        print(f"User: {user}, Request {i+1}: {'Allowed' if allowed else 'Denied'}, Status: {status}")
    time.sleep(1)
```

### 3. Ограничение скорости для отдельных конечных точек API

```python
class EndpointRateLimiter:
    """
    Ограничитель скорости с разными правилами для разных эндпоинтов.
    """
    
    def __init__(self):
        """Инициализирует ограничитель скорости для эндпоинтов."""
        self.endpoint_limiters = {}  # {endpoint: limiter}
        self.default_limiter = None
    
    def add_endpoint_limit(self, endpoint, limiter_class, **limiter_args):
        """
        Добавляет ограничение для конкретного эндпоинта.
        
        Args:
            endpoint: Путь эндпоинта или шаблон
            limiter_class: Класс ограничителя скорости
            limiter_args: Аргументы для инициализации ограничителя
        """
        self.endpoint_limiters[endpoint] = limiter_class(**limiter_args)
    
    def set_default_limit(self, limiter_class, **limiter_args):
        """
        Устанавливает ограничение по умолчанию для всех эндпоинтов.
        
        Args:
            limiter_class: Класс ограничителя скорости
            limiter_args: Аргументы для инициализации ограничителя
        """
        self.default_limiter = limiter_class(**limiter_args)
    
    def _get_limiter_for_endpoint(self, endpoint):
        """
        Получает ограничитель для данного эндпоинта.
        
        Args:
            endpoint: Путь эндпоинта
            
        Returns:
            Объект ограничителя скорости
        """
        # Ищем точное совпадение
        if endpoint in self.endpoint_limiters:
            return self.endpoint_limiters[endpoint]
        
        # Ищем совпадение по шаблону (простая реализация)
        for pattern, limiter in self.endpoint_limiters.items():
            if pattern.endswith('*') and endpoint.startswith(pattern[:-1]):
                return limiter
        
        # Возвращаем ограничитель по умолчанию
        return self.default_limiter
    
    def is_allowed(self, client_id, endpoint):
        """
        Проверяет, разрешен ли запрос для данного клиента и эндпоинта.
        
        Args:
            client_id: Идентификатор клиента
            endpoint: Путь эндпоинта
            
        Returns:
            bool: True, если запрос разрешен, иначе False
        """
        limiter = self._get_limiter_for_endpoint(endpoint)
        if limiter is None:
            return True  # Нет ограничений для этого эндпоинта
        
        # Используем составной ключ (клиент + эндпоинт)
        composite_key = f"{client_id}:{endpoint}"
        return limiter.is_allowed(composite_key)
    
    def get_client_status(self, client_id, endpoint):
        """
        Возвращает текущий статус клиента для данного эндпоинта.
        
        Args:
            client_id: Идентификатор клиента
            endpoint: Путь эндпоинта
            
        Returns:
            dict: Информация о статусе клиента
        """
        limiter = self._get_limiter_for_endpoint(endpoint)
        if limiter is None:
            return {"status": "unlimited"}
        
        # Используем составной ключ
        composite_key = f"{client_id}:{endpoint}"
        return limiter.get_client_status(composite_key)

# Пример использования
endpoint_limiter = EndpointRateLimiter()

# Устанавливаем ограничения для разных эндпоинтов
endpoint_limiter.add_endpoint_limit(
    "/api/public/*",  # Все публичные API
    TokenBucketRateLimiter,
    rate=1.0,
    max_tokens=5
)

endpoint_limiter.add_endpoint_limit(
    "/api/users",  # Эндпоинт пользователей
    TokenBucketRateLimiter,
    rate=0.2,  # 1 запрос каждые 5 секунд
    max_tokens=2
)

# Ограничение по умолчанию
endpoint_limiter.set_default_limit(
    TokenBucketRateLimiter,
    rate=0.5,  # 1 запрос каждые 2 секунды
    max_tokens=3
)

# Проверяем ограничения для разных эндпоинтов
endpoints = [
    "/api/public/data",
    "/api/users",
    "/api/products"
]

client_id = "test_user"
for i in range(3):
    for endpoint in endpoints:
        allowed = endpoint_limiter.is_allowed(client_id, endpoint)
        status = endpoint_limiter.get_client_status(client_id, endpoint)
        print(f"Endpoint: {endpoint}, Request {i+1}: {'Allowed' if allowed else 'Denied'}, Status: {status}")
    time.sleep(1)
```

## Заключение

Алгоритмы ограничения скорости являются важным компонентом современных веб-приложений и API. Они защищают серверы от перегрузки, обеспечивают справедливое распределение ресурсов и позволяют создавать разные уровни обслуживания для разных клиентов.

При выборе алгоритма ограничения скорости необходимо учитывать особенности вашего приложения:

- **Фиксированное окно** — самый простой вариант, подходит для базовой защиты API
- **Скользящее окно** — улучшенная версия фиксированного окна, предотвращает краевые эффекты
- **Маркерное ведро** — хорошо подходит для API, где допустимы всплески трафика
- **Негерметичное ведро** — обеспечивает равномерный исходящий поток запросов, защищает бэкенд-сервисы

В распределенных системах рекомендуется использовать централизованное хранилище (например, Redis) для отслеживания использования ресурсов.

Правильно настроенные алгоритмы ограничения скорости не только защищают ваше приложение, но и улучшают пользовательский опыт, сохраняя стабильную производительность даже в условиях высокой нагрузки.
