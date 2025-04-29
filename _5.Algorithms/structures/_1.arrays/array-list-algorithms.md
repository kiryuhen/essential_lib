# Операции с массивами/списками

Массивы (arrays) и списки (lists) являются одними из самых фундаментальных структур данных в программировании. В Python термин "список" (list) используется для структуры данных, которая в других языках часто называется динамическим массивом. В этом разделе мы рассмотрим основные операции с массивами/списками и алгоритмы, которые часто применяются при работе с этими структурами данных.

## Основные операции и их сложность

Прежде чем перейти к алгоритмам, важно понимать сложность основных операций со списками в Python:

| Операция | Временная сложность | Описание |
|----------|---------------------|----------|
| Доступ по индексу: `arr[i]` | O(1) | Получение или изменение элемента по индексу |
| Поиск: `elem in arr` | O(n) | Проверка наличия элемента в списке |
| Вставка в конец: `arr.append(elem)` | O(1)* | Добавление элемента в конец списка |
| Вставка в начало: `arr.insert(0, elem)` | O(n) | Добавление элемента в начало списка |
| Вставка в середину: `arr.insert(i, elem)` | O(n) | Добавление элемента в произвольную позицию |
| Удаление с конца: `arr.pop()` | O(1) | Удаление последнего элемента |
| Удаление из начала: `arr.pop(0)` | O(n) | Удаление первого элемента |
| Удаление из середины: `arr.pop(i)` | O(n) | Удаление элемента по индексу |
| Длина списка: `len(arr)` | O(1) | Получение количества элементов |
| Срез: `arr[i:j]` | O(j-i) | Создание среза из списка |
| Конкатенация: `arr1 + arr2` | O(len(arr2)) | Объединение двух списков |
| Сортировка: `arr.sort()` или `sorted(arr)` | O(n log n) | Сортировка списка |

\* Амортизированная сложность. В редких случаях может потребоваться перевыделение памяти с копированием всех элементов.

## Алгоритмы и техники для работы с массивами/списками

### 1. Техника двух указателей (Two Pointers)

Эта техника использует два указателя для обхода массива, обычно с разных концов или с разной скоростью.

#### Пример 1: Поиск пары с заданной суммой в отсортированном массиве

```python
def find_pair_with_sum(arr, target_sum):
    """
    Находит пару чисел в отсортированном массиве, сумма которых равна target_sum.
    
    Args:
        arr: Отсортированный список чисел
        target_sum: Целевая сумма
        
    Returns:
        Кортеж индексов (i, j), если пара найдена, иначе None
    """
    left = 0  # Указатель слева (начало массива)
    right = len(arr) - 1  # Указатель справа (конец массива)
    
    while left < right:
        current_sum = arr[left] + arr[right]
        
        if current_sum == target_sum:
            return (left, right)  # Пара найдена
        elif current_sum < target_sum:
            left += 1  # Увеличиваем сумму, двигая левый указатель вправо
        else:
            right -= 1  # Уменьшаем сумму, двигая правый указатель влево
    
    return None  # Пара не найдена

# Пример использования
sorted_array = [1, 3, 5, 7, 9, 11, 13]
target = 16

pair = find_pair_with_sum(sorted_array, target)
if pair:
    i, j = pair
    print(f"Найдена пара: {sorted_array[i]} + {sorted_array[j]} = {target}")
else:
    print(f"Пара с суммой {target} не найдена")
```

Временная сложность: O(n), где n - длина массива.
Пространственная сложность: O(1), используется константное количество дополнительной памяти.

#### Пример 2: Удаление дубликатов из отсортированного массива "на месте"

```python
def remove_duplicates(arr):
    """
    Удаляет дубликаты из отсортированного массива "на месте" и возвращает
    длину нового массива без дубликатов.
    
    Args:
        arr: Отсортированный список
        
    Returns:
        Длина массива после удаления дубликатов
    """
    # Если массив пуст, возвращаем 0
    if not arr:
        return 0
    
    # i - текущая позиция для записи уникального элемента
    # j - текущая позиция для чтения элементов
    i = 0
    
    for j in range(1, len(arr)):
        # Если текущий элемент отличается от последнего сохраненного
        if arr[j] != arr[i]:
            # Увеличиваем индекс записи и сохраняем уникальный элемент
            i += 1
            arr[i] = arr[j]
    
    # Возвращаем длину массива без дубликатов (i + 1, так как индексация с 0)
    return i + 1

# Пример использования
sorted_array_with_duplicates = [1, 1, 2, 2, 3, 4, 4, 4, 5, 6, 6]
length = remove_duplicates(sorted_array_with_duplicates)

print(f"Массив после удаления дубликатов (первые {length} элементов): "
      f"{sorted_array_with_duplicates[:length]}")
```

Временная сложность: O(n), где n - длина массива.
Пространственная сложность: O(1), удаление выполняется "на месте".

### 2. Техника скользящего окна (Sliding Window)

Эта техника поддерживает "окно" элементов определенного размера, которое последовательно перемещается по массиву.

#### Пример 1: Нахождение максимальной суммы подмассива длины k

```python
def max_sum_subarray(arr, k):
    """
    Находит максимальную сумму подмассива длины k.
    
    Args:
        arr: Список чисел
        k: Размер подмассива
        
    Returns:
        Максимальная сумма и индекс начала подмассива
    """
    # Проверка входных данных
    n = len(arr)
    if n < k:
        return None, None
    
    # Вычисляем сумму первого окна
    window_sum = sum(arr[:k])
    max_sum = window_sum
    max_start = 0
    
    # Последовательно двигаем окно вправо
    for i in range(n - k):
        # Вычитаем уходящий элемент и добавляем новый
        window_sum = window_sum - arr[i] + arr[i + k]
        
        # Обновляем максимальную сумму, если необходимо
        if window_sum > max_sum:
            max_sum = window_sum
            max_start = i + 1
    
    return max_sum, max_start

# Пример использования
arr = [1, 9, -1, -2, 7, 3, -1, 2]
k = 4

max_sum, start_idx = max_sum_subarray(arr, k)
if max_sum is not None:
    print(f"Максимальная сумма подмассива длины {k}: {max_sum}")
    print(f"Подмассив: {arr[start_idx:start_idx+k]}")
```

Временная сложность: O(n), где n - длина массива.
Пространственная сложность: O(1).

#### Пример 2: Нахождение наименьшего подмассива с суммой, большей или равной заданной

```python
def smallest_subarray_with_sum(arr, target_sum):
    """
    Находит наименьший подмассив с суммой, большей или равной target_sum.
    
    Args:
        arr: Список неотрицательных чисел
        target_sum: Целевая сумма
        
    Returns:
        Длина наименьшего подмассива и его начальный индекс
    """
    n = len(arr)
    if n == 0:
        return 0, None
    
    # Инициализируем переменные
    min_length = float('inf')  # Инициализируем бесконечностью
    min_start = 0
    current_sum = 0
    start = 0
    
    # Расширяем/сужаем окно
    for end in range(n):
        # Добавляем текущий элемент в окно
        current_sum += arr[end]
        
        # Сужаем окно с левой стороны, пока сумма больше или равна целевой
        while current_sum >= target_sum and start <= end:
            # Обновляем минимальную длину, если текущая меньше
            if end - start + 1 < min_length:
                min_length = end - start + 1
                min_start = start
            
            # Удаляем элемент из окна слева
            current_sum -= arr[start]
            start += 1
    
    if min_length == float('inf'):
        return 0, None  # Подмассив с требуемой суммой не найден
    
    return min_length, min_start

# Пример использования
arr = [2, 1, 5, 2, 3, 2]
target_sum = 7

min_length, start_idx = smallest_subarray_with_sum(arr, target_sum)
if min_length > 0:
    print(f"Длина наименьшего подмассива с суммой >= {target_sum}: {min_length}")
    print(f"Подмассив: {arr[start_idx:start_idx+min_length]}")
else:
    print(f"Подмассив с суммой >= {target_sum} не найден")
```

Временная сложность: O(n), где n - длина массива.
Пространственная сложность: O(1).

### 3. Префиксные суммы (Prefix Sums)

Техника префиксных сумм предварительно вычисляет суммы всех префиксов массива, что позволяет эффективно находить сумму любого подмассива.

#### Пример 1: Вычисление суммы подмассива [i, j]

```python
def build_prefix_sums(arr):
    """
    Строит массив префиксных сумм.
    
    Args:
        arr: Исходный список чисел
        
    Returns:
        Список префиксных сумм, где prefix_sums[i] = сумма элементов arr[0...i-1]
    """
    n = len(arr)
    prefix_sums = [0] * (n + 1)
    
    for i in range(n):
        prefix_sums[i + 1] = prefix_sums[i] + arr[i]
    
    return prefix_sums

def range_sum(prefix_sums, i, j):
    """
    Вычисляет сумму элементов в диапазоне [i, j] с помощью префиксных сумм.
    
    Args:
        prefix_sums: Массив префиксных сумм
        i: Начальный индекс (включительно)
        j: Конечный индекс (включительно)
        
    Returns:
        Сумма элементов в диапазоне arr[i...j]
    """
    return prefix_sums[j + 1] - prefix_sums[i]

# Пример использования
arr = [1, 2, 3, 4, 5, 6, 7, 8]
prefix_sums = build_prefix_sums(arr)

# Вычисляем сумму подмассива [2, 5] (элементы с индексами 2, 3, 4, 5)
i, j = 2, 5
subarray_sum = range_sum(prefix_sums, i, j)
print(f"Сумма подмассива [{i}, {j}]: {subarray_sum}")
```

Временная сложность построения префиксных сумм: O(n).
Временная сложность запроса суммы подмассива: O(1).
Пространственная сложность: O(n) для хранения массива префиксных сумм.

#### Пример 2: Подсчет количества подмассивов с заданной суммой

```python
def count_subarrays_with_sum(arr, target_sum):
    """
    Подсчитывает количество подмассивов с суммой, равной target_sum.
    
    Args:
        arr: Список чисел
        target_sum: Целевая сумма
        
    Returns:
        Количество подмассивов с заданной суммой
    """
    # Словарь для хранения частот префиксных сумм
    prefix_sum_count = {0: 1}  # Пустой подмассив имеет сумму 0
    current_sum = 0
    count = 0
    
    for num in arr:
        # Обновляем текущую префиксную сумму
        current_sum += num
        
        # Если current_sum - target_sum встречалась ранее,
        # значит найдены подмассивы с суммой target_sum
        if current_sum - target_sum in prefix_sum_count:
            count += prefix_sum_count[current_sum - target_sum]
        
        # Обновляем частоту текущей префиксной суммы
        prefix_sum_count[current_sum] = prefix_sum_count.get(current_sum, 0) + 1
    
    return count

# Пример использования
arr = [3, 4, 7, 2, -3, 1, 4, 2]
target_sum = 7

count = count_subarrays_with_sum(arr, target_sum)
print(f"Количество подмассивов с суммой {target_sum}: {count}")
```

Временная сложность: O(n), где n - длина массива.
Пространственная сложность: O(n) в худшем случае для хранения словаря префиксных сумм.

### 4. Алгоритм Кадане (Kadane's Algorithm)

Алгоритм Кадане используется для нахождения подмассива с максимальной суммой.

```python
def kadane_algorithm(arr):
    """
    Находит подмассив с максимальной суммой с помощью алгоритма Кадане.
    
    Args:
        arr: Список чисел
        
    Returns:
        Максимальная сумма, начальный и конечный индексы подмассива
    """
    # Проверка на пустой массив
    if not arr:
        return 0, None, None
    
    # Инициализируем переменные
    max_so_far = arr[0]  # Максимальная сумма, найденная до сих пор
    max_ending_here = arr[0]  # Максимальная сумма, заканчивающаяся на текущем элементе
    start = 0  # Начальный индекс текущего подмассива
    end = 0  # Конечный индекс текущего подмассива
    temp_start = 0  # Временный начальный индекс
    
    # Проходим по массиву, начиная со второго элемента
    for i in range(1, len(arr)):
        # Выбираем лучший из двух вариантов:
        # 1. Начать новый подмассив с текущего элемента
        # 2. Продолжить текущий подмассив
        if arr[i] > max_ending_here + arr[i]:
            max_ending_here = arr[i]
            temp_start = i
        else:
            max_ending_here += arr[i]
        
        # Обновляем максимальную сумму, если найдена лучшая
        if max_ending_here > max_so_far:
            max_so_far = max_ending_here
            start = temp_start
            end = i
    
    return max_so_far, start, end

# Пример использования
arr = [-2, -3, 4, -1, -2, 1, 5, -3]
max_sum, start_idx, end_idx = kadane_algorithm(arr)

print(f"Максимальная сумма подмассива: {max_sum}")
print(f"Подмассив: {arr[start_idx:end_idx+1]}")
print(f"Индексы: [{start_idx}, {end_idx}]")
```

Временная сложность: O(n), где n - длина массива.
Пространственная сложность: O(1).

### 5. Быстрый и медленный указатели (Floyd's Cycle Finding Algorithm)

Эта техника использует два указателя, движущихся с разной скоростью, для обнаружения циклов в последовательностях.

```python
def detect_cycle(arr, start_idx):
    """
    Определяет, содержит ли массив цикл при интерпретации его как связного списка,
    где значение каждого элемента - индекс следующего элемента.
    
    Args:
        arr: Список чисел, где каждое число - индекс следующего элемента
        start_idx: Начальный индекс
        
    Returns:
        True, если цикл найден, иначе False
    """
    # Проверка входных данных
    if not arr or start_idx < 0 or start_idx >= len(arr):
        return False
    
    # Инициализируем указатели
    slow = start_idx
    fast = start_idx
    
    while True:
        # Продвигаем медленный указатель на одну позицию
        slow = arr[slow]
        
        # Продвигаем быстрый указатель на две позиции
        fast = arr[fast]
        if fast < 0 or fast >= len(arr):
            return False  # Вышли за границы массива
        
        fast = arr[fast]
        if fast < 0 or fast >= len(arr):
            return False  # Вышли за границы массива
        
        # Если указатели встретились, значит цикл найден
        if slow == fast:
            return True
        
        # Если быстрый указатель достиг конца, цикла нет
        if fast < 0 or fast >= len(arr):
            return False

# Пример использования
# Массив, где arr[i] - индекс следующего элемента
# В этом примере есть цикл: 0->1->3->2->3->2->...
arr_with_cycle = [1, 3, 3, 2]
has_cycle = detect_cycle(arr_with_cycle, 0)
print(f"Массив содержит цикл: {has_cycle}")

# Массив без цикла: 0->2->4
arr_without_cycle = [2, 3, 4, 5, 6]
has_cycle = detect_cycle(arr_without_cycle, 0)
print(f"Массив содержит цикл: {has_cycle}")
```

Временная сложность: O(n), где n - длина массива.
Пространственная сложность: O(1).

## Практическое применение в веб-разработке

### 1. Пагинация данных

```python
def paginate_data(items, page_size, page_number):
    """
    Разбивает список элементов на страницы.
    
    Args:
        items: Список элементов для пагинации
        page_size: Размер страницы
        page_number: Номер страницы (начиная с 1)
        
    Returns:
        Словарь с элементами текущей страницы и метаданными пагинации
    """
    # Проверка входных данных
    if page_size <= 0 or page_number <= 0:
        raise ValueError("Размер страницы и номер страницы должны быть положительными")
    
    # Вычисляем общее количество страниц
    total_items = len(items)
    total_pages = (total_items + page_size - 1) // page_size  # Округление вверх
    
    # Проверяем, что запрошенная страница существует
    if page_number > total_pages:
        page_number = total_pages if total_pages > 0 else 1
    
    # Вычисляем начальный и конечный индексы
    start_idx = (page_number - 1) * page_size
    end_idx = min(start_idx + page_size, total_items)
    
    # Получаем элементы текущей страницы
    current_page_items = items[start_idx:end_idx]
    
    # Формируем результат
    result = {
        "items": current_page_items,
        "pagination": {
            "total_items": total_items,
            "total_pages": total_pages,
            "current_page": page_number,
            "page_size": page_size,
            "has_previous": page_number > 1,
            "has_next": page_number < total_pages
        }
    }
    
    return result

# Пример использования
products = [
    {"id": 1, "name": "Ноутбук", "price": 1200},
    {"id": 2, "name": "Смартфон", "price": 800},
    {"id": 3, "name": "Планшет", "price": 500},
    {"id": 4, "name": "Наушники", "price": 150},
    {"id": 5, "name": "Монитор", "price": 300},
    {"id": 6, "name": "Клавиатура", "price": 80},
    {"id": 7, "name": "Мышь", "price": 50},
    {"id": 8, "name": "Колонки", "price": 120},
    {"id": 9, "name": "Веб-камера", "price": 70},
    {"id": 10, "name": "Принтер", "price": 200}
]

# Получаем вторую страницу с размером страницы 3
page = paginate_data(products, 3, 2)

print("Элементы на странице:")
for item in page["items"]:
    print(f"{item['name']}: ${item['price']}")

print("\nИнформация о пагинации:")
for key, value in page["pagination"].items():
    print(f"{key}: {value}")
```

### 2. Фильтрация и группировка данных в REST API

```python
def filter_and_group_data(items, filters, group_by=None):
    """
    Фильтрует и группирует данные по заданным критериям.
    
    Args:
        items: Список элементов
        filters: Словарь фильтров {поле: значение}
        group_by: Поле для группировки (опционально)
        
    Returns:
        Отфильтрованные и сгруппированные данные
    """
    # Фильтрация
    filtered_items = items
    
    for field, value in filters.items():
        # Разделяем поле и оператор (если есть)
        parts = field.split('__')
        field_name = parts[0]
        operator = parts[1] if len(parts) > 1 else 'eq'
        
        if operator == 'eq':  # Равенство
            filtered_items = [item for item in filtered_items if item.get(field_name) == value]
        elif operator == 'gt':  # Больше
            filtered_items = [item for item in filtered_items if item.get(field_name, 0) > value]
        elif operator == 'lt':  # Меньше
            filtered_items = [item for item in filtered_items if item.get(field_name, 0) < value]
        elif operator == 'contains':  # Содержит подстроку
            filtered_items = [item for item in filtered_items if value in str(item.get(field_name, ''))]
    
    # Группировка (если указана)
    if group_by:
        grouped_data = {}
        
        for item in filtered_items:
            group_value = item.get(group_by)
            
            if group_value not in grouped_data:
                grouped_data[group_value] = []
            
            grouped_data[group_value].append(item)
        
        return grouped_data
    
    return filtered_items

# Пример использования
orders = [
    {"id": 1, "customer_id": 101, "total": 150, "status": "completed", "date": "2023-06-15"},
    {"id": 2, "customer_id": 102, "total": 200, "status": "pending", "date": "2023-06-16"},
    {"id": 3, "customer_id": 101, "total": 50, "status": "completed", "date": "2023-06-17"},
    {"id": 4, "customer_id": 103, "total": 300, "status": "processing", "date": "2023-06-17"},
    {"id": 5, "customer_id": 102, "total": 120, "status": "completed", "date": "2023-06-18"},
    {"id": 6, "customer_id": 101, "total": 180, "status": "pending", "date": "2023-06-19"}
]

# Фильтруем заказы со статусом "completed" и общей суммой больше 100
filters = {"status": "completed", "total__gt": 100}
filtered_orders = filter_and_group_data(orders, filters)

print("\nОтфильтрованные заказы:")
for order in filtered_orders:
    print(f"Заказ #{order['id']}: статус={order['status']}, сумма=${order['total']}")

# Группируем заказы по customer_id
grouped_orders = filter_and_group_data(orders, {}, "customer_id")

print("\nЗаказы, сгруппированные по покупателям:")
for customer_id, customer_orders in grouped_orders.items():
    total_spent = sum(order["total"] for order in customer_orders)
    print(f"Покупатель #{customer_id}: {len(customer_orders)} заказов, общая сумма: ${total_spent}")
```

### 3. Кеширование результатов с использованием алгоритма скользящего окна

```python
class SlidingWindowCache:
    """
    Кеш с использованием алгоритма скользящего окна для хранения
    последних N результатов API-запросов.
    """
    def __init__(self, capacity):
        """
        Инициализирует кеш с заданной емкостью.
        
        Args:
            capacity: Максимальное количество элементов в кеше
        """
        self.capacity = capacity
        self.cache = {}  # Словарь для хранения значений
        self.keys = []   # Список для отслеживания порядка ключей
    
    def get(self, key):
        """
        Получает значение из кеша по ключу.
        
        Args:
            key: Ключ для поиска
            
        Returns:
            Значение, если ключ найден, иначе None
        """
        if key in self.cache:
            # Обновляем порядок ключей (удаляем и добавляем в конец)
            self.keys.remove(key)
            self.keys.append(key)
            return self.cache[key]
        
        return None
    
    def put(self, key, value):
        """
        Добавляет или обновляет значение в кеше.
        
        Args:
            key: Ключ
            value: Значение для сохранения
        """
        # Если ключ уже существует, обновляем его
        if key in self.cache:
            self.cache[key] = value
            self.keys.remove(key)
            self.keys.append(key)
            return
        
        # Если кеш полон, удаляем самый старый элемент
        if len(self.keys) >= self.capacity:
            oldest_key = self.keys.pop(0)
            del self.cache[oldest_key]
        
        # Добавляем новый элемент
        self.cache[key] = value
        self.keys.append(key)
    
    def clear(self):
        """Очищает кеш."""
        self.cache = {}
        self.keys = []
    
    def info(self):
        """
        Возвращает информацию о текущем состоянии кеша.
        
        Returns:
            Словарь с информацией о кеше
        """
        return {
            "capacity": self.capacity,
            "size": len(self.keys),
            "keys": self.keys
        }

# Пример использования
api_cache = SlidingWindowCache(capacity=3)

# Имитируем запросы к API с кешированием
def api_request(endpoint, use_cache=True):
    # Формируем ключ для кеша
    cache_key = f"api:{endpoint}"
    
    # Проверяем кеш, если разрешено использование кеша
    if use_cache:
        cached_result = api_cache.get(cache_key)
        if cached_result:
            print(f"Результат для {endpoint} получен из кеша")
            return cached_result
    
    # Имитируем запрос к API (в реальном коде здесь был бы HTTP-запрос)
    print(f"Выполнение запроса к API: {endpoint}")
    result = {"endpoint": endpoint, "data": f"Данные для {endpoint}", "timestamp": "2023-06-20"}
    
    # Кешируем результат
    api_cache.put(cache_key, result)
    
    return result

# Выполняем запросы
endpoints = ["/users", "/products", "/orders", "/categories"]
for endpoint in endpoints:
    api_request(endpoint)

print("\nИнформация о кеше после первого прохода:")
print(api_cache.info())

# Повторно запрашиваем некоторые эндпоинты
print("\nПовторные запросы:")
api_request("/users")
api_request("/categories")

print("\nИнформация о кеше после повторных запросов:")
print(api_cache.info())
```

### 4. Обнаружение аномалий с использованием алгоритма скользящего окна

```python
def detect_anomalies(time_series, window_size, threshold):
    """
    Обнаруживает аномалии в временном ряду с использованием алгоритма скользящего окна.
    
    Args:
        time_series: Список значений временного ряда
        window_size: Размер скользящего окна
        threshold: Порог для определения аномалии (отклонение от среднего в стандартных отклонениях)
        
    Returns:
        Список индексов, где обнаружены аномалии
    """
    import statistics
    
    # Проверка входных данных
    n = len(time_series)
    if n < window_size:
        return []
    
    anomalies = []
    
    # Проходим по временному ряду
    for i in range(window_size, n):
        # Получаем текущее окно
        window = time_series[i - window_size:i]
        
        # Вычисляем статистики окна
        mean = statistics.mean(window)
        stdev = statistics.stdev(window) if len(window) > 1 else 0
        
        # Вычисляем z-оценку текущего значения
        current_value = time_series[i]
        z_score = (current_value - mean) / stdev if stdev > 0 else 0
        
        # Если z-оценка превышает порог, считаем значение аномальным
        if abs(z_score) > threshold:
            anomalies.append(i)
    
    return anomalies

# Пример использования
# Имитируем временной ряд с несколькими аномалиями
request_times = [120, 115, 118, 125, 122, 130, 125, 300, 123, 128, 135, 400, 129, 133]

# Обнаруживаем аномалии с окном размера 5 и порогом 2.5 стандартных отклонения
anomalies = detect_anomalies(request_times, window_size=5, threshold=2.5)

print("\nОбнаруженные аномалии:")
for idx in anomalies:
    print(f"Аномалия в позиции {idx}: значение = {request_times[idx]}")
```

### 5. Анализ пользовательских сессий с использованием префиксных сумм

```python
def analyze_user_sessions(events, session_timeout=30):
    """
    Анализирует пользовательские сессии на основе событий.
    
    Args:
        events: Список событий, отсортированных по времени
                Каждое событие - словарь с полями: user_id, timestamp, action
        session_timeout: Таймаут сессии в минутах
        
    Returns:
        Словарь со статистикой по сессиям
    """
    # Группируем события по пользователям
    user_events = {}
    
    for event in events:
        user_id = event["user_id"]
        if user_id not in user_events:
            user_events[user_id] = []
        
        user_events[user_id].append(event)
    
    # Анализируем сессии для каждого пользователя
    sessions = []
    
    for user_id, user_event_list in user_events.items():
        # Сортируем события по времени (если они ещё не отсортированы)
        user_event_list.sort(key=lambda e: e["timestamp"])
        
        current_session = {
            "user_id": user_id,
            "start_time": user_event_list[0]["timestamp"],
            "end_time": user_event_list[0]["timestamp"],
            "events": [user_event_list[0]],
            "actions": [user_event_list[0]["action"]]
        }
        
        for i in range(1, len(user_event_list)):
            event = user_event_list[i]
            time_diff = (event["timestamp"] - current_session["end_time"]).total_seconds() / 60
            
            # Если время между событиями превышает таймаут, создаем новую сессию
            if time_diff > session_timeout:
                # Вычисляем длительность сессии
                session_duration = (current_session["end_time"] - current_session["start_time"]).total_seconds() / 60
                current_session["duration"] = session_duration
                
                # Сохраняем текущую сессию
                sessions.append(current_session)
                
                # Начинаем новую сессию
                current_session = {
                    "user_id": user_id,
                    "start_time": event["timestamp"],
                    "end_time": event["timestamp"],
                    "events": [event],
                    "actions": [event["action"]]
                }
            else:
                # Продолжаем текущую сессию
                current_session["end_time"] = event["timestamp"]
                current_session["events"].append(event)
                current_session["actions"].append(event["action"])
        
        # Не забываем сохранить последнюю сессию
        session_duration = (current_session["end_time"] - current_session["start_time"]).total_seconds() / 60
        current_session["duration"] = session_duration
        sessions.append(current_session)
    
    # Строим префиксные суммы для быстрого анализа
    session_durations = [session["duration"] for session in sessions]
    prefix_sum = build_prefix_sums(session_durations)
    
    # Вычисляем статистику
    total_sessions = len(sessions)
    total_duration = sum(session_durations)
    avg_duration = total_duration / total_sessions if total_sessions > 0 else 0
    
    # Находим наиболее распространенные действия
    all_actions = [action for session in sessions for action in session["actions"]]
    action_counts = {}
    
    for action in all_actions:
        action_counts[action] = action_counts.get(action, 0) + 1
    
    top_actions = sorted(action_counts.items(), key=lambda x: x[1], reverse=True)[:3]
    
    return {
        "total_sessions": total_sessions,
        "total_duration_minutes": total_duration,
        "average_session_duration": avg_duration,
        "top_actions": top_actions,
        "sessions": sessions
    }

# Пример использования
from datetime import datetime, timedelta

# Генерируем тестовые данные событий
base_time = datetime(2023, 6, 1, 10, 0, 0)
events = []

# События пользователя 1
events.append({"user_id": 1, "timestamp": base_time, "action": "login"})
events.append({"user_id": 1, "timestamp": base_time + timedelta(minutes=5), "action": "view_page"})
events.append({"user_id": 1, "timestamp": base_time + timedelta(minutes=10), "action": "add_to_cart"})
events.append({"user_id": 1, "timestamp": base_time + timedelta(minutes=15), "action": "checkout"})
events.append({"user_id": 1, "timestamp": base_time + timedelta(minutes=45), "action": "login"})  # Новая сессия
events.append({"user_id": 1, "timestamp": base_time + timedelta(minutes=50), "action": "view_page"})

# События пользователя 2
events.append({"user_id": 2, "timestamp": base_time + timedelta(minutes=20), "action": "login"})
events.append({"user_id": 2, "timestamp": base_time + timedelta(minutes=25), "action": "view_page"})
events.append({"user_id": 2, "timestamp": base_time + timedelta(minutes=30), "action": "logout"})

# Анализируем сессии
session_stats = analyze_user_sessions(events)

print("\nСтатистика пользовательских сессий:")
print(f"Всего сессий: {session_stats['total_sessions']}")
print(f"Общая длительность: {session_stats['total_duration_minutes']:.2f} минут")
print(f"Средняя длительность сессии: {session_stats['average_session_duration']:.2f} минут")

print("\nНаиболее распространенные действия:")
for action, count in session_stats['top_actions']:
    print(f"{action}: {count} раз")
```

## Заключение

В этом разделе мы рассмотрели основные алгоритмы и техники для работы с массивами и списками, которые являются фундаментальными структурами данных в программировании. Эти алгоритмы находят широкое применение в веб-разработке, особенно в бэкенд-части, для обработки данных, оптимизации запросов и анализа пользовательской активности.

Ключевые моменты:

1. **Техника двух указателей** позволяет эффективно решать задачи, связанные с поиском пар элементов или обработкой подмассивов с заданными свойствами.

2. **Скользящее окно** помогает оптимизировать вычисления над последовательными подмассивами, избегая повторения одних и тех же операций.

3. **Префиксные суммы** предоставляют возможность за O(1) вычислять суммы произвольных подмассивов после предварительной обработки за O(n).

4. **Алгоритм Кадане** эффективно находит подмассив с максимальной суммой за линейное время.

5. **Быстрый и медленный указатели** позволяют обнаруживать циклы в последовательностях и решать связанные задачи.

Эти алгоритмы и техники могут значительно повысить эффективность вашего кода и расширить спектр задач, которые вы можете решать в веб-разработке.

Следующим шагом в изучении алгоритмов на структурах данных будет рассмотрение алгоритмов на стеках и очередях, которые являются важными абстракциями для многих задач в программировании.

---

В следующем разделе мы рассмотрим алгоритмы на стеках и очередях, включая их применение для обработки выражений, обхода графов и моделирования асинхронных задач в веб-приложениях.