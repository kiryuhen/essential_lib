# Алгоритмы кеширования (LRU, LFU)

Кеширование — это фундаментальная техника в веб-разработке, которая позволяет сохранять результаты дорогостоящих операций для повторного использования. Этот подход значительно повышает производительность приложений, уменьшая время отклика и снижая нагрузку на серверы и базы данных.

## Зачем нужно кеширование?

Кеширование необходимо по нескольким причинам:

1. **Улучшение производительности** — доступ к данным из кеша обычно намного быстрее, чем повторное вычисление или извлечение из базы данных
2. **Снижение нагрузки** — снижается нагрузка на серверы и базы данных
3. **Повышение отказоустойчивости** — приложение может продолжать работать с кешированными данными даже при сбоях в других компонентах
4. **Экономия трафика** — уменьшается количество передаваемых данных, что особенно важно для мобильных приложений

Однако размер кеша всегда ограничен, поэтому необходимы алгоритмы, определяющие, какие элементы следует хранить, а какие удалять, когда кеш заполняется. Две наиболее распространенные стратегии — LRU (Least Recently Used, вытеснение давно не используемых) и LFU (Least Frequently Used, вытеснение редко используемых).

## LRU (Least Recently Used) Cache

LRU-кеш удаляет элемент, который не использовался дольше всего. Основная идея состоит в том, что элементы, которые были недавно использованы, с большей вероятностью будут использоваться снова в ближайшем будущем.

### Принцип работы LRU-кеша:

1. Когда доступ к элементу происходит (чтение или запись), он перемещается в начало списка "недавно использованных"
2. Когда кеш заполняется и требуется место для нового элемента, удаляется элемент из конца списка (наименее недавно использованный)

### Реализация LRU-кеша на Python

Для эффективной реализации LRU-кеша нам нужна структура данных, которая обеспечивает:
- Быстрое извлечение элементов по ключу (O(1))
- Быстрое перемещение элементов в начало списка (O(1))
- Быстрое удаление элементов из конца списка (O(1))

В Python для этого отлично подходит комбинация словаря (`dict`) и двусвязного списка, который реализуется через встроенный класс `OrderedDict` из модуля `collections`.

```python
from collections import OrderedDict

class LRUCache:
    """
    Реализация LRU-кеша (Least Recently Used Cache).
    
    Атрибуты:
        capacity (int): Максимальное количество элементов в кеше
        cache (OrderedDict): Словарь для хранения элементов кеша
    """
    
    def __init__(self, capacity):
        """Инициализирует кеш с заданной емкостью."""
        self.capacity = capacity
        self.cache = OrderedDict()
    
    def get(self, key):
        """
        Получает значение по ключу и обновляет порядок недавно используемых элементов.
        
        Args:
            key: Ключ для поиска в кеше
            
        Returns:
            Значение, связанное с ключом, или -1, если ключа нет в кеше
        """
        if key not in self.cache:
            return -1
        
        # Перемещаем элемент в конец (самый недавно использованный)
        value = self.cache.pop(key)
        self.cache[key] = value
        return value
    
    def put(self, key, value):
        """
        Добавляет или обновляет элемент в кеше.
        
        Args:
            key: Ключ для добавления/обновления
            value: Значение для сохранения
        """
        # Если ключ уже существует, удаляем его (и позже добавляем снова)
        if key in self.cache:
            self.cache.pop(key)
        # Если кеш заполнен, удаляем наименее недавно использованный элемент (первый в OrderedDict)
        elif len(self.cache) >= self.capacity:
            self.cache.popitem(last=False)
        
        # Добавляем новый элемент (он будет самым недавно использованным)
        self.cache[key] = value
    
    def __str__(self):
        """Возвращает строковое представление кеша."""
        items = list(self.cache.items())
        return f"LRUCache(capacity={self.capacity}, items={items})"

# Пример использования
cache = LRUCache(2)  # Кеш с емкостью 2 элемента

cache.put(1, "one")
cache.put(2, "two")
print(cache)  # LRUCache(capacity=2, items=[(1, 'one'), (2, 'two')])

cache.get(1)  # Доступ к элементу с ключом 1, теперь он самый "недавно использованный"
print(cache)  # LRUCache(capacity=2, items=[(2, 'two'), (1, 'one')])

cache.put(3, "three")  # Добавляем новый элемент, вытесняя наименее недавно использованный (2)
print(cache)  # LRUCache(capacity=2, items=[(1, 'one'), (3, 'three')])

print(cache.get(2))  # -1 (элемент с ключом 2 был вытеснен)
print(cache.get(1))  # 'one'
```

### Альтернативная реализация с явным двусвязным списком

Хотя `OrderedDict` удобен, можно также реализовать LRU-кеш с использованием явного двусвязного списка для лучшего понимания внутреннего механизма:

```python
class Node:
    """Узел двусвязного списка для LRU-кеша."""
    
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class LRUCacheExplicit:
    """
    Реализация LRU-кеша с использованием явного двусвязного списка.
    """
    
    def __init__(self, capacity):
        """Инициализирует кеш с заданной емкостью."""
        self.capacity = capacity
        self.cache = {}  # словарь {key: node}
        
        # Инициализация фиктивных головы и хвоста списка
        self.head = Node(0, 0)  # самый старый элемент (будет удаляться первым)
        self.tail = Node(0, 0)  # самый свежий элемент
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _add_node(self, node):
        """Добавляет узел перед хвостом (как самый свежий)."""
        prev_node = self.tail.prev
        prev_node.next = node
        node.prev = prev_node
        node.next = self.tail
        self.tail.prev = node
    
    def _remove_node(self, node):
        """Удаляет узел из списка."""
        node.prev.next = node.next
        node.next.prev = node.prev
    
    def _move_to_tail(self, node):
        """Перемещает узел в конец (делает его самым свежим)."""
        self._remove_node(node)
        self._add_node(node)
    
    def get(self, key):
        """
        Получает значение по ключу.
        
        Args:
            key: Ключ для поиска
            
        Returns:
            Значение или -1, если ключ не найден
        """
        if key not in self.cache:
            return -1
        
        node = self.cache[key]
        self._move_to_tail(node)  # обновляем как недавно использованный
        return node.value
    
    def put(self, key, value):
        """
        Добавляет или обновляет элемент в кеше.
        
        Args:
            key: Ключ
            value: Значение
        """
        # Если ключ уже есть, обновляем значение и перемещаем узел
        if key in self.cache:
            node = self.cache[key]
            node.value = value
            self._move_to_tail(node)
            return
        
        # Если кеш заполнен, удаляем самый старый элемент (следующий после головы)
        if len(self.cache) >= self.capacity:
            oldest = self.head.next
            self._remove_node(oldest)
            del self.cache[oldest.key]
        
        # Создаем новый узел и добавляем его
        new_node = Node(key, value)
        self._add_node(new_node)
        self.cache[key] = new_node
    
    def __str__(self):
        """Возвращает строковое представление кеша."""
        items = []
        current = self.head.next
        while current != self.tail:
            items.append((current.key, current.value))
            current = current.next
        return f"LRUCacheExplicit(capacity={self.capacity}, items={items})"

# Пример использования
cache = LRUCacheExplicit(2)

cache.put(1, "one")
cache.put(2, "two")
print(cache)  # LRUCacheExplicit(capacity=2, items=[(1, 'one'), (2, 'two')])

cache.get(1)  # Обновляем 1 как недавно использованный
print(cache)  # LRUCacheExplicit(capacity=2, items=[(2, 'two'), (1, 'one')])

cache.put(3, "three")  # Добавляем новый элемент, вытесняя 2
print(cache)  # LRUCacheExplicit(capacity=2, items=[(1, 'one'), (3, 'three')])
```

## LFU (Least Frequently Used) Cache

LFU-кеш удаляет элемент, который используется реже всего. Это основано на предположении, что популярные элементы, которые запрашиваются чаще всего, скорее всего, будут запрошены снова.

### Принцип работы LFU-кеша:

1. Для каждого элемента кеша отслеживается частота использования
2. Когда кеш заполняется и требуется место для нового элемента, удаляется элемент с наименьшей частотой использования
3. Если несколько элементов имеют одинаковую наименьшую частоту, обычно удаляется тот, который не использовался дольше всего (сочетание LFU и LRU)

### Реализация LFU-кеша на Python

Эффективная реализация LFU-кеша сложнее, чем LRU, поскольку необходимо отслеживать не только порядок, но и частоту использования элементов:

```python
from collections import defaultdict, OrderedDict

class LFUCache:
    """
    Реализация LFU-кеша (Least Frequently Used Cache).
    
    Атрибуты:
        capacity (int): Максимальное количество элементов в кеше
        cache (dict): Словарь для хранения элементов кеша {key: (value, frequency)}
        freq_lists (defaultdict): Словарь {frequency: OrderedDict{key: value}}
                                 для отслеживания порядка добавления для каждой частоты
        min_freq (int): Минимальная частота среди всех элементов кеша
    """
    
    def __init__(self, capacity):
        """Инициализирует кеш с заданной емкостью."""
        self.capacity = capacity
        self.cache = {}  # {key: [value, frequency]}
        self.freq_lists = defaultdict(OrderedDict)  # {frequency: OrderedDict{key: value}}
        self.min_freq = 0
    
    def get(self, key):
        """
        Получает значение по ключу и обновляет частоту использования.
        
        Args:
            key: Ключ для поиска в кеше
            
        Returns:
            Значение, связанное с ключом, или -1, если ключа нет в кеше
        """
        if key not in self.cache:
            return -1
        
        # Получаем текущие значение и частоту
        value, freq = self.cache[key]
        
        # Удаляем элемент из текущего списка частоты
        del self.freq_lists[freq][key]
        
        # Если список для текущей частоты стал пустым и это была минимальная частота,
        # увеличиваем минимальную частоту
        if not self.freq_lists[freq] and freq == self.min_freq:
            self.min_freq += 1
        
        # Увеличиваем частоту и добавляем элемент в новый список частоты
        freq += 1
        self.freq_lists[freq][key] = value
        self.cache[key] = [value, freq]
        
        return value
    
    def put(self, key, value):
        """
        Добавляет или обновляет элемент в кеше.
        
        Args:
            key: Ключ для добавления/обновления
            value: Значение для сохранения
        """
        # Особый случай: емкость кеша равна 0
        if self.capacity == 0:
            return
        
        # Если ключ уже существует, обновляем значение и частоту
        if key in self.cache:
            _, freq = self.cache[key]
            self.cache[key] = [value, freq]  # Обновляем значение
            self.get(key)  # Используем get для обновления частоты
            return
        
        # Если кеш полон, удаляем элемент с наименьшей частотой
        if len(self.cache) >= self.capacity:
            # Находим ключ с наименьшей частотой (и наименее недавно использованный)
            lfu_key, _ = self.freq_lists[self.min_freq].popitem(last=False)
            del self.cache[lfu_key]
        
        # Добавляем новый элемент с частотой 1
        self.cache[key] = [value, 1]
        self.freq_lists[1][key] = value
        self.min_freq = 1  # Новый элемент имеет минимальную частоту
    
    def __str__(self):
        """Возвращает строковое представление кеша."""
        items = [(k, v[0], v[1]) for k, v in self.cache.items()]  # (key, value, frequency)
        return f"LFUCache(capacity={self.capacity}, items={items}, min_freq={self.min_freq})"

# Пример использования
cache = LFUCache(2)

cache.put(1, "one")
cache.put(2, "two")
print(cache)  # LFUCache(capacity=2, items=[(1, 'one', 1), (2, 'two', 1)], min_freq=1)

cache.get(1)  # Увеличиваем частоту для ключа 1
print(cache)  # LFUCache(capacity=2, items=[(1, 'one', 2), (2, 'two', 1)], min_freq=1)

cache.put(3, "three")  # Добавляем новый элемент, вытесняя 2 (с наименьшей частотой)
print(cache)  # LFUCache(capacity=2, items=[(1, 'one', 2), (3, 'three', 1)], min_freq=1)

cache.get(1)  # Увеличиваем частоту для ключа 1 еще раз
print(cache)  # LFUCache(capacity=2, items=[(1, 'one', 3), (3, 'three', 1)], min_freq=1)

cache.put(4, "four")  # Добавляем новый элемент, вытесняя 3 (с наименьшей частотой)
print(cache)  # LFUCache(capacity=2, items=[(1, 'one', 3), (4, 'four', 1)], min_freq=1)
```

## Другие стратегии кеширования

Помимо LRU и LFU, существуют и другие алгоритмы кеширования:

### 1. FIFO (First In, First Out)

Самый простой алгоритм — удаляет элементы в порядке их добавления, независимо от того, как часто они используются.

```python
from collections import deque

class FIFOCache:
    """
    Реализация FIFO-кеша (First In, First Out).
    """
    
    def __init__(self, capacity):
        """Инициализирует кеш с заданной емкостью."""
        self.capacity = capacity
        self.cache = {}  # {key: value}
        self.queue = deque()  # Очередь для отслеживания порядка добавления
    
    def get(self, key):
        """Получает значение по ключу."""
        if key not in self.cache:
            return -1
        return self.cache[key]
    
    def put(self, key, value):
        """Добавляет или обновляет элемент в кеше."""
        if key in self.cache:
            # Обновляем значение существующего ключа
            self.cache[key] = value
            return
        
        # Если кеш полон, удаляем первый добавленный элемент
        if len(self.cache) >= self.capacity:
            oldest_key = self.queue.popleft()
            del self.cache[oldest_key]
        
        # Добавляем новый элемент
        self.cache[key] = value
        self.queue.append(key)
```

### 2. Random Replacement (RR)

Случайная замена — удаляет случайно выбранный элемент. Удивительно, но эта простая стратегия иногда работает не хуже более сложных алгоритмов.

```python
import random

class RandomCache:
    """
    Реализация кеша со случайной заменой.
    """
    
    def __init__(self, capacity):
        """Инициализирует кеш с заданной емкостью."""
        self.capacity = capacity
        self.cache = {}  # {key: value}
    
    def get(self, key):
        """Получает значение по ключу."""
        if key not in self.cache:
            return -1
        return self.cache[key]
    
    def put(self, key, value):
        """Добавляет или обновляет элемент в кеше."""
        if key in self.cache or len(self.cache) < self.capacity:
            self.cache[key] = value
            return
        
        # Если кеш полон, удаляем случайный элемент
        random_key = random.choice(list(self.cache.keys()))
        del self.cache[random_key]
        self.cache[key] = value
```

### 3. LRU-K

Расширение алгоритма LRU, которое учитывает историю K последних обращений к каждому элементу для принятия более информированных решений о вытеснении.

### 4. 2Q (Two Queue Algorithm)

Комбинирует FIFO и LRU, используя две очереди: один для новых элементов (FIFO) и один для часто используемых (LRU).

### 5. ARC (Adaptive Replacement Cache)

Адаптивно балансирует между недавно использованными и часто использованными элементами, автоматически настраиваясь под текущий паттерн доступа к данным.

## Практическое применение алгоритмов кеширования в веб-разработке

### 1. Кеширование запросов к базе данных

```python
class DBQueryCache:
    """
    Кеш для результатов запросов к базе данных.
    """
    
    def __init__(self, capacity=100):
        self.cache = LRUCache(capacity)
        self.hits = 0
        self.misses = 0
    
    def query(self, sql_query, execute_query_func):
        """
        Выполняет запрос с использованием кеша.
        
        Args:
            sql_query: SQL-запрос (строка)
            execute_query_func: Функция, выполняющая запрос к БД
            
        Returns:
            Результат запроса
        """
        # Используем SQL-запрос как ключ кеша
        result = self.cache.get(sql_query)
        
        if result != -1:  # Кеш-хит
            self.hits += 1
            print(f"Cache HIT: {sql_query[:30]}...")
            return result
        
        # Кеш-промах, выполняем запрос
        self.misses += 1
        print(f"Cache MISS: {sql_query[:30]}...")
        result = execute_query_func(sql_query)
        
        # Кешируем результат
        self.cache.put(sql_query, result)
        return result
    
    def get_stats(self):
        """Возвращает статистику производительности кеша."""
        total = self.hits + self.misses
        hit_ratio = self.hits / total if total > 0 else 0
        return {
            'hits': self.hits,
            'misses': self.misses,
            'total': total,
            'hit_ratio': hit_ratio
        }

# Имитация функции выполнения запроса к БД
def execute_db_query(query):
    print(f"Executing DB query: {query[:30]}...")
    # Имитация задержки БД
    import time
    time.sleep(0.2)
    # Имитация результата
    return [{"id": 1, "name": "John"}, {"id": 2, "name": "Jane"}]

# Использование кеша запросов
db_cache = DBQueryCache(capacity=10)

# Выполнение запросов
query1 = "SELECT * FROM users WHERE active = true"
query2 = "SELECT * FROM products WHERE category_id = 5"

# Первый запрос - кеш-промах
result1 = db_cache.query(query1, execute_db_query)
# Повторный запрос - кеш-хит
result1_again = db_cache.query(query1, execute_db_query)
# Другой запрос - кеш-промах
result2 = db_cache.query(query2, execute_db_query)

# Статистика кеша
print(f"Cache statistics: {db_cache.get_stats()}")
```

### 2. Кеширование HTTP-ответов в веб-приложении

```python
import time
import hashlib
import json

class APIResponseCache:
    """
    Кеш для ответов API с автоматическим истечением срока действия (TTL).
    """
    
    def __init__(self, capacity=100, default_ttl=300):
        """
        Инициализирует кеш ответов API.
        
        Args:
            capacity: Емкость кеша
            default_ttl: Время жизни записей в секундах по умолчанию
        """
        self.cache = {}  # {key: (value, timestamp, ttl)}
        self.capacity = capacity
        self.default_ttl = default_ttl
        # Используем LRU для политики вытеснения
        self.lru_keys = []
    
    def _generate_key(self, endpoint, params):
        """Генерирует ключ кеша на основе эндпоинта и параметров."""
        # Преобразуем параметры в стабильную строку для хеширования
        param_str = json.dumps(params, sort_keys=True) if params else ""
        key_data = f"{endpoint}:{param_str}"
        return hashlib.md5(key_data.encode()).hexdigest()
    
    def _update_lru(self, key):
        """Обновляет порядок LRU для ключа."""
        if key in self.lru_keys:
            self.lru_keys.remove(key)
        self.lru_keys.append(key)
    
    def get(self, endpoint, params=None):
        """
        Получает ответ из кеша, если он действителен.
        
        Args:
            endpoint: Эндпоинт API
            params: Параметры запроса
            
        Returns:
            Кешированный ответ или None, если кеш-промах или запись устарела
        """
        key = self._generate_key(endpoint, params)
        
        if key not in self.cache:
            return None
        
        value, timestamp, ttl = self.cache[key]
        current_time = time.time()
        
        # Проверяем, не устарела ли запись
        if current_time - timestamp > ttl:
            # Удаляем устаревшую запись
            del self.cache[key]
            self.lru_keys.remove(key)
            return None
        
        # Обновляем LRU-порядок
        self._update_lru(key)
        return value
    
    def put(self, endpoint, params, value, ttl=None):
        """
        Добавляет ответ в кеш.
        
        Args:
            endpoint: Эндпоинт API
            params: Параметры запроса
            value: Ответ для кеширования
            ttl: Время жизни записи в секундах (или None для значения по умолчанию)
        """
        key = self._generate_key(endpoint, params)
        ttl = ttl if ttl is not None else self.default_ttl
        
        # Если кеш заполнен, удаляем наименее недавно использованный элемент
        if len(self.cache) >= self.capacity and key not in self.cache:
            lru_key = self.lru_keys.pop(0)
            del self.cache[lru_key]
        
        # Сохраняем значение с текущим временем и TTL
        self.cache[key] = (value, time.time(), ttl)
        self._update_lru(key)
    
    def invalidate(self, endpoint, params=None):
        """
        Инвалидирует запись в кеше.
        
        Args:
            endpoint: Эндпоинт API
            params: Параметры запроса (или None для инвалидации всех записей для эндпоинта)
        """
        if params is not None:
            # Инвалидируем конкретный запрос
            key = self._generate_key(endpoint, params)
            if key in self.cache:
                del self.cache[key]
                self.lru_keys.remove(key)
        else:
            # Инвалидируем все записи для данного эндпоинта
            keys_to_remove = []
            for key in list(self.cache.keys()):
                # Проверяем, начинается ли ключ с хеша этого эндпоинта
                # Это грубое приближение, но для демонстрации подойдет
                endpoint_key = self._generate_key(endpoint, {})
                if endpoint_key[:len(endpoint)] == endpoint[:len(endpoint)]:
                    keys_to_remove.append(key)
            
            for key in keys_to_remove:
                del self.cache[key]
                if key in self.lru_keys:
                    self.lru_keys.remove(key)

# Пример использования в веб-приложении
# Имитация функции выполнения API-запроса
def fetch_api_data(endpoint, params=None):
    print(f"Fetching data from API: {endpoint} with params {params}")
    # Имитация задержки сети
    time.sleep(0.5)
    # Имитация ответа
    if endpoint == "/users":
        return ["User1", "User2", "User3"]
    elif endpoint == "/products":
        return [{"id": 1, "name": "Product A"}, {"id": 2, "name": "Product B"}]
    return {"data": "Some data"}

# Веб-обработчик с кешированием
def api_handler(request_endpoint, request_params=None, cache_ttl=60):
    # Инициализируем кеш (в реальном приложении это было бы сделано один раз)
    api_cache = APIResponseCache(capacity=100, default_ttl=300)
    
    # Пробуем получить данные из кеша
    cached_data = api_cache.get(request_endpoint, request_params)
    
    if cached_data is not None:
        print("Returning cached response")
        return cached_data
    
    # Если данных нет в кеше, выполняем запрос
    api_data = fetch_api_data(request_endpoint, request_params)
    
    # Кешируем полученные данные
    api_cache.put(request_endpoint, request_params, api_data, ttl=cache_ttl)
    
    return api_data

# Тестируем кеширование API-ответов
print("First request to /users:")
api_handler("/users")

print("\nSecond request to /users (should be cached):")
api_handler("/users")

print("\nRequest to /products:")
api_handler("/products", {"category": "electronics"})

print("\nAnother request to /products with same params (should be cached):")
api_handler("/products", {"category": "electronics"})
```

## Заключение

Кеширование является критически важной техникой оптимизации для веб-приложений. Выбор подходящего алгоритма кеширования зависит от характера вашего приложения и паттернов доступа к данным:

- **LRU** хорошо работает, когда недавно использованные элементы с высокой вероятностью будут использованы снова (временная локальность).
- **LFU** эффективен, когда некоторые элементы запрашиваются гораздо чаще других (частотная локальность).
- **Комбинированные алгоритмы** (например, ARC, 2Q) пытаются адаптироваться к изменяющимся паттернам доступа.

При внедрении кеширования в веб-приложения важно учитывать следующие аспекты:

1. **Инвалидация кеша** — определение политики обновления или удаления устаревших данных
2. **TTL (Time-To-Live)** — установка времени жизни для записей в кеше
3. **Консистентность** — обеспечение согласованности кешированных данных с исходными
4. **Мониторинг** — отслеживание эффективности кеша (hit rate, hit-miss ratio)
5. **Распределенное кеширование** — масштабирование кеша в распределенной среде (Redis, Memcached)

Правильно реализованные алгоритмы кеширования могут значительно повысить производительность веб-приложений и улучшить пользовательский опыт, особенно при работе с большими объемами данных или при ограниченных серверных ресурсах.
