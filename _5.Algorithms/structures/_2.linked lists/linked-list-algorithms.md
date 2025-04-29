# Алгоритмы на связных списках

Связный список — это линейная структура данных, состоящая из набора узлов, каждый из которых содержит данные и ссылку (или указатель) на следующий узел в последовательности. В отличие от массивов, связные списки не хранят элементы в смежных ячейках памяти, что делает их более гибкими для определенных операций.

## Типы связных списков

### 1. Односвязный список (Singly Linked List)

В односвязном списке каждый узел содержит данные и ссылку на следующий узел.

```
[данные | ссылка] -> [данные | ссылка] -> [данные | ссылка] -> null
```

### 2. Двусвязный список (Doubly Linked List)

В двусвязном списке каждый узел содержит данные, ссылку на следующий узел и ссылку на предыдущий узел.

```
null <- [ссылка | данные | ссылка] <-> [ссылка | данные | ссылка] <-> [ссылка | данные | ссылка] -> null
```

### 3. Кольцевой связный список (Circular Linked List)

В кольцевом списке последний узел указывает не на null, а на первый узел, образуя цикл.

```
[данные | ссылка] -> [данные | ссылка] -> [данные | ссылка] ->
       ^                                                       |
       |                                                       |
       +---------------------------------------------------------+
```

## Реализация связных списков в Python

### Односвязный список

```python
class Node:
    """Узел односвязного списка."""
    def __init__(self, data):
        self.data = data  # Данные узла
        self.next = None  # Ссылка на следующий узел
    
    def __str__(self):
        return str(self.data)

class SinglyLinkedList:
    """Реализация односвязного списка."""
    def __init__(self):
        self.head = None  # Указатель на первый узел списка
    
    def is_empty(self):
        """Проверяет, пуст ли список."""
        return self.head is None
    
    def append(self, data):
        """
        Добавляет новый узел с данными в конец списка.
        
        Args:
            data: Данные для добавления
        """
        new_node = Node(data)
        
        # Если список пуст, новый узел становится головой
        if self.is_empty():
            self.head = new_node
            return
        
        # Иначе проходим по списку до последнего узла
        current = self.head
        while current.next:
            current = current.next
        
        # Добавляем новый узел после последнего
        current.next = new_node
    
    def prepend(self, data):
        """
        Добавляет новый узел с данными в начало списка.
        
        Args:
            data: Данные для добавления
        """
        new_node = Node(data)
        
        # Новый узел указывает на текущую голову
        new_node.next = self.head
        
        # Новый узел становится головой
        self.head = new_node
    
    def delete(self, data):
        """
        Удаляет первый узел с указанными данными.
        
        Args:
            data: Данные для удаления
        
        Returns:
            bool: True, если узел был удален, иначе False
        """
        if self.is_empty():
            return False
        
        # Если удаляется голова списка
        if self.head.data == data:
            self.head = self.head.next
            return True
        
        # Проходим по списку, пока не найдем узел для удаления
        current = self.head
        while current.next and current.next.data != data:
            current = current.next
        
        # Если достигнут конец списка и узел не найден
        if not current.next:
            return False
        
        # Удаляем узел, изменяя ссылки
        current.next = current.next.next
        return True
    
    def search(self, data):
        """
        Ищет узел с указанными данными.
        
        Args:
            data: Данные для поиска
        
        Returns:
            Node: Найденный узел или None, если узел не найден
        """
        current = self.head
        
        while current:
            if current.data == data:
                return current
            current = current.next
        
        return None
    
    def __str__(self):
        """Возвращает строковое представление списка."""
        values = []
        current = self.head
        
        while current:
            values.append(str(current.data))
            current = current.next
        
        return " -> ".join(values)

# Пример использования
linked_list = SinglyLinkedList()
linked_list.append(1)
linked_list.append(2)
linked_list.append(3)
linked_list.prepend(0)

print(f"Связный список: {linked_list}")

# Удаление элемента
linked_list.delete(2)
print(f"После удаления 2: {linked_list}")

# Поиск элемента
node = linked_list.search(3)
print(f"Найден узел: {node}")
```

### Двусвязный список

```python
class DoublyNode:
    """Узел двусвязного списка."""
    def __init__(self, data):
        self.data = data   # Данные узла
        self.next = None   # Ссылка на следующий узел
        self.prev = None   # Ссылка на предыдущий узел
    
    def __str__(self):
        return str(self.data)

class DoublyLinkedList:
    """Реализация двусвязного списка."""
    def __init__(self):
        self.head = None  # Указатель на первый узел списка
        self.tail = None  # Указатель на последний узел списка
    
    def is_empty(self):
        """Проверяет, пуст ли список."""
        return self.head is None
    
    def append(self, data):
        """
        Добавляет новый узел с данными в конец списка.
        
        Args:
            data: Данные для добавления
        """
        new_node = DoublyNode(data)
        
        # Если список пуст
        if self.is_empty():
            self.head = new_node
            self.tail = new_node
            return
        
        # Добавляем новый узел в конец списка
        new_node.prev = self.tail
        self.tail.next = new_node
        self.tail = new_node
    
    def prepend(self, data):
        """
        Добавляет новый узел с данными в начало списка.
        
        Args:
            data: Данные для добавления
        """
        new_node = DoublyNode(data)
        
        # Если список пуст
        if self.is_empty():
            self.head = new_node
            self.tail = new_node
            return
        
        # Добавляем новый узел в начало списка
        new_node.next = self.head
        self.head.prev = new_node
        self.head = new_node
    
    def delete(self, data):
        """
        Удаляет первый узел с указанными данными.
        
        Args:
            data: Данные для удаления
        
        Returns:
            bool: True, если узел был удален, иначе False
        """
        if self.is_empty():
            return False
        
        current = self.head
        
        # Ищем узел с указанными данными
        while current and current.data != data:
            current = current.next
        
        # Если узел не найден
        if not current:
            return False
        
        # Если узел является головой списка
        if current == self.head:
            self.head = current.next
            if self.head:
                self.head.prev = None
            else:
                # Если список стал пустым
                self.tail = None
        
        # Если узел является хвостом списка
        elif current == self.tail:
            self.tail = current.prev
            self.tail.next = None
        
        # Если узел находится в середине списка
        else:
            current.prev.next = current.next
            current.next.prev = current.prev
        
        return True
    
    def search(self, data):
        """
        Ищет узел с указанными данными.
        
        Args:
            data: Данные для поиска
        
        Returns:
            DoublyNode: Найденный узел или None, если узел не найден
        """
        current = self.head
        
        while current:
            if current.data == data:
                return current
            current = current.next
        
        return None
    
    def forward_traversal(self):
        """Возвращает список элементов при прямом обходе."""
        values = []
        current = self.head
        
        while current:
            values.append(str(current.data))
            current = current.next
        
        return " <-> ".join(values)
    
    def backward_traversal(self):
        """Возвращает список элементов при обратном обходе."""
        values = []
        current = self.tail
        
        while current:
            values.append(str(current.data))
            current = current.prev
        
        return " <-> ".join(values)
    
    def __str__(self):
        """Возвращает строковое представление списка."""
        return self.forward_traversal()

# Пример использования
doubly_list = DoublyLinkedList()
doubly_list.append(1)
doubly_list.append(2)
doubly_list.append(3)
doubly_list.prepend(0)

print(f"Двусвязный список (прямой обход): {doubly_list}")
print(f"Двусвязный список (обратный обход): {doubly_list.backward_traversal()}")

# Удаление элемента
doubly_list.delete(2)
print(f"После удаления 2: {doubly_list}")
```

## Сложность операций

| Операция | Односвязный список | Двусвязный список | Пояснение |
|----------|-------------------|-------------------|-----------|
| Добавление в начало | O(1) | O(1) | Изменение только указателей на head |
| Добавление в конец | O(n) для списка без указателя на tail, O(1) с указателем | O(1) | Прямой доступ к последнему элементу через tail |
| Вставка в середину | O(n) | O(n) | Необходим проход до позиции вставки |
| Удаление из начала | O(1) | O(1) | Изменение только указателей на head |
| Удаление из конца | O(n) | O(1) | В двусвязном списке можно перейти к предпоследнему узлу через указатель prev |
| Удаление из середины | O(n) | O(n) | Необходим проход до узла |
| Поиск | O(n) | O(n) | Необходим последовательный проход |
| Доступ по индексу | O(n) | O(n) | Необходим последовательный проход |

## Расширенные операции и алгоритмы на связных списках

### 1. Обращение (реверс) связного списка

Один из классических алгоритмов — изменение порядка элементов в связном списке на обратный.

```python
def reverse_linked_list(linked_list):
    """
    Обращает порядок узлов в односвязном списке.
    
    Args:
        linked_list: Односвязный список
        
    Returns:
        SinglyLinkedList: Обращенный список
    """
    # Если список пуст или содержит один элемент
    if linked_list.is_empty() or linked_list.head.next is None:
        return linked_list
    
    prev = None         # Предыдущий узел
    current = linked_list.head  # Текущий узел
    next_node = None    # Следующий узел
    
    while current:
        # Сохраняем ссылку на следующий узел
        next_node = current.next
        
        # Меняем направление указателя
        current.next = prev
        
        # Перемещаем указатели prev и current на одну позицию вперед
        prev = current
        current = next_node
    
    # Обновляем голову списка
    linked_list.head = prev
    
    return linked_list

# Пример использования
linked_list = SinglyLinkedList()
linked_list.append(1)
linked_list.append(2)
linked_list.append(3)
linked_list.append(4)

print(f"Исходный список: {linked_list}")
reverse_linked_list(linked_list)
print(f"Обращенный список: {linked_list}")
```

### 2. Обнаружение циклов в связном списке (алгоритм Флойда)

Алгоритм Флойда (также известный как алгоритм "черепахи и зайца") используется для обнаружения циклов в связном списке.

```python
def has_cycle(linked_list):
    """
    Проверяет, содержит ли связный список цикл, используя алгоритм Флойда.
    
    Args:
        linked_list: Односвязный список
        
    Returns:
        bool: True, если список содержит цикл, иначе False
    """
    if linked_list.is_empty() or linked_list.head.next is None:
        return False
    
    # Инициализируем два указателя (медленный и быстрый)
    slow = linked_list.head
    fast = linked_list.head
    
    while fast and fast.next:
        # Медленный указатель двигается на один шаг
        slow = slow.next
        
        # Быстрый указатель двигается на два шага
        fast = fast.next.next
        
        # Если указатели встретились, список содержит цикл
        if slow == fast:
            return True
    
    # Если fast указатель достиг конца списка, цикла нет
    return False

# Пример использования
# Создаем список с циклом для тестирования
cycle_list = SinglyLinkedList()
cycle_list.append(1)
cycle_list.append(2)
cycle_list.append(3)
cycle_list.append(4)

# Создаем цикл (4 указывает на 2)
current = cycle_list.head
while current.next:
    current = current.next
    
# Находим узел с данными 2
node_2 = cycle_list.head
while node_2 and node_2.data != 2:
    node_2 = node_2.next

# Создаем цикл
current.next = node_2

# Проверяем наличие цикла
has_cycle_result = has_cycle(cycle_list)
print(f"Список содержит цикл: {has_cycle_result}")

# Проверяем список без цикла
normal_list = SinglyLinkedList()
normal_list.append(1)
normal_list.append(2)
normal_list.append(3)

no_cycle_result = has_cycle(normal_list)
print(f"Обычный список содержит цикл: {no_cycle_result}")
```

### 3. Нахождение точки начала цикла в связном списке

Расширение алгоритма Флойда для нахождения узла, с которого начинается цикл.

```python
def find_cycle_start(linked_list):
    """
    Находит начальный узел цикла в связном списке, если цикл существует.
    
    Args:
        linked_list: Односвязный список
        
    Returns:
        Node: Узел, с которого начинается цикл, или None, если цикла нет
    """
    if linked_list.is_empty() or linked_list.head.next is None:
        return None
    
    # Инициализируем два указателя
    slow = linked_list.head
    fast = linked_list.head
    
    # Первая фаза: определяем, есть ли цикл
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        # Если указатели встретились, список содержит цикл
        if slow == fast:
            break
    
    # Если fast указатель достиг конца списка, цикла нет
    if not fast or not fast.next:
        return None
    
    # Вторая фаза: находим начало цикла
    # Перемещаем slow указатель в начало списка
    slow = linked_list.head
    
    # Двигаем оба указателя с одинаковой скоростью
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    # Когда указатели снова встретятся, это будет начало цикла
    return slow

# Пример использования (с тем же cycle_list из предыдущего примера)
cycle_start = find_cycle_start(cycle_list)
if cycle_start:
    print(f"Начало цикла: узел с данными {cycle_start.data}")
else:
    print("Цикл не найден")
```

### 4. Удаление N-го узла с конца списка

Задача: удалить N-й узел с конца односвязного списка за один проход.

```python
def remove_nth_from_end(linked_list, n):
    """
    Удаляет N-й узел с конца односвязного списка.
    
    Args:
        linked_list: Односвязный список
        n: Позиция узла с конца (1 - последний узел, 2 - предпоследний и т.д.)
        
    Returns:
        bool: True, если узел был удален, иначе False
    """
    if linked_list.is_empty() or n <= 0:
        return False
    
    # Используем два указателя с разницей в n узлов
    fast = linked_list.head
    slow = linked_list.head
    
    # Перемещаем fast указатель на n узлов вперед
    for _ in range(n):
        if not fast:
            return False  # Список короче n узлов
        fast = fast.next
    
    # Если fast достиг конца, удаляем головной узел
    if not fast:
        linked_list.head = linked_list.head.next
        return True
    
    # Двигаем оба указателя, пока fast не достигнет конца списка
    while fast.next:
        slow = slow.next
        fast = fast.next
    
    # Теперь slow указывает на узел перед тем, который нужно удалить
    slow.next = slow.next.next
    
    return True

# Пример использования
list_for_removal = SinglyLinkedList()
list_for_removal.append(1)
list_for_removal.append(2)
list_for_removal.append(3)
list_for_removal.append(4)
list_for_removal.append(5)

print(f"Исходный список: {list_for_removal}")
remove_nth_from_end(list_for_removal, 2)  # Удаляем предпоследний элемент (4)
print(f"После удаления 2-го с конца: {list_for_removal}")
```

### 5. Объединение двух отсортированных списков

Задача: объединить два отсортированных списка в один отсортированный список.

```python
def merge_sorted_lists(list1, list2):
    """
    Объединяет два отсортированных списка в один отсортированный список.
    
    Args:
        list1: Первый отсортированный односвязный список
        list2: Второй отсортированный односвязный список
        
    Returns:
        SinglyLinkedList: Новый отсортированный список
    """
    # Создаем новый список для результата
    merged_list = SinglyLinkedList()
    
    # Если один из списков пуст, возвращаем другой
    if list1.is_empty():
        return list2
    if list2.is_empty():
        return list1
    
    # Указатели на текущие узлы в обоих списках
    current1 = list1.head
    current2 = list2.head
    
    # Пока оба списка не закончились
    while current1 and current2:
        if current1.data <= current2.data:
            merged_list.append(current1.data)
            current1 = current1.next
        else:
            merged_list.append(current2.data)
            current2 = current2.next
    
    # Добавляем оставшиеся элементы из первого списка
    while current1:
        merged_list.append(current1.data)
        current1 = current1.next
    
    # Добавляем оставшиеся элементы из второго списка
    while current2:
        merged_list.append(current2.data)
        current2 = current2.next
    
    return merged_list

# Пример использования
list1 = SinglyLinkedList()
list1.append(1)
list1.append(3)
list1.append(5)

list2 = SinglyLinkedList()
list2.append(2)
list2.append(4)
list2.append(6)

print(f"Первый список: {list1}")
print(f"Второй список: {list2}")

merged_list = merge_sorted_lists(list1, list2)
print(f"Объединенный список: {merged_list}")
```

### 6. Разделение списка на две части

Задача: разделить список на две части так, чтобы первая часть содержала элементы, удовлетворяющие заданному условию, а вторая - все остальные.

```python
def partition_list(linked_list, pivot):
    """
    Разделяет список на две части: узлы со значениями меньше pivot идут перед 
    узлами со значениями, большими или равными pivot.
    
    Args:
        linked_list: Односвязный список
        pivot: Значение для разделения
        
    Returns:
        SinglyLinkedList: Разделенный список
    """
    if linked_list.is_empty() or linked_list.head.next is None:
        return linked_list
    
    # Создаем два dummy-узла для начала двух частей
    before_head = Node(0)
    before = before_head
    
    after_head = Node(0)
    after = after_head
    
    current = linked_list.head
    
    # Проходим по исходному списку и распределяем узлы
    while current:
        if current.data < pivot:
            before.next = current
            before = before.next
        else:
            after.next = current
            after = after.next
        
        current = current.next
    
    # Завершаем вторую часть
    after.next = None
    
    # Соединяем две части
    before.next = after_head.next
    
    # Обновляем голову исходного списка
    linked_list.head = before_head.next
    
    return linked_list

# Пример использования
partition_list_example = SinglyLinkedList()
partition_list_example.append(3)
partition_list_example.append(5)
partition_list_example.append(8)
partition_list_example.append(2)
partition_list_example.append(10)
partition_list_example.append(1)

print(f"Исходный список: {partition_list_example}")
partition_list(partition_list_example, 5)  # Разделяем по значению 5
print(f"Список после разделения: {partition_list_example}")
```

### 7. Проверка, является ли список палиндромом

Задача: проверить, читается ли связный список одинаково в обоих направлениях.

```python
def is_palindrome(linked_list):
    """
    Проверяет, является ли связный список палиндромом.
    
    Args:
        linked_list: Односвязный список
        
    Returns:
        bool: True, если список является палиндромом, иначе False
    """
    if linked_list.is_empty() or linked_list.head.next is None:
        return True
    
    # Находим середину списка с помощью техники быстрого и медленного указателей
    slow = linked_list.head
    fast = linked_list.head
    
    # Стек для хранения первой половины элементов
    stack = []
    
    # Добавляем элементы из первой половины списка в стек
    while fast and fast.next:
        stack.append(slow.data)
        slow = slow.next
        fast = fast.next.next
    
    # Если fast не None, значит список имеет нечетную длину
    # Пропускаем средний элемент
    if fast:
        slow = slow.next
    
    # Сравниваем вторую половину списка с элементами в стеке
    while slow:
        if slow.data != stack.pop():
            return False
        slow = slow.next
    
    return True

# Пример использования
palindrome_list = SinglyLinkedList()
palindrome_list.append(1)
palindrome_list.append(2)
palindrome_list.append(3)
palindrome_list.append(2)
palindrome_list.append(1)

print(f"Список: {palindrome_list}")
print(f"Является палиндромом: {is_palindrome(palindrome_list)}")

non_palindrome_list = SinglyLinkedList()
non_palindrome_list.append(1)
non_palindrome_list.append(2)
non_palindrome_list.append(3)
non_palindrome_list.append(4)

print(f"Список: {non_palindrome_list}")
print(f"Является палиндромом: {is_palindrome(non_palindrome_list)}")
```

## Практическое применение в веб-разработке

### 1. Реализация LRU-кеша

LRU (Least Recently Used) кеш - это структура данных, которая хранит ограниченное количество элементов и удаляет наименее недавно использованные элементы, когда достигает своего лимита.

```python
class LRUCacheNode:
    """Узел для LRU-кеша."""
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class LRUCache:
    """
    Реализация LRU-кеша с использованием двусвязного списка и хеш-таблицы.
    """
    def __init__(self, capacity):
        """
        Инициализирует кеш с заданной емкостью.
        
        Args:
            capacity: Максимальное количество элементов в кеше
        """
        self.capacity = capacity
        self.cache = {}  # Словарь для быстрого доступа: {ключ: узел}
        
        # Создаем фиктивные головной и хвостовой узлы
        self.head = LRUCacheNode(0, 0)
        self.tail = LRUCacheNode(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _add_node(self, node):
        """
        Добавляет узел после головы (в начало списка).
        
        Args:
            node: Узел для добавления
        """
        node.prev = self.head
        node.next = self.head.next
        
        self.head.next.prev = node
        self.head.next = node
    
    def _remove_node(self, node):
        """
        Удаляет узел из списка.
        
        Args:
            node: Узел для удаления
        """
        prev = node.prev
        next_node = node.next
        
        prev.next = next_node
        next_node.prev = prev
    
    def _move_to_head(self, node):
        """
        Перемещает узел в начало списка (после головы).
        
        Args:
            node: Узел для перемещения
        """
        self._remove_node(node)
        self._add_node(node)
    
    def _pop_tail(self):
        """
        Удаляет хвостовой узел (перед фиктивным хвостом).
        
        Returns:
            LRUCacheNode: Удаленный узел
        """
        res = self.tail.prev
        self._remove_node(res)
        return res
    
    def get(self, key):
        """
        Получает значение по ключу и обновляет порядок узлов.
        
        Args:
            key: Ключ для поиска
            
        Returns:
            Значение, если ключ найден, иначе -1
        """
        node = self.cache.get(key)
        
        # Если ключ не найден
        if not node:
            return -1
        
        # Перемещаем узел в начало (обновляем его как недавно использованный)
        self._move_to_head(node)
        
        return node.value
    
    def put(self, key, value):
        """
        Добавляет или обновляет пару ключ-значение.
        
        Args:
            key: Ключ для добавления/обновления
            value: Значение
        """
        node = self.cache.get(key)
        
        # Если ключ существует, обновляем значение и перемещаем узел в начало
        if node:
            node.value = value
            self._move_to_head(node)
            return
        
        # Создаем новый узел
        new_node = LRUCacheNode(key, value)
        self.cache[key] = new_node
        self._add_node(new_node)
        
        # Если превышена емкость, удаляем наименее недавно использованный элемент
        if len(self.cache) > self.capacity:
            tail = self._pop_tail()
            del self.cache[tail.key]
    
    def display(self):
        """Выводит содержимое кеша в порядке от наиболее до наименее недавно использованных."""
        values = []
        current = self.head.next
        
        while current != self.tail:
            values.append(f"{current.key}:{current.value}")
            current = current.next
        
        return " -> ".join(values)

# Пример использования
lru_cache = LRUCache(3)  # Кеш емкостью 3 элемента

lru_cache.put(1, "one")
lru_cache.put(2, "two")
lru_cache.put(3, "three")

print(f"Кеш после добавления трех элементов: {lru_cache.display()}")

# Получаем значение по ключу 1 (перемещает его в начало)
print(f"Значение для ключа 1: {lru_cache.get(1)}")
print(f"Кеш после получения элемента 1: {lru_cache.display()}")

# Добавляем новый элемент, который вытеснит наименее недавно использованный (2)
lru_cache.put(4, "four")
print(f"Кеш после добавления элемента 4: {lru_cache.display()}")

# Пытаемся получить вытесненный элемент
print(f"Значение для ключа 2: {lru_cache.get(2)}")
```

### 2. Управление историей просмотра в браузере

Реализация структуры данных для управления историей просмотра веб-страниц с возможностью перемещения вперед и назад.

```python
class BrowserHistory:
    """
    Реализация истории просмотра в браузере с использованием двусвязного списка.
    """
    class PageNode:
        """Узел, представляющий веб-страницу."""
        def __init__(self, url, title=None):
            self.url = url
            self.title = title or url
            self.prev = None
            self.next = None
    
    def __init__(self, homepage):
        """
        Инициализирует историю с домашней страницей.
        
        Args:
            homepage: URL домашней страницы
        """
        self.current = self.PageNode(homepage)
    
    def visit(self, url, title=None):
        """
        Посещает новую страницу.
        
        Args:
            url: URL новой страницы
            title: Заголовок страницы (опционально)
        """
        # Создаем новый узел для страницы
        new_page = self.PageNode(url, title)
        
        # Связываем с текущей страницей
        new_page.prev = self.current
        self.current.next = new_page
        
        # Обновляем текущую страницу
        self.current = new_page
        
        print(f"Посещена страница: {url}")
    
    def back(self):
        """
        Переходит на предыдущую страницу в истории.
        
        Returns:
            str: URL предыдущей страницы или None, если невозможно перейти назад
        """
        if self.current.prev:
            self.current = self.current.prev
            print(f"Переход назад на: {self.current.url}")
            return self.current.url
        else:
            print("Нельзя перейти назад")
            return None
    
    def forward(self):
        """
        Переходит на следующую страницу в истории.
        
        Returns:
            str: URL следующей страницы или None, если невозможно перейти вперед
        """
        if self.current.next:
            self.current = self.current.next
            print(f"Переход вперед на: {self.current.url}")
            return self.current.url
        else:
            print("Нельзя перейти вперед")
            return None
    
    def get_current_page(self):
        """
        Возвращает информацию о текущей странице.
        
        Returns:
            dict: Информация о текущей странице
        """
        return {
            'url': self.current.url,
            'title': self.current.title,
            'can_go_back': self.current.prev is not None,
            'can_go_forward': self.current.next is not None
        }
    
    def get_history(self, limit=10):
        """
        Возвращает список последних посещенных страниц.
        
        Args:
            limit: Максимальное количество страниц для возврата
            
        Returns:
            list: Список URL страниц
        """
        history = []
        page = self.current
        
        # Переходим назад до первой страницы или достижения лимита
        while page and len(history) < limit:
            history.append(page.url)
            page = page.prev
        
        # Переворачиваем список, чтобы получить хронологический порядок
        return history[::-1]

# Пример использования
browser = BrowserHistory("https://example.com")

# Посещаем несколько страниц
browser.visit("https://example.com/page1", "Page 1")
browser.visit("https://example.com/page2", "Page 2")
browser.visit("https://example.com/page3", "Page 3")

# Получаем информацию о текущей странице
current_page = browser.get_current_page()
print(f"Текущая страница: {current_page['title']} ({current_page['url']})")

# Переходим назад
browser.back()
current_page = browser.get_current_page()
print(f"Текущая страница после перехода назад: {current_page['title']} ({current_page['url']})")

# Переходим вперед
browser.forward()
current_page = browser.get_current_page()
print(f"Текущая страница после перехода вперед: {current_page['title']} ({current_page['url']})")

# Посещаем новую страницу (это удалит все страницы в истории вперед)
browser.visit("https://example.com/page4", "Page 4")

# Пытаемся перейти вперед (должно быть невозможно)
browser.forward()

# Выводим историю
history = browser.get_history()
print("История просмотра:")
for url in history:
    print(f"  {url}")
```

### 3. Управление очередью задач с разными приоритетами

Реализация очереди задач для фоновых процессов в веб-приложении с поддержкой приоритетов.

```python
class TaskNode:
    """Узел для задачи."""
    def __init__(self, task_id, name, priority, payload=None):
        self.task_id = task_id
        self.name = name
        self.priority = priority  # Меньшее значение = более высокий приоритет
        self.payload = payload or {}
        self.next = None
        self.prev = None
    
    def __str__(self):
        return f"Task {self.task_id}: {self.name} (приоритет: {self.priority})"

class PriorityTaskQueue:
    """
    Очередь задач с приоритетами, реализованная с использованием двусвязного списка.
    """
    def __init__(self):
        self.head = None
        self.tail = None
        self.task_count = 0
        self.tasks_by_id = {}  # Для быстрого доступа к задачам по ID
    
    def is_empty(self):
        """Проверяет, пуста ли очередь."""
        return self.head is None
    
    def enqueue(self, task_id, name, priority, payload=None):
        """
        Добавляет новую задачу в очередь с учетом приоритета.
        
        Args:
            task_id: Уникальный идентификатор задачи
            name: Название задачи
            priority: Приоритет задачи (меньшее значение = более высокий приоритет)
            payload: Дополнительные данные задачи
            
        Returns:
            TaskNode: Добавленный узел задачи
        """
        # Проверяем, существует ли задача с таким ID
        if task_id in self.tasks_by_id:
            raise ValueError(f"Задача с ID {task_id} уже существует")
        
        # Создаем новый узел задачи
        new_task = TaskNode(task_id, name, priority, payload)
        
        # Если очередь пуста
        if self.is_empty():
            self.head = new_task
            self.tail = new_task
            self.tasks_by_id[task_id] = new_task
            self.task_count += 1
            return new_task
        
        # Если новая задача имеет наивысший приоритет
        if priority < self.head.priority:
            new_task.next = self.head
            self.head.prev = new_task
            self.head = new_task
            self.tasks_by_id[task_id] = new_task
            self.task_count += 1
            return new_task
        
        # Находим нужную позицию для вставки
        current = self.head
        while current.next and current.next.priority <= priority:
            current = current.next
        
        # Вставляем новую задачу
        new_task.next = current.next
        new_task.prev = current
        
        if current.next:
            current.next.prev = new_task
        else:
            self.tail = new_task
        
        current.next = new_task
        
        self.tasks_by_id[task_id] = new_task
        self.task_count += 1
        return new_task
    
    def dequeue(self):
        """
        Извлекает задачу с наивысшим приоритетом.
        
        Returns:
            TaskNode: Извлеченный узел задачи или None, если очередь пуста
        """
        if self.is_empty():
            return None
        
        task = self.head
        self.head = self.head.next
        
        if self.head:
            self.head.prev = None
        else:
            self.tail = None
        
        del self.tasks_by_id[task.task_id]
        self.task_count -= 1
        
        return task
    
    def get_task(self, task_id):
        """
        Получает задачу по ID без удаления из очереди.
        
        Args:
            task_id: ID задачи
            
        Returns:
            TaskNode: Узел задачи или None, если задача не найдена
        """
        return self.tasks_by_id.get(task_id)
    
    def update_priority(self, task_id, new_priority):
        """
        Обновляет приоритет задачи и перемещает её в соответствующую позицию.
        
        Args:
            task_id: ID задачи
            new_priority: Новый приоритет
            
        Returns:
            TaskNode: Обновленный узел задачи или None, если задача не найдена
        """
        task = self.get_task(task_id)
        if not task:
            return None
        
        # Если приоритет не изменился
        if task.priority == new_priority:
            return task
        
        # Удаляем задачу из текущей позиции
        if task.prev:
            task.prev.next = task.next
        else:
            self.head = task.next
        
        if task.next:
            task.next.prev = task.prev
        else:
            self.tail = task.prev
        
        # Обновляем приоритет
        task.priority = new_priority
        task.next = None
        task.prev = None
        
        # Вставляем задачу в новую позицию
        # Если очередь пуста после удаления
        if self.is_empty():
            self.head = task
            self.tail = task
            return task
        
        # Если задача имеет наивысший приоритет
        if new_priority < self.head.priority:
            task.next = self.head
            self.head.prev = task
            self.head = task
            return task
        
        # Находим нужную позицию для вставки
        current = self.head
        while current.next and current.next.priority <= new_priority:
            current = current.next
        
        # Вставляем задачу
        task.next = current.next
        task.prev = current
        
        if current.next:
            current.next.prev = task
        else:
            self.tail = task
        
        current.next = task
        
        return task
    
    def cancel_task(self, task_id):
        """
        Отменяет задачу, удаляя её из очереди.
        
        Args:
            task_id: ID задачи
            
        Returns:
            TaskNode: Удаленный узел задачи или None, если задача не найдена
        """
        task = self.get_task(task_id)
        if not task:
            return None
        
        # Удаляем задачу из списка
        if task.prev:
            task.prev.next = task.next
        else:
            self.head = task.next
        
        if task.next:
            task.next.prev = task.prev
        else:
            self.tail = task.prev
        
        del self.tasks_by_id[task_id]
        self.task_count -= 1
        
        return task
    
    def get_all_tasks(self):
        """
        Возвращает список всех задач в очереди.
        
        Returns:
            list: Список узлов задач
        """
        tasks = []
        current = self.head
        
        while current:
            tasks.append(current)
            current = current.next
        
        return tasks
    
    def __str__(self):
        """Возвращает строковое представление очереди."""
        values = []
        current = self.head
        
        while current:
            values.append(f"({current.task_id}: {current.name}, приоритет={current.priority})")
            current = current.next
        
        return " -> ".join(values) if values else "Очередь пуста"

# Пример использования
task_queue = PriorityTaskQueue()

# Добавляем задачи с разными приоритетами
task_queue.enqueue("task1", "Отправить email", 3, {"recipient": "user@example.com"})
task_queue.enqueue("task2", "Обработать платеж", 1, {"amount": 100.0})
task_queue.enqueue("task3", "Обновить кеш", 2, {"cache_key": "products"})
task_queue.enqueue("task4", "Сгенерировать отчет", 3, {"report_type": "monthly"})

print(f"Очередь задач: {task_queue}")

# Обновляем приоритет задачи
task_queue.update_priority("task3", 1)
print(f"Очередь после обновления приоритета: {task_queue}")

# Обрабатываем задачи по приоритету
print("\nОбработка задач:")
while not task_queue.is_empty():
    task = task_queue.dequeue()
    print(f"Обработка задачи: {task}, данные: {task.payload}")
```

### 4. Реализация пула соединений для базы данных

Пул соединений для базы данных, который эффективно управляет ограниченным набором соединений.

```python
import time
import threading
from queue import Queue

class DatabaseConnection:
    """Имитация соединения с базой данных."""
    def __init__(self, connection_id):
        self.connection_id = connection_id
        self.is_open = True
        self.last_used = time.time()
    
    def execute_query(self, query, params=None):
        """
        Выполняет SQL-запрос.
        
        Args:
            query: SQL-запрос
            params: Параметры запроса (опционально)
            
        Returns:
            Результат запроса (имитация)
        """
        if not self.is_open:
            raise ValueError("Соединение закрыто")
        
        # Имитируем выполнение запроса
        time.sleep(0.1)
        self.last_used = time.time()
        
        print(f"Соединение {self.connection_id} выполняет запрос: {query}")
        
        # Возвращаем имитированный результат
        return {
            "connection_id": self.connection_id,
            "query": query,
            "params": params,
            "rows": [],
            "timestamp": self.last_used
        }
    
    def close(self):
        """Закрывает соединение."""
        if self.is_open:
            print(f"Соединение {self.connection_id} закрыто")
            self.is_open = False

class DatabaseConnectionPool:
    """
    Пул соединений для базы данных.
    """
    def __init__(self, pool_size=5, max_idle_time=60):
        """
        Инициализирует пул соединений.
        
        Args:
            pool_size: Максимальное количество соединений в пуле
            max_idle_time: Максимальное время простоя соединения в секундах
        """
        self.pool_size = pool_size
        self.max_idle_time = max_idle_time
        
        self.connections = Queue(maxsize=pool_size)  # Очередь доступных соединений
        self.active_connections = {}  # Активные соединения: {соединение: время получения}
        
        self.lock = threading.RLock()  # Блокировка для потокобезопасности
        self.connection_count = 0  # Общее количество созданных соединений
        
        # Заполняем пул начальными соединениями
        self._fill_pool()
        
        # Запускаем поток для очистки старых соединений
        self.cleanup_thread = threading.Thread(target=self._cleanup_idle_connections, daemon=True)
        self.cleanup_thread.start()
    
    def _fill_pool(self):
        """Заполняет пул начальными соединениями."""
        with self.lock:
            while not self.connections.full() and self.connection_count < self.pool_size:
                connection = self._create_connection()
                self.connections.put(connection)
    
    def _create_connection(self):
        """
        Создает новое соединение с базой данных.
        
        Returns:
            DatabaseConnection: Новое соединение
        """
        with self.lock:
            self.connection_count += 1
            connection_id = self.connection_count
        
        print(f"Создано новое соединение с ID {connection_id}")
        return DatabaseConnection(connection_id)
    
    def get_connection(self, timeout=None):
        """
        Получает соединение из пула.
        
        Args:
            timeout: Таймаут ожидания в секундах
            
        Returns:
            DatabaseConnection: Соединение из пула
            
        Raises:
            Exception: Если соединение не удалось получить в течение таймаута
        """
        try:
            # Пытаемся получить соединение из очереди
            connection = self.connections.get(block=True, timeout=timeout)
            
            # Если соединение закрыто, создаем новое
            if not connection.is_open:
                print(f"Соединение {connection.connection_id} закрыто, создаем новое")
                connection = self._create_connection()
            
            # Отмечаем соединение как активное
            with self.lock:
                self.active_connections[connection] = time.time()
            
            return connection
        
        except Exception as e:
            print(f"Не удалось получить соединение: {e}")
            raise
    
    def release_connection(self, connection):
        """
        Возвращает соединение в пул.
        
        Args:
            connection: Соединение для возврата
        """
        with self.lock:
            # Удаляем из активных соединений
            if connection in self.active_connections:
                del self.active_connections[connection]
            
            # Если соединение открыто, возвращаем в пул
            if connection.is_open:
                connection.last_used = time.time()
                try:
                    self.connections.put_nowait(connection)
                    print(f"Соединение {connection.connection_id} возвращено в пул")
                except Exception as e:
                    print(f"Не удалось вернуть соединение в пул: {e}")
                    connection.close()
            else:
                print(f"Соединение {connection.connection_id} закрыто, не возвращаем в пул")
    
    def _cleanup_idle_connections(self):
        """Периодически проверяет и закрывает старые неиспользуемые соединения."""
        while True:
            time.sleep(self.max_idle_time / 2)  # Проверяем в два раза чаще, чем max_idle_time
            
            with self.lock:
                # Извлекаем все соединения из очереди
                temp_connections = []
                while not self.connections.empty():
                    try:
                        connection = self.connections.get_nowait()
                        temp_connections.append(connection)
                    except:
                        break
                
                # Проверяем каждое соединение
                for connection in temp_connections:
                    # Если соединение слишком старое, закрываем его
                    if time.time() - connection.last_used > self.max_idle_time:
                        print(f"Закрываем старое соединение {connection.connection_id}")
                        connection.close()
                        # Создаем новое соединение вместо закрытого
                        new_connection = self._create_connection()
                        self.connections.put(new_connection)
                    else:
                        # Возвращаем соединение в очередь
                        self.connections.put(connection)
    
    def close_all_connections(self):
        """Закрывает все соединения в пуле."""
        with self.lock:
            # Закрываем активные соединения
            for connection in list(self.active_connections.keys()):
                connection.close()
            
            self.active_connections.clear()
            
            # Закрываем соединения в очереди
            while not self.connections.empty():
                try:
                    connection = self.connections.get_nowait()
                    connection.close()
                except:
                    break
            
            print("Все соединения закрыты")
    
    def get_status(self):
        """
        Возвращает информацию о состоянии пула.
        
        Returns:
            dict: Информация о пуле соединений
        """
        with self.lock:
            return {
                "pool_size": self.pool_size,
                "available_connections": self.connections.qsize(),
                "active_connections": len(self.active_connections),
                "total_connections": self.connection_count
            }

# Пример использования
def example_usage():
    # Создаем пул с 3 соединениями
    pool = DatabaseConnectionPool(pool_size=3, max_idle_time=5)
    
    try:
        # Выводим начальное состояние пула
        print(f"Начальное состояние пула: {pool.get_status()}")
        
        # Получаем соединение из пула
        conn1 = pool.get_connection()
        print(f"Получено соединение: {conn1.connection_id}")
        print(f"Состояние пула после получения соединения: {pool.get_status()}")
        
        # Выполняем запрос
        result = conn1.execute_query("SELECT * FROM users WHERE id = %s", [123])
        print(f"Результат запроса: {result}")
        
        # Получаем еще два соединения
        conn2 = pool.get_connection()
        conn3 = pool.get_connection()
        print(f"Состояние пула после получения всех соединений: {pool.get_status()}")
        
        # Пытаемся получить еще одно соединение (должно блокироваться)
        print("Пытаемся получить еще одно соединение (с таймаутом 1 сек)...")
        try:
            conn4 = pool.get_connection(timeout=1)
        except Exception as e:
            print(f"Не удалось получить соединение: {e}")
        
        # Возвращаем соединение в пул
        pool.release_connection(conn1)
        print(f"Состояние пула после возврата соединения: {pool.get_status()}")
        
        # Теперь должны успешно получить новое соединение
        conn4 = pool.get_connection()
        print(f"Получено соединение: {conn4.connection_id}")
        
        # Возвращаем все соединения
        pool.release_connection(conn2)
        pool.release_connection(conn3)
        pool.release_connection(conn4)
        
        print(f"Финальное состояние пула: {pool.get_status()}")
        
        # Ждем, чтобы увидеть работу очистки старых соединений
        print("\nОжидаем 6 секунд для проверки очистки старых соединений...")
        time.sleep(6)
        
        # Получаем соединение после очистки
        conn = pool.get_connection()
        print(f"Получено соединение после очистки: {conn.connection_id}")
        pool.release_connection(conn)
        
    finally:
        # Закрываем все соединения
        pool.close_all_connections()

# Раскомментируйте для запуска примера
# example_usage()
```

## Заключение

Связные списки — это гибкие и мощные структуры данных, которые находят применение во многих областях программирования, включая веб-разработку. В отличие от массивов, они обеспечивают эффективную вставку и удаление элементов за O(1) при условии, что известен указатель на нужный узел.

Основные преимущества связных списков:

1. **Динамический размер**: Связные списки могут расти и уменьшаться во время выполнения программы, не требуя предварительного выделения памяти.

2. **Эффективные вставка и удаление**: Вставка и удаление элементов в начало списка или после известного узла выполняются за O(1).

3. **Отсутствие перемещения элементов**: В отличие от массивов, вставка и удаление не требуют перемещения других элементов.

4. **Эффективное использование памяти**: Связные списки выделяют память только для хранимых элементов, в то время как массивы часто выделяют больше памяти, чем необходимо.

Однако связные списки имеют и недостатки:

1. **Дополнительная память на указатели**: Каждый узел требует дополнительной памяти для хранения указателей.

2. **Отсутствие произвольного доступа**: Для доступа к n-му элементу необходимо пройти через n-1 предшествующих элементов.

3. **Кеш-неэффективность**: Из-за разбросанного размещения в памяти связные списки менее эффективно используют кеш процессора, что может снизить производительность.

В веб-разработке связные списки и алгоритмы на их основе находят применение для:

- Реализации кешей (например, LRU-кеш)
- Управления историей в приложениях
- Организации очередей задач с приоритетами
- Управления пулами соединений с базами данных
- Обработки потоков данных

Понимание алгоритмов на связных списках и их правильное применение может значительно повысить эффективность и надежность веб-приложений, особенно при работе с динамическими структурами данных и ресурсоемкими операциями.

---

В следующем разделе мы рассмотрим алгоритмы на хеш-таблицах, которые обеспечивают эффективный доступ к данным по ключу и широко используются в веб-разработке для индексирования, кеширования и организации данных.