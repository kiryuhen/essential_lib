# Алгоритмы на хеш-таблицах

Хеш-таблицы — это мощные структуры данных, которые обеспечивают быстрый доступ, вставку и удаление элементов. Они лежат в основе таких структур данных, как множества (sets) и словари (dictionaries), которые широко используются в программировании. В отличие от массивов и связных списков, хеш-таблицы позволяют получать доступ к данным по ключу, а не по индексу или последовательному перебору, что делает их чрезвычайно эффективными для многих задач.

## Основы хеш-таблиц

### Принцип работы хеш-таблицы

Хеш-таблица состоит из двух основных компонентов:

1. **Массив** (или таблица) фиксированного размера для хранения элементов
2. **Хеш-функция**, которая преобразует ключи в индексы этого массива

Процесс работы хеш-таблицы выглядит следующим образом:

1. Для данного ключа вычисляется хеш-значение с помощью хеш-функции
2. Хеш-значение преобразуется в индекс массива (обычно с помощью операции модуля по размеру массива)
3. По этому индексу в массиве хранится соответствующее значение

```
Индекс в таблице = hash(ключ) % размер_таблицы
```

### Коллизии и методы их разрешения

**Коллизия** происходит, когда два разных ключа дают одинаковый индекс в хеш-таблице. Существует несколько методов разрешения коллизий:

1. **Метод цепочек (chaining)**: В каждой ячейке хеш-таблицы хранится связный список элементов, имеющих одинаковый хеш. При коллизии новый элемент добавляется в этот список.

2. **Открытая адресация (open addressing)**: При коллизии ищется другая свободная ячейка по определенному правилу. Основные методы:
   - **Линейное пробирование**: Проверяются последовательные ячейки (`index + 1`, `index + 2`, ...) до нахождения свободной
   - **Квадратичное пробирование**: Проверяются ячейки с шагом, растущим квадратично (`index + 1²`, `index + 2²`, ...)
   - **Двойное хеширование**: Используется вторая хеш-функция для определения шага проверки

## Реализация хеш-таблицы с методом цепочек в Python

Рассмотрим реализацию простой хеш-таблицы с разрешением коллизий методом цепочек:

```python
class HashNode:
    """
    Класс для узла в хеш-таблице (пара ключ-значение).
    """
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.next = None
    
    def __str__(self):
        return f"{self.key}: {self.value}"

class HashTable:
    """
    Реализация хеш-таблицы с разрешением коллизий методом цепочек.
    """
    def __init__(self, capacity=10):
        self.capacity = capacity  # Емкость таблицы (количество корзин)
        self.size = 0             # Текущее количество элементов
        self.buckets = [None] * capacity  # Массив корзин
    
    def _hash(self, key):
        """
        Вычисляет хеш-значение для ключа.
        
        Args:
            key: Ключ для хеширования
            
        Returns:
            Индекс корзины для данного ключа
        """
        # Используем встроенную функцию hash() для вычисления хеша
        hash_value = hash(key)
        
        # Преобразуем хеш-значение в индекс массива
        return hash_value % self.capacity
    
    def put(self, key, value):
        """
        Добавляет или обновляет пару ключ-значение в хеш-таблице.
        
        Args:
            key: Ключ для добавления/обновления
            value: Значение для хранения
        """
        # Получаем индекс корзины для данного ключа
        index = self._hash(key)
        
        # Если корзина пуста, создаем новый узел
        if self.buckets[index] is None:
            self.buckets[index] = HashNode(key, value)
            self.size += 1
            return
        
        # Если корзина не пуста, проверяем, есть ли уже узел с таким ключом
        current = self.buckets[index]
        
        # Если ключ совпадает с ключом первого узла, обновляем значение
        if current.key == key:
            current.value = value
            return
        
        # Ищем узел с таким ключом в цепочке или последний узел в цепочке
        while current.next:
            if current.next.key == key:
                # Если нашли узел с таким ключом, обновляем значение
                current.next.value = value
                return
            current = current.next
        
        # Если не нашли узел с таким ключом, добавляем новый узел в конец цепочки
        current.next = HashNode(key, value)
        self.size += 1
    
    def get(self, key):
        """
        Получает значение по ключу.
        
        Args:
            key: Ключ для поиска
            
        Returns:
            Значение, соответствующее ключу, или None, если ключ не найден
        """
        # Получаем индекс корзины для данного ключа
        index = self._hash(key)
        
        # Если корзина пуста, ключ не найден
        if self.buckets[index] is None:
            return None
        
        # Ищем узел с таким ключом в цепочке
        current = self.buckets[index]
        while current:
            if current.key == key:
                return current.value
            current = current.next
        
        # Если не нашли, возвращаем None
        return None
    
    def remove(self, key):
        """
        Удаляет пару ключ-значение из хеш-таблицы.
        
        Args:
            key: Ключ для удаления
            
        Returns:
            True, если элемент успешно удален, иначе False
        """
        # Получаем индекс корзины для данного ключа
        index = self._hash(key)
        
        # Если корзина пуста, ключ не найден
        if self.buckets[index] is None:
            return False
        
        # Особый случай: удаление первого узла в цепочке
        if self.buckets[index].key == key:
            self.buckets[index] = self.buckets[index].next
            self.size -= 1
            return True
        
        # Ищем узел с таким ключом в цепочке
        current = self.buckets[index]
        while current.next:
            if current.next.key == key:
                # Если нашли, удаляем узел
                current.next = current.next.next
                self.size -= 1
                return True
            current = current.next
        
        # Если не нашли, возвращаем False
        return False
    
    def contains(self, key):
        """
        Проверяет, содержится ли ключ в хеш-таблице.
        
        Args:
            key: Ключ для проверки
            
        Returns:
            True, если ключ найден, иначе False
        """
        return self.get(key) is not None
    
    def get_size(self):
        """Возвращает количество элементов в хеш-таблице."""
        return self.size
    
    def is_empty(self):
        """Проверяет, пуста ли хеш-таблица."""
        return self.size == 0
    
    def get_keys(self):
        """
        Возвращает список всех ключей в хеш-таблице.
        
        Returns:
            Список ключей
        """
        keys = []
        
        for bucket in self.buckets:
            current = bucket
            while current:
                keys.append(current.key)
                current = current.next
        
        return keys
    
    def get_values(self):
        """
        Возвращает список всех значений в хеш-таблице.
        
        Returns:
            Список значений
        """
        values = []
        
        for bucket in self.buckets:
            current = bucket
            while current:
                values.append(current.value)
                current = current.next
        
        return values
    
    def __str__(self):
        """Возвращает строковое представление хеш-таблицы."""
        result = "{\n"
        
        for i, bucket in enumerate(self.buckets):
            if bucket:
                result += f"  Bucket {i}: "
                
                current = bucket
                while current:
                    result += f"{current.key}: {current.value}"
                    if current.next:
                        result += " -> "
                    current = current.next
                
                result += "\n"
        
        result += "}"
        return result

# Пример использования
hash_table = HashTable(5)  # Создаем хеш-таблицу с 5 корзинами

# Добавляем элементы
hash_table.put("apple", 10)
hash_table.put("banana", 20)
hash_table.put("cherry", 30)
hash_table.put("date", 40)
hash_table.put("elderberry", 50)

print("Хеш-таблица после добавления элементов:")
print(hash_table)
print(f"Размер: {hash_table.get_size()}")

# Получаем значения
print(f"Значение для ключа 'apple': {hash_table.get('apple')}")
print(f"Значение для ключа 'banana': {hash_table.get('banana')}")
print(f"Значение для ключа 'fig': {hash_table.get('fig')}")  # Отсутствующий ключ

# Обновляем значение
hash_table.put("apple", 15)
print(f"Обновленное значение для ключа 'apple': {hash_table.get('apple')}")

# Удаляем элемент
hash_table.remove("banana")
print("Хеш-таблица после удаления 'banana':")
print(hash_table)

# Получаем все ключи и значения
print("Ключи:", hash_table.get_keys())
print("Значения:", hash_table.get_values())
```

## Реализация хеш-таблицы с открытой адресацией в Python

Теперь рассмотрим реализацию хеш-таблицы с разрешением коллизий методом открытой адресации (линейное пробирование):

```python
class HashTableOpenAddressing:
    """
    Реализация хеш-таблицы с разрешением коллизий методом открытой адресации
    (линейное пробирование).
    """
    # Специальные маркеры для обозначения удаленных элементов
    class _DeletedNode:
        def __init__(self):
            pass
        
        def __str__(self):
            return "<DELETED>"
    
    _DELETED = _DeletedNode()  # Синглтон для обозначения удаленных элементов
    
    def __init__(self, capacity=10):
        self.capacity = capacity  # Емкость таблицы
        self.size = 0             # Текущее количество элементов
        self.keys = [None] * capacity   # Массив ключей
        self.values = [None] * capacity  # Массив значений
        self.load_factor_threshold = 0.7  # Порог коэффициента загрузки для ресайзинга
    
    def _hash(self, key):
        """
        Вычисляет хеш-значение для ключа.
        
        Args:
            key: Ключ для хеширования
            
        Returns:
            Индекс в таблице для данного ключа
        """
        return hash(key) % self.capacity
    
    def _find_slot(self, key):
        """
        Находит индекс для ключа в таблице.
        
        Args:
            key: Ключ для поиска
            
        Returns:
            (index, found): Индекс для ключа и флаг, найден ли ключ
        """
        # Находим начальный индекс для ключа
        index = self._hash(key)
        start_index = index
        first_deleted = -1
        
        # Ищем ключ или свободную ячейку
        while True:
            # Если ячейка пуста, ключ не найден
            if self.keys[index] is None:
                # Если встречался удаленный элемент, возвращаем его индекс
                if first_deleted != -1:
                    return (first_deleted, False)
                return (index, False)
            
            # Если ячейка помечена как удаленная и это первый удаленный элемент,
            # запоминаем его индекс
            if self.keys[index] is self._DELETED and first_deleted == -1:
                first_deleted = index
            
            # Если нашли ключ, возвращаем его индекс
            elif self.keys[index] is not self._DELETED and self.keys[index] == key:
                return (index, True)
            
            # Переходим к следующей ячейке (линейное пробирование)
            index = (index + 1) % self.capacity
            
            # Если обошли всю таблицу, ключ не найден
            if index == start_index:
                # Если встречался удаленный элемент, возвращаем его индекс
                if first_deleted != -1:
                    return (first_deleted, False)
                return (0, False)  # Таблица полна, что не должно происходить при правильном ресайзинге
    
    def _resize(self, new_capacity):
        """
        Изменяет размер хеш-таблицы.
        
        Args:
            new_capacity: Новая емкость таблицы
        """
        old_keys = self.keys
        old_values = self.values
        
        # Создаем новые массивы
        self.keys = [None] * new_capacity
        self.values = [None] * new_capacity
        self.capacity = new_capacity
        old_size = self.size
        self.size = 0
        
        # Переносим элементы в новую таблицу
        for i in range(len(old_keys)):
            if old_keys[i] is not None and old_keys[i] is not self._DELETED:
                self.put(old_keys[i], old_values[i])
        
        assert self.size == old_size, "Размер таблицы изменился после ресайзинга"
    
    def put(self, key, value):
        """
        Добавляет или обновляет пару ключ-значение в хеш-таблице.
        
        Args:
            key: Ключ для добавления/обновления
            value: Значение для хранения
        """
        # Проверяем, нужно ли изменить размер таблицы
        if self.size >= self.capacity * self.load_factor_threshold:
            self._resize(self.capacity * 2)
        
        # Находим индекс для ключа
        index, found = self._find_slot(key)
        
        # Если ключ уже существует, обновляем значение
        if found:
            self.values[index] = value
        else:
            # Иначе добавляем новую пару ключ-значение
            self.keys[index] = key
            self.values[index] = value
            self.size += 1
    
    def get(self, key):
        """
        Получает значение по ключу.
        
        Args:
            key: Ключ для поиска
            
        Returns:
            Значение, соответствующее ключу, или None, если ключ не найден
        """
        # Находим индекс для ключа
        index, found = self._find_slot(key)
        
        # Если ключ найден, возвращаем значение
        if found:
            return self.values[index]
        
        # Иначе возвращаем None
        return None
    
    def remove(self, key):
        """
        Удаляет пару ключ-значение из хеш-таблицы.
        
        Args:
            key: Ключ для удаления
            
        Returns:
            True, если элемент успешно удален, иначе False
        """
        # Находим индекс для ключа
        index, found = self._find_slot(key)
        
        # Если ключ найден, удаляем элемент
        if found:
            self.keys[index] = self._DELETED
            self.values[index] = None
            self.size -= 1
            return True
        
        # Иначе ключ не найден
        return False
    
    def contains(self, key):
        """
        Проверяет, содержится ли ключ в хеш-таблице.
        
        Args:
            key: Ключ для проверки
            
        Returns:
            True, если ключ найден, иначе False
        """
        _, found = self._find_slot(key)
        return found
    
    def get_size(self):
        """Возвращает количество элементов в хеш-таблице."""
        return self.size
    
    def is_empty(self):
        """Проверяет, пуста ли хеш-таблица."""
        return self.size == 0
    
    def get_keys(self):
        """
        Возвращает список всех ключей в хеш-таблице.
        
        Returns:
            Список ключей
        """
        keys = []
        
        for key in self.keys:
            if key is not None and key is not self._DELETED:
                keys.append(key)
        
        return keys
    
    def get_values(self):
        """
        Возвращает список всех значений в хеш-таблице.
        
        Returns:
            Список значений
        """
        values = []
        
        for i, key in enumerate(self.keys):
            if key is not None and key is not self._DELETED:
                values.append(self.values[i])
        
        return values
    
    def __str__(self):
        """Возвращает строковое представление хеш-таблицы."""
        result = "{\n"
        
        for i in range(self.capacity):
            if self.keys[i] is not None:
                if self.keys[i] is self._DELETED:
                    result += f"  {i}: <DELETED>\n"
                else:
                    result += f"  {i}: {self.keys[i]}: {self.values[i]}\n"
        
        result += "}"
        return result

# Пример использования
hash_table = HashTableOpenAddressing(5)  # Создаем хеш-таблицу с 5 ячейками

# Добавляем элементы
hash_table.put("apple", 10)
hash_table.put("banana", 20)
hash_table.put("cherry", 30)
hash_table.put("date", 40)
hash_table.put("elderberry", 50)  # Это должно вызвать ресайзинг

print("Хеш-таблица после добавления элементов:")
print(hash_table)
print(f"Размер: {hash_table.get_size()}")

# Получаем значения
print(f"Значение для ключа 'apple': {hash_table.get('apple')}")
print(f"Значение для ключа 'banana': {hash_table.get('banana')}")
print(f"Значение для ключа 'fig': {hash_table.get('fig')}")  # Отсутствующий ключ

# Обновляем значение
hash_table.put("apple", 15)
print(f"Обновленное значение для ключа 'apple': {hash_table.get('apple')}")

# Удаляем элемент
hash_table.remove("banana")
print("Хеш-таблица после удаления 'banana':")
print(hash_table)

# Добавляем новый элемент (должен занять удаленную ячейку)
hash_table.put("fig", 60)
print("Хеш-таблица после добавления 'fig':")
print(hash_table)

# Получаем все ключи и значения
print("Ключи:", hash_table.get_keys())
print("Значения:", hash_table.get_values())
```

## Хеш-функции

Хорошая хеш-функция должна обладать следующими свойствами:

1. **Детерминированность**: для одного и того же ключа всегда должен возвращаться один и тот же хеш-код
2. **Равномерное распределение**: хеш-коды должны быть равномерно распределены по всему диапазону возможных значений
3. **Эффективность**: вычисление хеш-кода должно быть быстрым
4. **Лавинный эффект**: небольшие изменения в ключе должны приводить к значительным изменениям в хеш-коде

### Примеры хеш-функций

#### 1. Хеширование строк методом сложения (простой, но не очень эффективный)

```python
def simple_hash(string, table_size):
    """
    Простая хеш-функция, суммирующая коды ASCII символов.
    
    Args:
        string: Строка для хеширования
        table_size: Размер хеш-таблицы
        
    Returns:
        Хеш-код (индекс в таблице)
    """
    hash_value = 0
    
    for char in string:
        hash_value += ord(char)
    
    return hash_value % table_size
```

#### 2. Полиномиальное хеширование (метод Горнера)

```python
def polynomial_hash(string, table_size, prime=31):
    """
    Полиномиальная хеш-функция.
    
    Args:
        string: Строка для хеширования
        table_size: Размер хеш-таблицы
        prime: Простое число для улучшения распределения
        
    Returns:
        Хеш-код (индекс в таблице)
    """
    hash_value = 0
    
    for char in string:
        hash_value = (hash_value * prime + ord(char)) % table_size
    
    return hash_value
```

#### 3. FNV-1a (быстрый алгоритм хеширования для строк)

```python
def fnv1a_hash(string, table_size):
    """
    Реализация алгоритма хеширования FNV-1a.
    
    Args:
        string: Строка для хеширования
        table_size: Размер хеш-таблицы
        
    Returns:
        Хеш-код (индекс в таблице)
    """
    # Константы FNV-1a для 32-битных хешей
    FNV_PRIME = 16777619
    OFFSET_BASIS = 2166136261
    
    hash_value = OFFSET_BASIS
    
    for char in string.encode('utf-8'):
        hash_value ^= char
        hash_value = (hash_value * FNV_PRIME) % (2**32)
    
    return hash_value % table_size
```

### Тестирование качества хеш-функций

```python
import random
import string
import matplotlib.pyplot as plt

def generate_random_string(length):
    """Генерирует случайную строку заданной длины."""
    return ''.join(random.choice(string.ascii_letters) for _ in range(length))

def test_hash_function(hash_func, table_size, num_strings=10000, string_length=10):
    """
    Тестирует качество хеш-функции, измеряя распределение хеш-кодов.
    
    Args:
        hash_func: Функция хеширования для тестирования
        table_size: Размер хеш-таблицы
        num_strings: Количество тестовых строк
        string_length: Длина каждой тестовой строки
        
    Returns:
        Словарь с распределением хеш-кодов
    """
    distribution = {i: 0 for i in range(table_size)}
    
    for _ in range(num_strings):
        s = generate_random_string(string_length)
        hash_code = hash_func(s, table_size)
        distribution[hash_code] += 1
    
    return distribution

def plot_hash_distribution(hash_funcs, table_size, num_strings=10000):
    """
    Строит диаграммы распределения хеш-кодов для различных хеш-функций.
    
    Args:
        hash_funcs: Словарь {имя_функции: функция}
        table_size: Размер хеш-таблицы
        num_strings: Количество тестовых строк
    """
    fig, axes = plt.subplots(len(hash_funcs), 1, figsize=(10, 5*len(hash_funcs)))
    
    for i, (name, func) in enumerate(hash_funcs.items()):
        distribution = test_hash_function(func, table_size, num_strings)
        
        # Вычисляем статистику
        values = list(distribution.values())
        max_collisions = max(values)
        mean_collisions = sum(values) / len(values)
        empty_slots = values.count(0)
        
        ax = axes[i] if len(hash_funcs) > 1 else axes
        ax.bar(range(table_size), values)
        ax.set_title(f"{name}: Распределение хеш-кодов\n"
                     f"Макс. коллизий: {max_collisions}, "
                     f"Средн. коллизий: {mean_collisions:.2f}, "
                     f"Пустых ячеек: {empty_slots}")
        ax.set_xlabel("Хеш-код")
        ax.set_ylabel("Количество коллизий")
    
    plt.tight_layout()
    plt.show()

# Тестируем хеш-функции
table_size = 100
hash_functions = {
    "Простое сложение": simple_hash,
    "Полиномиальное хеширование": polynomial_hash,
    "FNV-1a": fnv1a_hash
}

plot_hash_distribution(hash_functions, table_size)
```

## Распространенные алгоритмы и задачи с использованием хеш-таблиц

### 1. Поиск пары с заданной суммой

Задача: Для заданного массива чисел и целевой суммы найти пару элементов, сумма которых равна целевой.

```python
def find_pair_with_sum(arr, target_sum):
    """
    Находит пару чисел в массиве, сумма которых равна заданной.
    
    Args:
        arr: Массив чисел
        target_sum: Целевая сумма
        
    Returns:
        Кортеж (num1, num2), если пара найдена, иначе None
    """
    # Используем хеш-таблицу для хранения чисел, которые мы уже видели
    seen = {}
    
    for num in arr:
        # Вычисляем комплемент (число, которое в паре с текущим даст целевую сумму)
        complement = target_sum - num
        
        # Если комплемент уже встречался, мы нашли пару
        if complement in seen:
            return (complement, num)
        
        # Иначе добавляем текущее число в хеш-таблицу
        seen[num] = True
    
    # Если пара не найдена
    return None

# Пример использования
arr = [8, 4, 2, 5, 1, 9, 3]
target_sum = 7

pair = find_pair_with_sum(arr, target_sum)
if pair:
    print(f"Найдена пара: {pair[0]} + {pair[1]} = {target_sum}")
else:
    print(f"Пара с суммой {target_sum} не найдена")
```

### 2. Поиск наиболее частого элемента

Задача: Найти элемент, который встречается в массиве наиболее часто.

```python
def find_most_frequent_element(arr):
    """
    Находит элемент, который встречается наиболее часто.
    
    Args:
        arr: Массив элементов
        
    Returns:
        Кортеж (element, frequency) - наиболее частый элемент и его частота
    """
    # Используем хеш-таблицу для подсчета частоты каждого элемента
    frequency = {}
    
    for element in arr:
        if element in frequency:
            frequency[element] += 1
        else:
            frequency[element] = 1
    
    # Находим элемент с максимальной частотой
    max_frequency = 0
    most_frequent = None
    
    for element, count in frequency.items():
        if count > max_frequency:
            max_frequency = count
            most_frequent = element
    
    return (most_frequent, max_frequency)

# Пример использования
arr = [1, 3, 2, 1, 4, 1, 5, 7, 5, 1, 9]

element, frequency = find_most_frequent_element(arr)
print(f"Наиболее частый элемент: {element} (встречается {frequency} раз)")
```

### 3. Проверка наличия анаграммы в строке

Задача: Определить, содержит ли строка анаграмму заданного слова.

```python
def contains_anagram(text, word):
    """
    Проверяет, содержит ли строка анаграмму заданного слова.
    
    Args:
        text: Исходная строка
        word: Слово для поиска анаграмм
        
    Returns:
        True, если анаграмма найдена, иначе False
    """
    if not text or not word or len(text) < len(word):
        return False
    
    # Подсчитываем частоту каждого символа в искомом слове
    word_freq = {}
    for char in word:
        if char in word_freq:
            word_freq[char] += 1
        else:
            word_freq[char] = 1
    
    # Размер окна равен длине слова
    window_size = len(word)
    
    # Подсчитываем частоту символов в первом окне текста
    window_freq = {}
    for i in range(window_size):
        if i >= len(text):
            break
        char = text[i]
        if char in window_freq:
            window_freq[char] += 1
        else:
            window_freq[char] = 1
    
    # Проверяем, является ли первое окно анаграммой
    if word_freq == window_freq:
        return True
    
    # Скользящее окно для проверки остальных подстрок
    for i in range(window_size, len(text)):
        # Добавляем новый символ в окно
        new_char = text[i]
        if new_char in window_freq:
            window_freq[new_char] += 1
        else:
            window_freq[new_char] = 1
        
        # Удаляем символ, который выходит из окна
        old_char = text[i - window_size]
        window_freq[old_char] -= 1
        
        # Если частота стала нулевой, удаляем символ из словаря
        if window_freq[old_char] == 0:
            del window_freq[old_char]
        
        # Проверяем, является ли текущее окно анаграммой
        if word_freq == window_freq:
            return True
    
    return False

# Пример использования
text = "abcbaedcbafgabc"
word = "abc"

if contains_anagram(text, word):
    print(f"Строка '{text}' содержит анаграмму слова '{word}'")
else:
    print(f"Строка '{text}' не содержит анаграмму слова '{word}'")
```

### 4. Поиск первого неповторяющегося символа

Задача: Найти первый символ в строке, который не повторяется.

```python
def first_non_repeating_char(string):
    """
    Находит первый неповторяющийся символ в строке.
    
    Args:
        string: Исходная строка
        
    Returns:
        Первый неповторяющийся символ или None, если такого нет
    """
    # Подсчитываем частоту каждого символа
    char_freq = {}
    
    for char in string:
        if char in char_freq:
            char_freq[char] += 1
        else:
            char_freq[char] = 1
    
    # Ищем первый символ с частотой 1
    for char in string:
        if char_freq[char] == 1:
            return char
    
    # Если все символы повторяются
    return None

# Пример использования
string = "abacddbec"

char = first_non_repeating_char(string)
if char:
    print(f"Первый неповторяющийся символ: '{char}'")
else:
    print("В строке нет неповторяющихся символов")
```

### 5. Пересечение двух массивов

Задача: Найти пересечение двух массивов (элементы, которые присутствуют в обоих массивах).

```python
def intersection(arr1, arr2):
    """
    Находит пересечение двух массивов.
    
    Args:
        arr1: Первый массив
        arr2: Второй массив
        
    Returns:
        Список элементов, присутствующих в обоих массивах
    """
    # Используем хеш-таблицу для быстрого поиска
    set1 = set(arr1)
    
    # Находим элементы, которые присутствуют в обоих массивах
    result = []
    
    for element in arr2:
        if element in set1:
            result.append(element)
            # Удаляем элемент из множества, чтобы избежать дубликатов
            set1.remove(element)
    
    return result

# Пример использования
arr1 = [1, 2, 3, 4, 5, 6]
arr2 = [4, 5, 6, 7, 8, 9]

common_elements = intersection(arr1, arr2)
print(f"Пересечение массивов: {common_elements}")
```

### 6. Группировка анаграмм

Задача: Сгруппировать слова в списке так, чтобы анаграммы находились в одной группе.

```python
def group_anagrams(words):
    """
    Группирует слова так, чтобы анаграммы находились в одной группе.
    
    Args:
        words: Список слов
        
    Returns:
        Список групп анаграмм
    """
    # Используем хеш-таблицу для группировки анаграмм
    anagram_groups = {}
    
    for word in words:
        # Сортируем символы слова, чтобы получить уникальный ключ для всех анаграмм
        sorted_word = ''.join(sorted(word))
        
        # Добавляем слово в соответствующую группу
        if sorted_word in anagram_groups:
            anagram_groups[sorted_word].append(word)
        else:
            anagram_groups[sorted_word] = [word]
    
    # Преобразуем словарь в список групп
    result = list(anagram_groups.values())
    
    return result

# Пример использования
words = ["eat", "tea", "tan", "ate", "nat", "bat"]

groups = group_anagrams(words)
print("Группы анаграмм:")
for i, group in enumerate(groups):
    print(f"Группа {i+1}: {group}")
```

### 7. Проверка на дубликаты

Задача: Проверить, содержит ли массив дубликаты.

```python
def contains_duplicate(arr):
    """
    Проверяет, содержит ли массив дубликаты.
    
    Args:
        arr: Массив элементов
        
    Returns:
        True, если массив содержит дубликаты, иначе False
    """
    # Используем хеш-таблицу для отслеживания уникальных элементов
    seen = set()
    
    for element in arr:
        if element in seen:
            return True
        seen.add(element)
    
    return False

# Пример использования
arr1 = [1, 2, 3, 4, 5]
arr2 = [1, 2, 3, 2, 5]

print(f"Массив {arr1} содержит дубликаты: {contains_duplicate(arr1)}")
print(f"Массив {arr2} содержит дубликаты: {contains_duplicate(arr2)}")
```

## Практические применения хеш-таблиц

### 1. Система кэширования

Реализуем простую систему кэширования с ограничением размера и политикой вытеснения LRU (Least Recently Used).

```python
class LRUCache:
    """
    Реализация LRU-кэша (Least Recently Used) с использованием
    хеш-таблицы и двусвязного списка.
    """
    class _Node:
        """Внутренний класс для узла кэша."""
        def __init__(self, key, value):
            self.key = key
            self.value = value
            self.prev = None
            self.next = None
    
    def __init__(self, capacity):
        """
        Инициализирует кэш с заданной емкостью.
        
        Args:
            capacity: Максимальное количество элементов в кэше
        """
        self.capacity = capacity
        self.size = 0
        self.cache = {}  # Хеш-таблица: ключ -> узел
        
        # Создаем фиктивные узлы для головы и хвоста двусвязного списка
        self.head = self._Node(0, 0)  # Самый новый элемент после головы
        self.tail = self._Node(0, 0)  # Самый старый элемент перед хвостом
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _add_node(self, node):
        """
        Добавляет узел после головы списка (самый свежий).
        """
        node.prev = self.head
        node.next = self.head.next
        
        self.head.next.prev = node
        self.head.next = node
    
    def _remove_node(self, node):
        """
        Удаляет узел из списка.
        """
        prev_node = node.prev
        next_node = node.next
        
        prev_node.next = next_node
        next_node.prev = prev_node
    
    def _move_to_head(self, node):
        """
        Перемещает узел в начало списка (отмечает как недавно использованный).
        """
        self._remove_node(node)
        self._add_node(node)
    
    def _pop_tail(self):
        """
        Удаляет и возвращает последний узел списка (наименее недавно использованный).
        """
        node = self.tail.prev
        self._remove_node(node)
        return node
    
    def get(self, key):
        """
        Получает значение по ключу и отмечает его как недавно использованное.
        
        Args:
            key: Ключ для поиска
            
        Returns:
            Значение, соответствующее ключу, или -1, если ключ не найден
        """
        if key not in self.cache:
            return -1
        
        # Перемещаем узел в начало списка (отмечаем как недавно использованный)
        node = self.cache[key]
        self._move_to_head(node)
        
        return node.value
    
    def put(self, key, value):
        """
        Добавляет или обновляет значение по ключу.
        
        Args:
            key: Ключ для добавления/обновления
            value: Значение для хранения
        """
        # Если ключ уже существует, обновляем значение и перемещаем узел в начало
        if key in self.cache:
            node = self.cache[key]
            node.value = value
            self._move_to_head(node)
            return
        
        # Создаем новый узел
        node = self._Node(key, value)
        self.cache[key] = node
        self._add_node(node)
        self.size += 1
        
        # Если превышена емкость, удаляем наименее недавно использованный узел
        if self.size > self.capacity:
            tail = self._pop_tail()
            del self.cache[tail.key]
            self.size -= 1
    
    def __str__(self):
        """Возвращает строковое представление кэша."""
        items = []
        current = self.head.next
        
        while current != self.tail:
            items.append(f"{current.key}: {current.value}")
            current = current.next
        
        return "{" + ", ".join(items) + "}"

# Пример использования
cache = LRUCache(3)  # Кэш вместимостью 3 элемента

# Добавляем элементы
cache.put(1, "Value 1")
cache.put(2, "Value 2")
cache.put(3, "Value 3")

print("Кэш после добавления трех элементов:")
print(cache)

# Получаем значение по ключу 1 (перемещает его в начало)
print(f"Получаем значение по ключу 1: {cache.get(1)}")
print("Кэш после получения значения по ключу 1:")
print(cache)

# Добавляем новый элемент, должен вытеснить наименее недавно использованный (ключ 2)
cache.put(4, "Value 4")
print("Кэш после добавления четвертого элемента:")
print(cache)

# Пытаемся получить вытесненный элемент
print(f"Получаем значение по ключу 2: {cache.get(2)}")
```

### 2. Система подсчета уникальных посетителей (HyperLogLog)

HyperLogLog — это алгоритм для приблизительного подсчета уникальных элементов в потоке данных с использованием очень малого объема памяти.

```python
import hashlib
import math

class HyperLogLog:
    """
    Реализация алгоритма HyperLogLog для приблизительного подсчета
    уникальных элементов в большом наборе данных.
    """
    def __init__(self, precision=10):
        """
        Инициализирует структуру HyperLogLog.
        
        Args:
            precision: Количество бит, используемых для определения регистра (4-16)
        """
        if precision < 4 or precision > 16:
            raise ValueError("Precision must be between 4 and 16")
        
        self.precision = precision
        self.num_registers = 1 << precision  # 2^precision
        self.registers = [0] * self.num_registers
        self.alpha = self._get_alpha(self.num_registers)
    
    def _get_alpha(self, m):
        """
        Определяет константу нормализации.
        
        Args:
            m: Количество регистров
        """
        if m == 16:
            return 0.673
        elif m == 32:
            return 0.697
        elif m == 64:
            return 0.709
        else:
            return 0.7213 / (1 + 1.079 / m)
    
    def _hash(self, value):
        """
        Хеширует значение и возвращает 32-битный хеш.
        
        Args:
            value: Значение для хеширования
        """
        h = hashlib.md5(str(value).encode('utf-8')).hexdigest()
        return int(h, 16) & 0xFFFFFFFF  # 32-битный хеш
    
    def add(self, value):
        """
        Добавляет значение в HyperLogLog.
        
        Args:
            value: Значение для добавления
        """
        # Вычисляем хеш значения
        x = self._hash(value)
        
        # Используем первые precision бит для определения индекса регистра
        j = x & (self.num_registers - 1)
        
        # Используем оставшиеся биты для определения ранга
        w = x >> self.precision
        
        # Определяем позицию первой 1 в w (ранг)
        rank = 1
        while (w & 1) == 0 and rank <= 32:
            w >>= 1
            rank += 1
        
        # Обновляем регистр, если новый ранг больше текущего
        self.registers[j] = max(self.registers[j], rank)
    
    def count(self):
        """
        Оценивает количество уникальных элементов.
        
        Returns:
            Приблизительное количество уникальных элементов
        """
        # Вычисляем гармоническое среднее значений 2^(-регистр)
        sum_inv = 0
        for value in self.registers:
            sum_inv += 2 ** (-value)
        
        # Применяем формулу оценки
        E = self.alpha * self.num_registers ** 2 / sum_inv
        
        # Коррекции для малых и больших значений
        if E <= 2.5 * self.num_registers:
            # Коррекция для малых значений
            zeros = self.registers.count(0)
            if zeros > 0:
                E = self.num_registers * math.log(self.num_registers / zeros)
        
        # Коррекция для больших значений не требуется в этой упрощенной реализации
        
        return int(E)

# Пример использования
hll = HyperLogLog(precision=10)  # Используем 2^10 = 1024 регистра

# Добавляем элементы
for i in range(1000):
    hll.add(f"user_{i}")

# Добавляем дубликаты (не должны влиять на результат)
for i in range(500):
    hll.add(f"user_{i}")

# Добавляем еще элементы
for i in range(1000, 2000):
    hll.add(f"user_{i}")

# Оцениваем количество уникальных элементов
estimated_count = hll.count()
real_count = 2000

print(f"Реальное количество уникальных элементов: {real_count}")
print(f"Оценка HyperLogLog: {estimated_count}")
print(f"Точность: {100 * (1 - abs(estimated_count - real_count) / real_count):.2f}%")
```

### 3. Фильтр Блума

Фильтр Блума — это вероятностная структура данных, которая позволяет эффективно проверять, принадлежит ли элемент множеству, с возможностью ложноположительных срабатываний, но без ложноотрицательных.

```python
import hashlib
import math

class BloomFilter:
    """
    Реализация фильтра Блума для эффективной проверки принадлежности
    элемента множеству с возможностью ложноположительных срабатываний.
    """
    def __init__(self, capacity, error_rate=0.001):
        """
        Инициализирует фильтр Блума.
        
        Args:
            capacity: Ожидаемое количество элементов
            error_rate: Допустимая вероятность ложноположительных срабатываний
        """
        # Вычисляем оптимальный размер битового массива
        self.size = self._optimal_size(capacity, error_rate)
        
        # Вычисляем оптимальное количество хеш-функций
        self.hash_count = self._optimal_hash_count(self.size, capacity)
        
        # Инициализируем битовый массив
        self.bit_array = [False] * self.size
        self.capacity = capacity
        self.error_rate = error_rate
        self.count = 0
    
    def _optimal_size(self, n, p):
        """
        Вычисляет оптимальный размер битового массива.
        
        Args:
            n: Ожидаемое количество элементов
            p: Допустимая вероятность ложноположительных срабатываний
            
        Returns:
            Оптимальный размер битового массива
        """
        # m = -n * ln(p) / (ln(2)^2)
        return int(-n * math.log(p) / (math.log(2) ** 2))
    
    def _optimal_hash_count(self, m, n):
        """
        Вычисляет оптимальное количество хеш-функций.
        
        Args:
            m: Размер битового массива
            n: Ожидаемое количество элементов
            
        Returns:
            Оптимальное количество хеш-функций
        """
        # k = (m/n) * ln(2)
        return max(1, int(m / n * math.log(2)))
    
    def _get_hash_values(self, value):
        """
        Генерирует различные хеш-значения для элемента.
        
        Args:
            value: Элемент для хеширования
            
        Returns:
            Список хеш-значений
        """
        # Преобразуем значение в строку
        value_str = str(value).encode('utf-8')
        
        # Используем два хеша для генерации нескольких хеш-функций
        h1 = int(hashlib.md5(value_str).hexdigest(), 16)
        h2 = int(hashlib.sha1(value_str).hexdigest(), 16)
        
        return [(h1 + i * h2) % self.size for i in range(self.hash_count)]
    
    def add(self, value):
        """
        Добавляет элемент в фильтр Блума.
        
        Args:
            value: Элемент для добавления
        """
        # Получаем хеш-значения
        hash_values = self._get_hash_values(value)
        
        # Устанавливаем соответствующие биты
        for hash_value in hash_values:
            self.bit_array[hash_value] = True
        
        self.count += 1
    
    def contains(self, value):
        """
        Проверяет, может ли элемент содержаться в фильтре.
        
        Args:
            value: Элемент для проверки
            
        Returns:
            True, если элемент может содержаться в фильтре, иначе False
            (возможны ложноположительные срабатывания)
        """
        # Если фильтр пуст, элемент точно не содержится
        if self.count == 0:
            return False
        
        # Получаем хеш-значения
        hash_values = self._get_hash_values(value)
        
        # Проверяем, установлены ли все соответствующие биты
        return all(self.bit_array[hash_value] for hash_value in hash_values)
    
    def current_error_rate(self):
        """
        Вычисляет текущую вероятность ложноположительных срабатываний.
        
        Returns:
            Вероятность ложноположительных срабатываний
        """
        # p = (1 - e^(-k*n/m))^k
        return (1 - math.exp(-self.hash_count * self.count / self.size)) ** self.hash_count
    
    def __str__(self):
        """Возвращает строковое представление фильтра Блума."""
        return (f"BloomFilter(size={self.size}, hash_count={self.hash_count}, "
                f"capacity={self.capacity}, error_rate={self.error_rate:.6f}, "
                f"current_error_rate={self.current_error_rate():.6f}, "
                f"count={self.count})")

# Пример использования
bloom = BloomFilter(capacity=10000, error_rate=0.01)

# Добавляем элементы в фильтр
added_elements = [f"element_{i}" for i in range(1000)]
for element in added_elements:
    bloom.add(element)

print(bloom)

# Проверяем элементы, которые точно добавлены
for i in range(10):
    element = added_elements[i]
    print(f"Содержится ли '{element}' в фильтре? {bloom.contains(element)}")

# Проверяем элементы, которые точно не добавлены
for i in range(10):
    element = f"nonexistent_{i}"
    print(f"Содержится ли '{element}' в фильтре? {bloom.contains(element)}")

# Статистика ложноположительных срабатываний
false_positives = 0
test_count = 10000

for i in range(test_count):
    element = f"nonexistent_{i}"
    if bloom.contains(element):
        false_positives += 1

actual_error_rate = false_positives / test_count
print(f"Фактическая частота ложноположительных срабатываний: {actual_error_rate:.6f}")
print(f"Теоретическая вероятность ложноположительных срабатываний: {bloom.current_error_rate():.6f}")
```

### 4. Счетчик словарных слов в тексте

Разработаем систему для подсчета частоты слов в тексте с использованием хеш-таблицы.

```python
import re
import string
from collections import Counter

def count_words(text, stop_words=None):
    """
    Подсчитывает частоту слов в тексте.
    
    Args:
        text: Исходный текст
        stop_words: Множество стоп-слов (слов, которые следует игнорировать)
        
    Returns:
        Словарь {слово: частота}
    """
    if stop_words is None:
        stop_words = set()
    
    # Приводим текст к нижнему регистру
    text = text.lower()
    
    # Удаляем знаки препинания и заменяем их пробелами
    translator = str.maketrans(string.punctuation, ' ' * len(string.punctuation))
    text = text.translate(translator)
    
    # Разбиваем текст на слова
    words = text.split()
    
    # Подсчитываем частоту слов, игнорируя стоп-слова
    word_counts = {}
    
    for word in words:
        if word and word not in stop_words:
            if word in word_counts:
                word_counts[word] += 1
            else:
                word_counts[word] = 1
    
    return word_counts

def get_top_words(word_counts, n=10):
    """
    Возвращает n наиболее часто встречающихся слов.
    
    Args:
        word_counts: Словарь {слово: частота}
        n: Количество слов для возврата
        
    Returns:
        Список кортежей (слово, частота) в порядке убывания частоты
    """
    # Используем Counter для сортировки по частоте
    counter = Counter(word_counts)
    return counter.most_common(n)

# Пример использования
text = """
Хеш-таблица — это структура данных, которая реализует интерфейс словаря, 
в котором можно хранить пары ключ-значение. Хеш-таблица использует 
хеш-функцию для вычисления индекса в массиве, в котором может быть найдено
нужное значение. Хеш-функция преобразует ключ в индекс массива.

Хеш-таблицы обеспечивают быстрый доступ к данным по ключу, 
что делает их идеальными для реализации словарей. Однако они могут 
страдать от коллизий, когда разные ключи дают одинаковый хеш-код. 
Существует несколько методов разрешения коллизий, включая метод цепочек и 
открытую адресацию.
"""

# Определяем стоп-слова (в реальном приложении обычно используется больший список)
stop_words = {"это", "в", "и", "на", "по", "с", "от", "для", "не", "к", "может", "быть"}

# Подсчитываем частоту слов
word_counts = count_words(text, stop_words)

# Получаем топ-10 наиболее часто встречающихся слов
top_words = get_top_words(word_counts, 10)

print("Топ-10 наиболее часто встречающихся слов:")
for word, count in top_words:
    print(f"{word}: {count}")
```

## Преимущества и недостатки хеш-таблиц

### Преимущества:

1. **Быстрый доступ по ключу**: Операции поиска, вставки и удаления имеют в среднем временную сложность O(1).

2. **Гибкость ключей**: В качестве ключей могут использоваться различные типы данных (строки, числа, кортежи и т.д.), если для них определена хеш-функция.

3. **Эффективное использование для поиска**: Хеш-таблицы идеальны для задач поиска, когда ключи известны заранее.

4. **Простота реализации**: Базовую хеш-таблицу относительно легко реализовать.

### Недостатки:

1. **Возможность коллизий**: Разные ключи могут давать одинаковые хеш-коды, что требует дополнительных механизмов разрешения коллизий.

2. **Неупорядоченность**: Хеш-таблицы не сохраняют порядок элементов. Если порядок важен, требуются дополнительные структуры данных.

3. **Изменчивость производительности**: Производительность может значительно ухудшиться при плохой хеш-функции или большом количестве коллизий.

4. **Требование к хеш-функции**: Эффективная хеш-функция должна распределять ключи равномерно и быстро вычисляться.

5. **Расход памяти**: Хеш-таблицы обычно требуют больше памяти, чем другие структуры данных, особенно при низкой загрузке.

## Заключение

Хеш-таблицы — это мощные структуры данных, которые обеспечивают высокую производительность для операций поиска, вставки и удаления. Они широко используются в программировании для реализации словарей, множеств, кэшей и многих других приложений.

В этом разделе мы рассмотрели принципы работы хеш-таблиц, различные методы разрешения коллизий, реализации на Python и множество алгоритмов и практических задач, которые можно эффективно решать с помощью хеш-таблиц.

Понимание хеш-таблиц и связанных с ними алгоритмов является фундаментальным навыком для любого программиста, который хочет писать эффективный и производительный код.

---

В следующем разделе мы изучим алгоритмы на деревьях, еще одной важной структуре данных, которая находит широкое применение в программировании и имеет множество разновидностей для различных задач.