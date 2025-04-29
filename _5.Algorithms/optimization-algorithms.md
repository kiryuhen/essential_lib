# Алгоритмы оптимизации

Алгоритмы оптимизации — это методы поиска наилучшего решения из множества возможных вариантов. Они применяются в различных областях программирования: от планирования ресурсов и маршрутизации до компьютерного зрения и машинного обучения. Понимание этих алгоритмов позволяет решать сложные задачи эффективно и находить оптимальные решения даже для проблем, которые на первый взгляд кажутся неразрешимыми за разумное время.

В этом разделе мы рассмотрим три основных подхода к оптимизации: жадные алгоритмы, динамическое программирование и алгоритмы с возвратом (backtracking). Каждый из них имеет свои сильные стороны и области применения.

## Жадные алгоритмы

Жадные алгоритмы (greedy algorithms) следуют простой стратегии: на каждом шаге выбирать локально оптимальное решение в надежде, что это приведет к глобально оптимальному результату. Эти алгоритмы не пересматривают ранее принятые решения и не ищут альтернативные пути — они просто "жадно" выбирают лучший вариант на каждом этапе.

### Принцип работы жадных алгоритмов

1. Разбить задачу на этапы, требующие принятия решения
2. На каждом этапе выбрать локально оптимальное решение согласно некоторому критерию
3. Никогда не пересматривать ранее принятые решения

### Когда работают жадные алгоритмы?

Жадные алгоритмы дают оптимальное решение не для всех задач. Они успешны, когда задача обладает свойством **жадного выбора** (greedy choice property) и **оптимальной подструктурой** (optimal substructure).

- **Жадный выбор**: локально оптимальный выбор приводит к глобально оптимальному решению
- **Оптимальная подструктура**: оптимальное решение задачи содержит оптимальные решения подзадач

### Примеры жадных алгоритмов

#### 1. Задача о выборе мероприятий (Activity Selection Problem)

Имеется набор мероприятий с временем начала и окончания. Необходимо выбрать максимальное количество не пересекающихся мероприятий.

```python
def activity_selection(activities):
    """
    Выбирает максимальное количество не пересекающихся мероприятий.
    
    Args:
        activities: Список кортежей (начало, конец) для каждого мероприятия
        
    Returns:
        Список индексов выбранных мероприятий
    """
    # Сортируем мероприятия по времени окончания
    sorted_activities = sorted(enumerate(activities), key=lambda x: x[1][1])
    
    selected = [sorted_activities[0][0]]  # Выбираем первое мероприятие
    last_end_time = sorted_activities[0][1][1]
    
    # Проходим по оставшимся мероприятиям
    for i in range(1, len(sorted_activities)):
        idx, (start, end) = sorted_activities[i]
        
        # Если мероприятие начинается после окончания последнего выбранного
        if start >= last_end_time:
            selected.append(idx)
            last_end_time = end
    
    return selected

# Пример использования
activities = [(1, 4), (3, 5), (0, 6), (5, 7), (3, 8), (5, 9), (6, 10), (8, 11), (8, 12), (2, 13), (12, 14)]
selected = activity_selection(activities)

print("Выбранные мероприятия:")
for idx in selected:
    start, end = activities[idx]
    print(f"Мероприятие {idx}: начало = {start}, конец = {end}")
```

**Правильность жадного подхода**: Выбор мероприятия с самым ранним временем окончания на каждом шаге гарантирует, что у нас останется максимальное количество времени для будущих мероприятий.

#### 2. Задача о размене монет (Coin Change Problem) с жадным подходом

Имеется набор монет различных номиналов. Необходимо выдать сдачу указанной суммы, используя минимальное количество монет.

```python
def greedy_coin_change(coins, amount):
    """
    Находит минимальное количество монет для размена суммы (жадный подход).
    
    Args:
        coins: Список номиналов монет в порядке убывания
        amount: Сумма для размена
        
    Returns:
        Словарь {номинал: количество} или None, если размен невозможен
    """
    # Сортируем монеты по убыванию
    coins.sort(reverse=True)
    
    result = {}
    remaining = amount
    
    for coin in coins:
        # Вычисляем, сколько монет данного номинала использовать
        if coin <= remaining:
            count = remaining // coin
            result[coin] = count
            remaining -= coin * count
        
        # Если сумма разменяна полностью, возвращаем результат
        if remaining == 0:
            return result
    
    # Если не удалось разменять сумму полностью
    if remaining > 0:
        return None
    
    return result

# Пример использования
coins_denominations = [100, 50, 10, 5, 2, 1]  # Номиналы монет
amount = 287  # Сумма для размена

result = greedy_coin_change(coins_denominations, amount)
if result:
    print(f"Размен суммы {amount}:")
    for coin, count in result.items():
        print(f"{count} монет номиналом {coin}")
    
    # Проверка
    total_coins = sum(result.values())
    total_amount = sum(coin * count for coin, count in result.items())
    print(f"Всего использовано {total_coins} монет для суммы {total_amount}")
else:
    print(f"Невозможно разменять сумму {amount} данными монетами")
```

**Важно**: Жадный алгоритм для задачи о размене монет работает не для всех наборов номиналов. Он гарантирует оптимальное решение только для **канонических систем** (canonical coin systems), где жадный выбор ведет к оптимальному решению. Примером такой системы являются монеты в большинстве валют (1, 2, 5, 10, ...).

#### 3. Алгоритм Хаффмана (Huffman Coding)

Алгоритм Хаффмана используется для сжатия данных и создания префиксных кодов переменной длины для символов на основе их частоты.

```python
import heapq
from collections import Counter

class HuffmanNode:
    """
    Узел для дерева Хаффмана.
    """
    def __init__(self, char, freq):
        self.char = char  # Символ (может быть None для внутренних узлов)
        self.freq = freq  # Частота
        self.left = None  # Левый потомок
        self.right = None  # Правый потомок
    
    def __lt__(self, other):
        # Для работы с очередью с приоритетом
        return self.freq < other.freq

def build_huffman_tree(text):
    """
    Строит дерево Хаффмана для заданного текста.
    
    Args:
        text: Исходный текст
        
    Returns:
        Корень дерева Хаффмана
    """
    # Подсчет частоты каждого символа
    char_freq = Counter(text)
    
    # Создаем очередь с приоритетом из узлов
    priority_queue = []
    for char, freq in char_freq.items():
        node = HuffmanNode(char, freq)
        heapq.heappush(priority_queue, node)
    
    # Строим дерево Хаффмана
    while len(priority_queue) > 1:
        # Извлекаем два узла с наименьшей частотой
        left = heapq.heappop(priority_queue)
        right = heapq.heappop(priority_queue)
        
        # Создаем новый внутренний узел с суммой частот
        merged = HuffmanNode(None, left.freq + right.freq)
        merged.left = left
        merged.right = right
        
        # Добавляем новый узел обратно в очередь
        heapq.heappush(priority_queue, merged)
    
    # Возвращаем корень дерева
    return priority_queue[0]

def build_huffman_codes(node, code="", mapping=None):
    """
    Рекурсивно строит коды Хаффмана для каждого символа.
    
    Args:
        node: Текущий узел дерева Хаффмана
        code: Текущий код (путь в дереве)
        mapping: Словарь для хранения кодов
        
    Returns:
        Словарь {символ: код}
    """
    if mapping is None:
        mapping = {}
    
    # Если это лист (узел с символом), сохраняем код
    if node.char is not None:
        mapping[node.char] = code
    else:
        # Рекурсивно обходим левое и правое поддерево
        build_huffman_codes(node.left, code + "0", mapping)
        build_huffman_codes(node.right, code + "1", mapping)
    
    return mapping

def huffman_encode(text):
    """
    Сжимает текст с помощью алгоритма Хаффмана.
    
    Args:
        text: Исходный текст
        
    Returns:
        Кортеж (сжатый текст, словарь кодов, корень дерева)
    """
    # Особый случай: пустой текст
    if not text:
        return "", {}, None
    
    # Строим дерево Хаффмана
    root = build_huffman_tree(text)
    
    # Получаем коды для каждого символа
    codes = build_huffman_codes(root)
    
    # Кодируем текст
    encoded = "".join(codes[char] for char in text)
    
    return encoded, codes, root

def huffman_decode(encoded_text, root):
    """
    Декодирует текст, сжатый алгоритмом Хаффмана.
    
    Args:
        encoded_text: Сжатый текст
        root: Корень дерева Хаффмана
        
    Returns:
        Декодированный текст
    """
    if not encoded_text or not root:
        return ""
    
    decoded = []
    current = root
    
    for bit in encoded_text:
        # Переходим к левому или правому потомку в зависимости от бита
        if bit == "0":
            current = current.left
        else:  # bit == "1"
            current = current.right
        
        # Если достигли листа, добавляем символ и возвращаемся к корню
        if current.char is not None:
            decoded.append(current.char)
            current = root
    
    return "".join(decoded)

# Пример использования
text = "this is an example for huffman encoding"
encoded, codes, tree = huffman_encode(text)

print("Исходный текст:", text)
print("Коды Хаффмана:")
for char, code in sorted(codes.items()):
    print(f"'{char}': {code}")

print(f"Сжатый текст: {encoded}")
print(f"Длина исходного текста: {len(text) * 8} бит")
print(f"Длина сжатого текста: {len(encoded)} бит")
print(f"Степень сжатия: {len(text) * 8 / len(encoded):.2f}")

# Проверяем декодирование
decoded = huffman_decode(encoded, tree)
print(f"Декодированный текст: {decoded}")
print(f"Проверка: {text == decoded}")
```

**Правильность жадного подхода**: Алгоритм Хаффмана на каждом шаге объединяет два узла с наименьшей частотой, что приводит к оптимальному префиксному коду.

#### 4. Задача о рюкзаке в дробном варианте (Fractional Knapsack Problem)

В задаче о дробном рюкзаке нужно выбрать набор предметов с максимальной суммарной ценностью, не превышая ограничение по весу. В отличие от классической задачи, здесь можно брать части предметов.

```python
def fractional_knapsack(items, capacity):
    """
    Решает задачу о дробном рюкзаке.
    
    Args:
        items: Список кортежей (вес, стоимость) для каждого предмета
        capacity: Максимальная вместимость рюкзака
        
    Returns:
        Кортеж (максимальная стоимость, список взятых предметов)
    """
    # Вычисляем удельную стоимость каждого предмета (стоимость на единицу веса)
    items_with_value_per_weight = [(i, w, v, v/w) for i, (w, v) in enumerate(items)]
    
    # Сортируем предметы по удельной стоимости в убывающем порядке
    items_with_value_per_weight.sort(key=lambda x: x[3], reverse=True)
    
    total_value = 0
    remaining_capacity = capacity
    selected_items = []
    
    for index, weight, value, ratio in items_with_value_per_weight:
        if remaining_capacity >= weight:
            # Берем предмет целиком
            selected_items.append((index, weight, value, 1.0))
            total_value += value
            remaining_capacity -= weight
        else:
            # Берем часть предмета
            fraction = remaining_capacity / weight
            selected_items.append((index, weight * fraction, value * fraction, fraction))
            total_value += value * fraction
            remaining_capacity = 0
            break
    
    return total_value, selected_items

# Пример использования
items = [(10, 60), (20, 100), (30, 120)]  # Список предметов (вес, стоимость)
capacity = 50  # Вместимость рюкзака

max_value, selected = fractional_knapsack(items, capacity)

print(f"Максимальная стоимость: {max_value}")
print("Выбранные предметы:")
for index, weight, value, fraction in selected:
    original_weight, original_value = items[index]
    print(f"Предмет {index}: вес = {weight:.1f}/{original_weight}, "
          f"стоимость = {value:.1f}/{original_value}, взято {fraction*100:.1f}%")
```

**Правильность жадного подхода**: В задаче о дробном рюкзаке жадная стратегия — брать предметы в порядке убывания их удельной стоимости — гарантированно приводит к оптимальному решению.

### Когда жадные алгоритмы не работают

Хотя жадные алгоритмы просты и эффективны, они не всегда дают оптимальное решение. Рассмотрим пример задачи о размене монет с нестандартным набором номиналов:

```python
# Пример, где жадный алгоритм не оптимален
non_canonical_coins = [1, 3, 4]  # Нестандартные номиналы
amount = 6

greedy_result = greedy_coin_change(non_canonical_coins.copy(), amount)
print("\nРазмен суммы", amount, "с номиналами", non_canonical_coins)
print("Жадный алгоритм:", greedy_result)  # {4: 1, 1: 2} - используется 3 монеты

# Оптимальное решение - две монеты по 3
optimal_result = {3: 2}
print("Оптимальное решение:", optimal_result)
```

Для таких задач лучше использовать динамическое программирование, которое рассматривает все возможные решения.

## Динамическое программирование

Динамическое программирование (DP) — это метод решения сложных задач путем разбиения их на более простые подзадачи. В отличие от жадных алгоритмов, DP рассматривает все возможные решения и гарантирует оптимальный результат. Ключевая идея заключается в сохранении результатов подзадач для предотвращения повторных вычислений.

### Основные характеристики динамического программирования

1. **Оптимальная подструктура**: оптимальное решение задачи содержит оптимальные решения подзадач.
2. **Перекрывающиеся подзадачи**: алгоритм многократно решает одни и те же подзадачи, что позволяет кешировать результаты.

### Подходы к динамическому программированию

1. **Нисходящий подход (top-down)**: решение с мемоизацией, когда мы рекурсивно решаем подзадачи и сохраняем результаты в кеше.
2. **Восходящий подход (bottom-up)**: итеративное построение результатов для подзадач от меньших к большим.

### Примеры задач динамического программирования

#### 1. Задача о размене монет (Coin Change Problem) с DP

```python
def dp_coin_change(coins, amount):
    """
    Находит минимальное количество монет для размена суммы (DP).
    
    Args:
        coins: Список номиналов монет
        amount: Сумма для размена
        
    Returns:
        Кортеж (минимальное число монет, список используемых монет) или (float('inf'), None)
    """
    # Массив для хранения минимального числа монет для каждой суммы
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0  # Для суммы 0 нужно 0 монет
    
    # Массив для хранения последней использованной монеты
    last_coin = [None] * (amount + 1)
    
    # Заполняем массивы dp и last_coin
    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i and dp[i - coin] + 1 < dp[i]:
                dp[i] = dp[i - coin] + 1
                last_coin[i] = coin
    
    # Если невозможно разменять сумму
    if dp[amount] == float('inf'):
        return float('inf'), None
    
    # Восстанавливаем список используемых монет
    result = []
    current_amount = amount
    
    while current_amount > 0:
        coin = last_coin[current_amount]
        result.append(coin)
        current_amount -= coin
    
    return dp[amount], result

# Пример использования
coins = [1, 3, 4]
amount = 6

min_coins, used_coins = dp_coin_change(coins, amount)
if min_coins != float('inf'):
    print(f"\nМинимальное количество монет для размена {amount}: {min_coins}")
    print(f"Использованные монеты: {used_coins}")
else:
    print(f"\nНевозможно разменять сумму {amount} данными монетами")
```

#### 2. Задача о рюкзаке в дискретном варианте (0/1 Knapsack Problem)

В классической задаче о рюкзаке нужно выбрать набор предметов с максимальной суммарной ценностью, не превышая ограничение по весу. В отличие от дробного варианта, здесь нельзя брать части предметов.

```python
def knapsack_01(items, capacity):
    """
    Решает задачу о рюкзаке в дискретном варианте (0/1).
    
    Args:
        items: Список кортежей (вес, стоимость) для каждого предмета
        capacity: Максимальная вместимость рюкзака
        
    Returns:
        Кортеж (максимальная стоимость, список выбранных предметов)
    """
    n = len(items)
    
    # Создаем таблицу DP размером (n+1) x (capacity+1)
    dp = [[0 for _ in range(capacity + 1)] for _ in range(n + 1)]
    
    # Заполняем таблицу DP
    for i in range(1, n + 1):
        for w in range(capacity + 1):
            weight, value = items[i - 1]
            
            if weight <= w:
                # Максимум из двух вариантов: взять предмет или не брать
                dp[i][w] = max(dp[i - 1][w], dp[i - 1][w - weight] + value)
            else:
                # Нельзя взять предмет из-за ограничения по весу
                dp[i][w] = dp[i - 1][w]
    
    # Восстанавливаем список выбранных предметов
    selected = []
    w = capacity
    
    for i in range(n, 0, -1):
        if dp[i][w] != dp[i - 1][w]:
            # Предмет i-1 был выбран
            selected.append(i - 1)
            w -= items[i - 1][0]
    
    # Переворачиваем список, чтобы предметы шли в исходном порядке
    selected.reverse()
    
    return dp[n][capacity], selected

# Пример использования
items = [(2, 3), (3, 4), (4, 5), (5, 6)]  # Список предметов (вес, стоимость)
capacity = 8  # Вместимость рюкзака

max_value, selected = knapsack_01(items, capacity)

print(f"\nМаксимальная стоимость: {max_value}")
print("Выбранные предметы:")
for idx in selected:
    weight, value = items[idx]
    print(f"Предмет {idx}: вес = {weight}, стоимость = {value}")
```

#### 3. Задача о наибольшей общей подпоследовательности (Longest Common Subsequence, LCS)

Нахождение наибольшей общей подпоследовательности двух строк — классическая задача динамического программирования.

```python
def longest_common_subsequence(str1, str2):
    """
    Находит наибольшую общую подпоследовательность двух строк.
    
    Args:
        str1: Первая строка
        str2: Вторая строка
        
    Returns:
        Кортеж (длина LCS, сама LCS)
    """
    m, n = len(str1), len(str2)
    
    # Создаем таблицу DP размером (m+1) x (n+1)
    dp = [[0 for _ in range(n + 1)] for _ in range(m + 1)]
    
    # Заполняем таблицу DP
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if str1[i - 1] == str2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    # Восстанавливаем саму LCS
    i, j = m, n
    lcs = []
    
    while i > 0 and j > 0:
        if str1[i - 1] == str2[j - 1]:
            lcs.append(str1[i - 1])
            i -= 1
            j -= 1
        elif dp[i - 1][j] >= dp[i][j - 1]:
            i -= 1
        else:
            j -= 1
    
    # Переворачиваем список, чтобы символы шли в правильном порядке
    lcs.reverse()
    
    return dp[m][n], "".join(lcs)

# Пример использования
str1 = "ABCBDAB"
str2 = "BDCABA"

length, subsequence = longest_common_subsequence(str1, str2)
print(f"\nСтроки: '{str1}' и '{str2}'")
print(f"Длина наибольшей общей подпоследовательности: {length}")
print(f"Наибольшая общая подпоследовательность: '{subsequence}'")
```

#### 4. Задача о редактировании строк (Edit Distance)

Нахождение минимального количества операций (вставка, удаление, замена), необходимых для преобразования одной строки в другую.

```python
def edit_distance(str1, str2):
    """
    Вычисляет редакционное расстояние между двумя строками.
    
    Args:
        str1: Первая строка
        str2: Вторая строка
        
    Returns:
        Кортеж (минимальное количество операций, матрица операций)
    """
    m, n = len(str1), len(str2)
    
    # Создаем таблицу DP размером (m+1) x (n+1)
    dp = [[0 for _ in range(n + 1)] for _ in range(m + 1)]
    
    # Инициализируем первую строку и первый столбец
    for i in range(m + 1):
        dp[i][0] = i  # Удаление i символов из str1
    
    for j in range(n + 1):
        dp[0][j] = j  # Вставка j символов из str2
    
    # Заполняем таблицу DP
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if str1[i - 1] == str2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]  # Символы совпадают, операция не требуется
            else:
                dp[i][j] = 1 + min(
                    dp[i - 1][j],      # Удаление
                    dp[i][j - 1],      # Вставка
                    dp[i - 1][j - 1]   # Замена
                )
    
    # Восстанавливаем последовательность операций
    operations = []
    i, j = m, n
    
    while i > 0 or j > 0:
        if i > 0 and j > 0 and str1[i - 1] == str2[j - 1]:
            # Символы совпадают, операция не требуется
            operations.append(("match", str1[i - 1]))
            i -= 1
            j -= 1
        elif i > 0 and j > 0 and dp[i][j] == dp[i - 1][j - 1] + 1:
            # Замена символа
            operations.append(("replace", str1[i - 1], str2[j - 1]))
            i -= 1
            j -= 1
        elif i > 0 and dp[i][j] == dp[i - 1][j] + 1:
            # Удаление символа
            operations.append(("delete", str1[i - 1]))
            i -= 1
        else:  # j > 0 and dp[i][j] == dp[i][j - 1] + 1
            # Вставка символа
            operations.append(("insert", str2[j - 1]))
            j -= 1
    
    # Переворачиваем список, чтобы операции шли в правильном порядке
    operations.reverse()
    
    return dp[m][n], operations

# Пример использования
str1 = "kitten"
str2 = "sitting"

distance, operations = edit_distance(str1, str2)
print(f"\nСтроки: '{str1}' и '{str2}'")
print(f"Редакционное расстояние: {distance}")
print("Операции:")
for op in operations:
    if op[0] == "match":
        print(f"Символы совпадают: '{op[1]}'")
    elif op[0] == "replace":
        print(f"Заменить '{op[1]}' на '{op[2]}'")
    elif op[0] == "delete":
        print(f"Удалить '{op[1]}'")
    elif op[0] == "insert":
        print(f"Вставить '{op[1]}'")
```

#### 5. Задача о наибольшей возрастающей подпоследовательности (Longest Increasing Subsequence, LIS)

Нахождение наибольшей строго возрастающей подпоследовательности в массиве.

```python
def longest_increasing_subsequence(arr):
    """
    Находит наибольшую возрастающую подпоследовательность.
    
    Args:
        arr: Исходный массив
        
    Returns:
        Кортеж (длина LIS, сама LIS)
    """
    if not arr:
        return 0, []
    
    n = len(arr)
    
    # dp[i] - длина LIS, оканчивающейся на arr[i]
    dp = [1] * n
    
    # prev[i] - индекс предыдущего элемента в LIS
    prev = [-1] * n
    
    # Заполняем массивы dp и prev
    for i in range(1, n):
        for j in range(i):
            if arr[j] < arr[i] and dp[j] + 1 > dp[i]:
                dp[i] = dp[j] + 1
                prev[i] = j
    
    # Находим индекс последнего элемента LIS
    max_length = max(dp)
    last_index = dp.index(max_length)
    
    # Восстанавливаем саму LIS
    lis = []
    while last_index != -1:
        lis.append(arr[last_index])
        last_index = prev[last_index]
    
    # Переворачиваем список, чтобы элементы шли в правильном порядке
    lis.reverse()
    
    return max_length, lis

# Пример использования
arr = [10, 22, 9, 33, 21, 50, 41, 60, 80]

length, subsequence = longest_increasing_subsequence(arr)
print(f"\nМассив: {arr}")
print(f"Длина наибольшей возрастающей подпоследовательности: {length}")
print(f"Наибольшая возрастающая подпоследовательность: {subsequence}")
```

### Оптимизация пространства в DP

В некоторых задачах можно оптимизировать использование памяти, сохраняя только необходимые данные. Например, в задаче о рюкзаке можно использовать только одну строку матрицы DP.

```python
def knapsack_01_optimized(items, capacity):
    """
    Решает задачу о рюкзаке с оптимизацией памяти.
    
    Args:
        items: Список кортежей (вес, стоимость) для каждого предмета
        capacity: Максимальная вместимость рюкзака
        
    Returns:
        Максимальная стоимость
    """
    n = len(items)
    
    # Создаем одномерный массив DP размером (capacity+1)
    dp = [0 for _ in range(capacity + 1)]
    
    # Заполняем массив DP
    for i in range(n):
        weight, value = items[i]
        
        # Проходим в обратном порядке, чтобы избежать повторного учета предмета
        for w in range(capacity, weight - 1, -1):
            dp[w] = max(dp[w], dp[w - weight] + value)
    
    return dp[capacity]

# Пример использования
items = [(2, 3), (3, 4), (4, 5), (5, 6)]  # Список предметов (вес, стоимость)
capacity = 8  # Вместимость рюкзака

max_value = knapsack_01_optimized(items, capacity)
print(f"\nМаксимальная стоимость (оптимизированная версия): {max_value}")
```

## Алгоритмы с возвратом (Backtracking)

Алгоритмы с возвратом (backtracking) — это методы перебора с возвратами, которые используются для решения задач, где нужно найти все (или некоторые) решения, удовлетворяющие заданным ограничениям. Основная идея заключается в построении решения пошагово: на каждом шаге выбираем один из возможных вариантов, а если он приводит к неудаче, возвращаемся (отступаем, отменяем выбор) и пробуем другой вариант.

### Принцип работы алгоритмов с возвратом

1. Выбрать возможный шаг (кандидата) для добавления к частичному решению
2. Проверить, удовлетворяет ли он ограничениям
3. Если удовлетворяет, добавить его к частичному решению и рекурсивно продолжить
4. Если не удовлетворяет или рекурсивный вызов не привел к решению, отменить выбор (backtrack) и попробовать другой шаг

### Примеры задач с использованием backtracking

#### 1. Задача о N ферзях (N-Queens Problem)

Задача о расстановке N ферзей на шахматной доске N×N так, чтобы никакие два ферзя не били друг друга.

```python
def solve_n_queens(n):
    """
    Решает задачу о N ферзях.
    
    Args:
        n: Размер доски и количество ферзей
        
    Returns:
        Список всех возможных решений, где каждое решение - список позиций ферзей по столбцам
    """
    def is_safe(board, row, col):
        """
        Проверяет, безопасно ли разместить ферзя на позиции (row, col).
        """
        # Проверяем только предыдущие строки, так как текущая и следующие еще не заполнены
        
        # Проверяем ту же колонку
        for i in range(row):
            if board[i] == col:
                return False
        
        # Проверяем левую диагональ
        for i, j in zip(range(row-1, -1, -1), range(col-1, -1, -1)):
            if board[i] == j:
                return False
        
        # Проверяем правую диагональ
        for i, j in zip(range(row-1, -1, -1), range(col+1, n)):
            if board[i] == j:
                return False
        
        return True
    
    def backtrack(row, current_solution):
        """
        Рекурсивно размещает ферзей на доске.
        
        Args:
            row: Текущая строка
            current_solution: Текущее размещение ферзей (по столбцам)
        """
        # Если все ферзи размещены, добавляем решение
        if row == n:
            solutions.append(current_solution.copy())
            return
        
        # Пробуем разместить ферзя в каждом столбце текущей строки
        for col in range(n):
            if is_safe(current_solution, row, col):
                # Размещаем ферзя
                current_solution[row] = col
                
                # Рекурсивно размещаем ферзей в следующих строках
                backtrack(row + 1, current_solution)
                
                # Backtrack: отменяем размещение (на самом деле это необязательно здесь,
                # так как мы всегда перезаписываем значение в следующей итерации,
                # но включено для ясности)
                current_solution[row] = -1
    
    solutions = []
    initial_board = [-1] * n  # Изначально ферзи не размещены
    
    backtrack(0, initial_board)
    
    return solutions

def print_chessboard(solution, n):
    """
    Печатает шахматную доску с расположением ферзей.
    
    Args:
        solution: Размещение ферзей (по столбцам)
        n: Размер доски
    """
    for row in range(n):
        line = ""
        for col in range(n):
            if solution[row] == col:
                line += "Q "
            else:
                line += ". "
        print(line)
    print()

# Пример использования
n = 4  # Размер доски и количество ферзей

solutions = solve_n_queens(n)
print(f"\nЗадача о {n} ферзях:")
print(f"Найдено {len(solutions)} решений.")

for i, solution in enumerate(solutions[:3]):  # Выводим только первые 3 решения
    print(f"Решение {i+1}:")
    print_chessboard(solution, n)
```

#### 2. Судоку (Sudoku)

Решение головоломки судоку с использованием алгоритма с возвратом.

```python
def solve_sudoku(board):
    """
    Решает головоломку судоку.
    
    Args:
        board: Сетка судоку 9x9, где 0 обозначает пустую ячейку
        
    Returns:
        True, если решение найдено (сетка будет изменена на месте), иначе False
    """
    # Находим пустую ячейку
    row, col = find_empty_cell(board)
    
    # Если пустых ячеек нет, судоку решено
    if row == -1 and col == -1:
        return True
    
    # Пробуем поставить цифры от 1 до 9
    for num in range(1, 10):
        if is_valid(board, row, col, num):
            # Если цифра подходит, ставим ее
            board[row][col] = num
            
            # Рекурсивно решаем остальную часть судоку
            if solve_sudoku(board):
                return True
            
            # Если решение не найдено, отменяем выбор (backtrack)
            board[row][col] = 0
    
    # Если ни одна цифра не подходит, возвращаемся на предыдущий шаг
    return False

def find_empty_cell(board):
    """
    Находит первую пустую ячейку в судоку.
    
    Args:
        board: Сетка судоку
        
    Returns:
        Кортеж (строка, столбец) или (-1, -1), если пустых ячеек нет
    """
    for row in range(9):
        for col in range(9):
            if board[row][col] == 0:
                return row, col
    
    return -1, -1

def is_valid(board, row, col, num):
    """
    Проверяет, можно ли поставить число num в ячейку (row, col).
    
    Args:
        board: Сетка судоку
        row: Строка
        col: Столбец
        num: Проверяемое число
        
    Returns:
        True, если число можно поставить, иначе False
    """
    # Проверяем строку
    for x in range(9):
        if board[row][x] == num:
            return False
    
    # Проверяем столбец
    for x in range(9):
        if board[x][col] == num:
            return False
    
    # Проверяем блок 3x3
    start_row, start_col = 3 * (row // 3), 3 * (col // 3)
    for i in range(start_row, start_row + 3):
        for j in range(start_col, start_col + 3):
            if board[i][j] == num:
                return False
    
    return True

def print_sudoku(board):
    """
    Печатает сетку судоку.
    
    Args:
        board: Сетка судоку
    """
    for i in range(9):
        if i % 3 == 0 and i != 0:
            print("- - - - - - - - - - -")
        
        for j in range(9):
            if j % 3 == 0 and j != 0:
                print("| ", end="")
            
            if j == 8:
                print(board[i][j])
            else:
                print(str(board[i][j]) + " ", end="")

# Пример использования
board = [
    [5, 3, 0, 0, 7, 0, 0, 0, 0],
    [6, 0, 0, 1, 9, 5, 0, 0, 0],
    [0, 9, 8, 0, 0, 0, 0, 6, 0],
    [8, 0, 0, 0, 6, 0, 0, 0, 3],
    [4, 0, 0, 8, 0, 3, 0, 0, 1],
    [7, 0, 0, 0, 2, 0, 0, 0, 6],
    [0, 6, 0, 0, 0, 0, 2, 8, 0],
    [0, 0, 0, 4, 1, 9, 0, 0, 5],
    [0, 0, 0, 0, 8, 0, 0, 7, 9]
]

print("\nИсходное судоку:")
print_sudoku(board)

if solve_sudoku(board):
    print("\nРешение:")
    print_sudoku(board)
else:
    print("\nРешение не найдено")
```

#### 3. Генерация всех возможных комбинаций скобок (Parentheses Generation)

Генерация всех правильных комбинаций скобок заданной длины.

```python
def generate_parentheses(n):
    """
    Генерирует все правильные комбинации скобок заданной длины.
    
    Args:
        n: Количество пар скобок
        
    Returns:
        Список всех правильных комбинаций
    """
    result = []
    
    def backtrack(s="", left=0, right=0):
        """
        Рекурсивно генерирует комбинации скобок.
        
        Args:
            s: Текущая строка
            left: Количество открывающих скобок
            right: Количество закрывающих скобок
        """
        # Если строка содержит n пар скобок, добавляем ее в результат
        if len(s) == 2 * n:
            result.append(s)
            return
        
        # Добавляем открывающую скобку, если их меньше n
        if left < n:
            backtrack(s + "(", left + 1, right)
        
        # Добавляем закрывающую скобку, если она соответствует открывающей
        if right < left:
            backtrack(s + ")", left, right + 1)
    
    backtrack()
    return result

# Пример использования
n = 3  # Количество пар скобок

parentheses = generate_parentheses(n)
print(f"\nВсе правильные комбинации {n} пар скобок:")
for i, combination in enumerate(parentheses):
    print(f"{i+1}. {combination}")
```

#### 4. Задача о разделении множества (Subset Sum Problem)

Поиск подмножества элементов, сумма которых равна заданному значению.

```python
def subset_sum(nums, target):
    """
    Находит подмножество элементов с суммой равной target.
    
    Args:
        nums: Список чисел
        target: Целевая сумма
        
    Returns:
        Список всех подмножеств с суммой равной target
    """
    result = []
    
    def backtrack(start, current_sum, subset):
        """
        Рекурсивно ищет подмножества с суммой равной target.
        
        Args:
            start: Индекс, с которого начинать поиск
            current_sum: Текущая сумма
            subset: Текущее подмножество
        """
        # Если текущая сумма равна target, добавляем подмножество в результат
        if current_sum == target:
            result.append(subset.copy())
            return
        
        # Если сумма превышает target или достигнут конец списка, возврат
        if current_sum > target or start >= len(nums):
            return
        
        # Перебираем элементы, начиная с позиции start
        for i in range(start, len(nums)):
            # Добавляем текущий элемент в подмножество
            subset.append(nums[i])
            
            # Рекурсивно ищем подмножества с обновленной суммой
            backtrack(i + 1, current_sum + nums[i], subset)
            
            # Backtrack: удаляем последний элемент
            subset.pop()
    
    backtrack(0, 0, [])
    return result

# Пример использования
nums = [2, 3, 6, 7]
target = 7

subsets = subset_sum(nums, target)
print(f"\nПодмножества с суммой {target} из {nums}:")
for subset in subsets:
    print(subset)
```

#### 5. Задача коммивояжера (Traveling Salesman Problem) с использованием backtracking

Поиск кратчайшего маршрута, проходящего через все города и возвращающегося в исходный город.

```python
def traveling_salesman(graph):
    """
    Решает задачу коммивояжера методом перебора с возвратами.
    
    Args:
        graph: Матрица смежности, где graph[i][j] - расстояние от города i до города j
        
    Returns:
        Кортеж (минимальная длина пути, оптимальный маршрут)
    """
    n = len(graph)
    
    # Начальное наилучшее решение
    best_distance = float('inf')
    best_path = []
    
    def backtrack(current_city, unvisited, path, distance):
        """
        Рекурсивно ищет оптимальный маршрут.
        
        Args:
            current_city: Текущий город
            unvisited: Множество непосещенных городов
            path: Текущий маршрут
            distance: Текущая длина маршрута
        """
        nonlocal best_distance, best_path
        
        # Если все города посещены, проверяем путь обратно в начальный город
        if not unvisited:
            if graph[current_city][0] > 0:  # Если есть путь обратно
                total_distance = distance + graph[current_city][0]
                
                if total_distance < best_distance:
                    best_distance = total_distance
                    best_path = path + [0]  # Добавляем начальный город в конец
            
            return
        
        # Если текущая длина уже превышает лучшую найденную, отсекаем ветвь
        if distance >= best_distance:
            return
        
        # Перебираем все непосещенные города
        for next_city in unvisited:
            # Если есть путь в следующий город
            if graph[current_city][next_city] > 0:
                # Удаляем город из непосещенных
                unvisited.remove(next_city)
                
                # Рекурсивно ищем маршрут
                backtrack(
                    next_city,
                    unvisited,
                    path + [next_city],
                    distance + graph[current_city][next_city]
                )
                
                # Backtrack: возвращаем город в непосещенные
                unvisited.add(next_city)
    
    # Начинаем с города 0
    start_city = 0
    unvisited = set(range(1, n))  # Все города, кроме начального
    
    backtrack(start_city, unvisited, [start_city], 0)
    
    return best_distance, best_path

# Пример использования
graph = [
    [0, 10, 15, 20],
    [10, 0, 35, 25],
    [15, 35, 0, 30],
    [20, 25, 30, 0]
]

min_distance, path = traveling_salesman(graph)
print(f"\nЗадача коммивояжера для {len(graph)} городов:")
print(f"Минимальная длина пути: {min_distance}")
print(f"Оптимальный маршрут: {' -> '.join(str(city) for city in path)}")
```

### Оптимизация алгоритмов с возвратом

Чистый перебор часто неэффективен для больших входных данных. Существуют различные методы оптимизации:

1. **Отсечение ветвей (Branch and Bound)**: отбрасывание ветвей, которые заведомо не могут привести к оптимальному решению.
2. **Эвристики для выбора следующего шага**: использование эвристик для выбора наиболее перспективных вариантов сначала.
3. **Запоминание уже решенных подзадач**: использование мемоизации для предотвращения повторных вычислений.

```python
def traveling_salesman_optimized(graph):
    """
    Решает задачу коммивояжера с оптимизацией (Branch and Bound).
    
    Args:
        graph: Матрица смежности, где graph[i][j] - расстояние от города i до города j
        
    Returns:
        Кортеж (минимальная длина пути, оптимальный маршрут)
    """
    n = len(graph)
    
    # Начальное наилучшее решение
    best_distance = float('inf')
    best_path = []
    
    # Вычисляем нижнюю оценку для непосещенных городов
    def lower_bound(current_city, unvisited):
        """
        Вычисляет нижнюю оценку для оставшихся городов.
        
        Args:
            current_city: Текущий город
            unvisited: Множество непосещенных городов
            
        Returns:
            Нижняя оценка для оставшихся городов
        """
        bound = 0
        
        # Добавляем минимальную исходящую дугу из текущего города
        if unvisited:
            bound += min(graph[current_city][j] for j in unvisited)
        
        # Добавляем минимальную исходящую дугу для каждого непосещенного города
        for city in unvisited:
            # Ищем минимальные дуги в другие непосещенные города и в начальный город
            possible_dest = unvisited - {city} | {0}
            if possible_dest:
                bound += min(graph[city][j] for j in possible_dest)
        
        return bound
    
    def backtrack(current_city, unvisited, path, distance):
        """
        Рекурсивно ищет оптимальный маршрут.
        
        Args:
            current_city: Текущий город
            unvisited: Множество непосещенных городов
            path: Текущий маршрут
            distance: Текущая длина маршрута
        """
        nonlocal best_distance, best_path
        
        # Если все города посещены, проверяем путь обратно в начальный город
        if not unvisited:
            if graph[current_city][0] > 0:  # Если есть путь обратно
                total_distance = distance + graph[current_city][0]
                
                if total_distance < best_distance:
                    best_distance = total_distance
                    best_path = path + [0]  # Добавляем начальный город в конец
            
            return
        
        # Вычисляем нижнюю оценку для текущего частичного решения
        if distance + lower_bound(current_city, unvisited) >= best_distance:
            return  # Отсекаем ветвь, если нижняя оценка уже больше лучшего решения
        
        # Сортируем непосещенные города по расстоянию от текущего
        sorted_cities = sorted(unvisited, key=lambda city: graph[current_city][city])
        
        # Перебираем все непосещенные города в отсортированном порядке
        for next_city in sorted_cities:
            # Если есть путь в следующий город
            if graph[current_city][next_city] > 0:
                # Удаляем город из непосещенных
                unvisited.remove(next_city)
                
                # Рекурсивно ищем маршрут
                backtrack(
                    next_city,
                    unvisited,
                    path + [next_city],
                    distance + graph[current_city][next_city]
                )
                
                # Backtrack: возвращаем город в непосещенные
                unvisited.add(next_city)
    
    # Начинаем с города 0
    start_city = 0
    unvisited = set(range(1, n))  # Все города, кроме начального
    
    backtrack(start_city, unvisited, [start_city], 0)
    
    return best_distance, best_path

# Пример использования
graph = [
    [0, 10, 15, 20],
    [10, 0, 35, 25],
    [15, 35, 0, 30],
    [20, 25, 30, 0]
]

min_distance, path = traveling_salesman_optimized(graph)
print(f"\nЗадача коммивояжера (оптимизированная версия):")
print(f"Минимальная длина пути: {min_distance}")
print(f"Оптимальный маршрут: {' -> '.join(str(city) for city in path)}")
```

## Практические применения алгоритмов оптимизации

### 1. Планирование и составление расписаний

Алгоритмы оптимизации широко используются для оптимального распределения ресурсов и составления расписаний.

```python
def schedule_tasks_with_deadlines(tasks):
    """
    Планирует задачи с учетом их крайних сроков, 
    максимизируя количество выполненных задач.
    
    Args:
        tasks: Список кортежей (задача, продолжительность, крайний срок)
        
    Returns:
        Кортеж (выполненные задачи, расписание)
    """
    # Сортируем задачи по крайним срокам
    sorted_tasks = sorted(tasks, key=lambda x: x[2])
    
    schedule = []  # Итоговое расписание
    current_time = 0  # Текущее время
    
    for task, duration, deadline in sorted_tasks:
        # Проверяем, можем ли выполнить задачу до крайнего срока
        if current_time + duration <= deadline:
            schedule.append((task, current_time, current_time + duration))
            current_time += duration
    
    return len(schedule), schedule

# Пример использования
tasks = [
    ("Task A", 2, 5),  # Задача, продолжительность, крайний срок
    ("Task B", 3, 7),
    ("Task C", 1, 3),
    ("Task D", 4, 6),
    ("Task E", 2, 8)
]

num_completed, schedule = schedule_tasks_with_deadlines(tasks)
print(f"\nПланирование задач с крайними сроками:")
print(f"Выполнено задач: {num_completed} из {len(tasks)}")
print("Расписание:")
for task, start, end in schedule:
    print(f"{task}: начало в {start}, окончание в {end}, крайний срок: {next(deadline for t, _, deadline in tasks if t == task)}")
```

### 2. Компрессия данных

Алгоритмы сжатия данных, такие как кодирование Хаффмана, используют оптимизационные техники для минимизации размера данных.

```python
def compress_text(text):
    """
    Сжимает текст с помощью кодирования Хаффмана.
    
    Args:
        text: Исходный текст
        
    Returns:
        Кортеж (сжатый текст, словарь кодов, коэффициент сжатия)
    """
    # Используем реализацию кодирования Хаффмана из предыдущего примера
    encoded, codes, _ = huffman_encode(text)
    
    # Вычисляем коэффициент сжатия
    original_size = len(text) * 8  # В битах (считая по 8 бит на символ)
    compressed_size = len(encoded)  # В битах
    compression_ratio = original_size / compressed_size
    
    return encoded, codes, compression_ratio

# Пример использования
text = "this is a test string for compression algorithm demonstration"

encoded, codes, ratio = compress_text(text)
print(f"\nСжатие текста:")
print(f"Исходный текст: '{text}'")
print(f"Размер исходного текста: {len(text)} символов, {len(text) * 8} бит")
print(f"Размер сжатого текста: {len(encoded)} бит")
print(f"Коэффициент сжатия: {ratio:.2f}x")
```

### 3. Маршрутизация и логистика

Оптимизационные алгоритмы применяются для нахождения оптимальных маршрутов доставки и минимизации затрат на транспортировку.

```python
def optimize_delivery_route(locations, distances):
    """
    Оптимизирует маршрут доставки с использованием приближенного решения
    задачи коммивояжера (алгоритм ближайшего соседа).
    
    Args:
        locations: Список названий локаций
        distances: Матрица расстояний между локациями
        
    Returns:
        Кортеж (оптимальный маршрут, общее расстояние)
    """
    n = len(locations)
    
    # Начинаем с первой локации (склад)
    current = 0
    route = [current]
    total_distance = 0
    unvisited = set(range(1, n))
    
    # Жадно выбираем ближайшую непосещенную локацию
    while unvisited:
        next_location = min(unvisited, key=lambda x: distances[current][x])
        total_distance += distances[current][next_location]
        current = next_location
        route.append(current)
        unvisited.remove(current)
    
    # Возвращаемся на склад
    total_distance += distances[current][0]
    route.append(0)
    
    # Преобразуем индексы в названия локаций
    named_route = [locations[i] for i in route]
    
    return named_route, total_distance

# Пример использования
locations = ["Склад", "Клиент A", "Клиент B", "Клиент C", "Клиент D"]
distances = [
    [0, 10, 15, 20, 25],
    [10, 0, 35, 25, 30],
    [15, 35, 0, 30, 10],
    [20, 25, 30, 0, 15],
    [25, 30, 10, 15, 0]
]

route, total_distance = optimize_delivery_route(locations, distances)
print(f"\nОптимизация маршрута доставки:")
print(f"Оптимальный маршрут: {' -> '.join(route)}")
print(f"Общее расстояние: {total_distance} км")
```

### 4. Финансовая оптимизация: задача формирования портфеля

Динамическое программирование используется для оптимального распределения инвестиций и максимизации доходности.

```python
def optimize_portfolio(investments, budget, min_return=0):
    """
    Оптимизирует инвестиционный портфель с учетом бюджета и минимальной доходности.
    
    Args:
        investments: Список кортежей (название, стоимость, ожидаемая доходность, риск)
        budget: Доступный бюджет
        min_return: Минимальная требуемая доходность
        
    Returns:
        Кортеж (выбранные инвестиции, общая стоимость, общая доходность, общий риск)
    """
    n = len(investments)
    
    # Сортируем инвестиции по соотношению доходность/риск (в порядке убывания)
    sorted_investments = sorted(enumerate(investments), 
                               key=lambda x: x[1][2] / x[1][3] if x[1][3] > 0 else float('inf'), 
                               reverse=True)
    
    # Оптимизация портфеля с использованием динамического программирования
    # dp[i][j] - максимальная доходность при использовании первых i инвестиций и бюджете j
    dp = [[-float('inf') for _ in range(budget + 1)] for _ in range(n + 1)]
    dp[0][0] = 0  # Начальное состояние: 0 инвестиций, 0 бюджета, 0 доходности
    
    # Хранение выбранных инвестиций
    choices = {}
    
    for i in range(1, n + 1):
        idx, (name, cost, returns, risk) = sorted_investments[i - 1]
        
        for j in range(budget + 1):
            # Не выбираем текущую инвестицию
            dp[i][j] = dp[i - 1][j]
            
            # Выбираем текущую инвестицию, если возможно
            if cost <= j and dp[i - 1][j - cost] != -float('inf'):
                new_return = dp[i - 1][j - cost] + returns
                
                if new_return > dp[i][j]:
                    dp[i][j] = new_return
                    choices[(i, j)] = True  # Запоминаем, что выбрали i-ю инвестицию при бюджете j
                else:
                    choices[(i, j)] = False  # Не выбираем i-ю инвестицию при бюджете j
            else:
                choices[(i, j)] = False
    
    # Проверяем, удовлетворяет ли решение минимальной доходности
    if dp[n][budget] < min_return:
        return [], 0, 0, 0
    
    # Восстанавливаем выбранные инвестиции
    selected = []
    remaining_budget = budget
    
    for i in range(n, 0, -1):
        if choices.get((i, remaining_budget), False):
            idx, (name, cost, returns, risk) = sorted_investments[i - 1]
            selected.append((name, cost, returns, risk))
            remaining_budget -= cost
    
    # Вычисляем общие показатели
    total_cost = sum(item[1] for item in selected)
    total_return = sum(item[2] for item in selected)
    total_risk = sum(item[3] for item in selected)
    
    return selected, total_cost, total_return, total_risk

# Пример использования
investments = [
    ("Акции A", 1000, 100, 20),  # Название, стоимость, доходность, риск
    ("Облигации B", 2000, 150, 10),
    ("Акции C", 3000, 300, 30),
    ("Недвижимость D", 5000, 500, 25),
    ("Облигации E", 1500, 120, 5)
]

budget = 5000
min_return = 300

selected, cost, returns, risk = optimize_portfolio(investments, budget, min_return)
print(f"\nОптимизация инвестиционного портфеля:")
print(f"Доступный бюджет: ${budget}")
print(f"Минимальная требуемая доходность: ${min_return}")
print(f"Выбранные инвестиции:")
for name, cost, returns, risk in selected:
    print(f"  {name}: стоимость=${cost}, доходность=${returns}, риск={risk}")
print(f"Общая стоимость: ${cost}")
print(f"Общая доходность: ${returns}")
print(f"Общий риск: {risk}")
```

### 5. Обработка изображений: сегментация изображения с использованием алгоритма с возвратом

Алгоритмы с возвратом могут использоваться для сегментации изображений и распознавания объектов.

```python
def segment_image(image, threshold):
    """
    Сегментирует изображение, выделяя связные компоненты.
    
    Args:
        image: Двумерный массив интенсивностей пикселей
        threshold: Порог интенсивности для сегментации
        
    Returns:
        Двумерный массив меток сегментов
    """
    height = len(image)
    width = len(image[0])
    
    # Создаем массив меток (0 - не посещено)
    labels = [[0 for _ in range(width)] for _ in range(height)]
    current_label = 1
    
    # Возможные направления движения (4-связность)
    directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
    
    def dfs(row, col, label):
        """
        Рекурсивно помечает связную компоненту.
        
        Args:
            row: Текущая строка
            col: Текущий столбец
            label: Текущая метка
        """
        # Выход за границы или уже посещен
        if (row < 0 or row >= height or col < 0 or col >= width or 
            labels[row][col] != 0 or image[row][col] < threshold):
            return
        
        # Помечаем пиксель
        labels[row][col] = label
        
        # Рекурсивно обходим соседей
        for dr, dc in directions:
            dfs(row + dr, col + dc, label)
    
    # Обходим изображение
    for row in range(height):
        for col in range(width):
            if image[row][col] >= threshold and labels[row][col] == 0:
                dfs(row, col, current_label)
                current_label += 1
    
    return labels, current_label - 1

def visualize_segments(labels, num_segments):
    """
    Визуализирует сегменты изображения.
    
    Args:
        labels: Двумерный массив меток сегментов
        num_segments: Количество сегментов
    """
    height = len(labels)
    width = len(labels[0])
    
    # Создаем упрощенную визуализацию
    visualization = []
    
    for row in range(height):
        row_str = ""
        for col in range(width):
            if labels[row][col] == 0:
                row_str += " "  # Фон
            else:
                # Используем разные символы для разных сегментов
                row_str += str(labels[row][col] % 10)
        
        visualization.append(row_str)
    
    return visualization

# Пример использования
# Простое изображение (интенсивности пикселей)
image = [
    [10, 10, 10, 10, 10, 10, 10, 10],
    [10, 20, 20, 10, 10, 30, 30, 10],
    [10, 20, 20, 10, 10, 30, 30, 10],
    [10, 10, 10, 10, 10, 10, 10, 10],
    [10, 10, 40, 40, 40, 10, 10, 10],
    [10, 10, 40, 40, 40, 10, 50, 50],
    [10, 10, 10, 10, 10, 10, 50, 50],
    [10, 10, 10, 10, 10, 10, 10, 10]
]

threshold = 15  # Пороговое значение для сегментации

labels, num_segments = segment_image(image, threshold)
visualization = visualize_segments(labels, num_segments)

print(f"\nСегментация изображения:")
print(f"Пороговое значение: {threshold}")
print(f"Количество найденных сегментов: {num_segments}")
print("Визуализация сегментов:")
for row in visualization:
    print(row)
```

## Заключение

Алгоритмы оптимизации — это мощные инструменты для решения широкого спектра задач, от планирования и маршрутизации до сжатия данных и обработки изображений. В этом разделе мы рассмотрели три основных подхода:

1. **Жадные алгоритмы** — простые и эффективные методы, которые выбирают локально оптимальное решение на каждом шаге. Они быстрые, но не всегда гарантируют глобальный оптимум.

2. **Динамическое программирование** — подход, основанный на разбиении задачи на подзадачи и сохранении промежуточных результатов. Динамическое программирование гарантирует оптимальное решение для задач с определенной структурой.

3. **Алгоритмы с возвратом (backtracking)** — методы систематического перебора с отсечением неперспективных ветвей. Они могут находить все возможные решения и часто применяются в задачах, требующих перебора вариантов.

При разработке алгоритмов оптимизации важно учитывать:

- **Характеристики задачи**: структуру и ограничения проблемы
- **Требования к эффективности**: время выполнения и использование памяти
- **Точность или приближенность**: нужно ли точное оптимальное решение или подойдет приближенное

Понимание этих алгоритмов и их применимости к конкретным задачам позволяет разработчику выбирать наиболее подходящий инструмент для решения практических проблем оптимизации.

---

В следующем разделе мы рассмотрим алгоритмы для веб-разработки, включая алгоритмы кеширования, ограничения скорости (rate limiting), пагинации и хеширования паролей, которые необходимы для создания эффективных и безопасных веб-приложений.