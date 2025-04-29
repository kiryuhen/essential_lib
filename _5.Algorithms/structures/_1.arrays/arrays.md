**Массивы**

## 1. Описание
Массив — структура данных, представляющая собой упорядоченную коллекцию элементов одного типа, размещённых в непрерывном блоке памяти. Каждый элемент доступен по индексу — целому числу от 0 до n-1, где n — количество элементов.

**Цель и применение:**
- Быстрый произвольный доступ к элементам по индексу.
- Базовая основа для реализации других структур данных (строки, списки, хеш-таблицы и т. д.).
- Используется в системах, где важно постоянство времени доступа.

## 2. Плюсы и минусы
**Плюсы:**
- Доступ к элементу по индексу за O(1).
- Простой компактный механизм хранения.

**Минусы:**
- Вставка или удаление в середине массива требует сдвига элементов, что занимает O(n).
- Фиксированный размер (в статических языках) либо дорогая операция ресайзинга (в динамических).

## 3. Основные операции и их сложности
| Операция                           | Сложность      |
|------------------------------------|----------------|
| Доступ по индексу                  | O(1)           |
| Вставка/удаление (в середине)      | O(n)           |
| Добавление в конец (динамический)  | Амортизированное O(1) |
| Ресайзинг (расширение массива)     | O(n)           |

## 4. Алгоритмы, работающие с массивами
### 4.1 Поиск
- **Линейный поиск**: O(n) — простой перебор элементов.
- **Бинарный поиск**: O(log n) — для отсортированных массивов.

### 4.2 Сортировки
- **Пузырьковая сортировка**: O(n²)
- **Сортировка вставками**: O(n²)
- **Сортировка выбором**: O(n²)
- **Быстрая сортировка**: O(n log n) в среднем, O(n²) в худшем.
- **Сортировка слиянием**: O(n log n)
- **Сортировка кучей**: O(n log n)

### 4.3 Другие алгоритмы
- **Алгоритм Кадане** (поиск максимальной суммы подмассива): O(n).
- **Нахождение двух элементов с заданной суммой** (с использованием хеш-таблицы): O(n).

## 5. Псевдокод основных операций
```text
Функция append(value):
    если size == capacity:
        resize(capacity * 2)
    data[size] = value
    size = size + 1

Функция get(index):
    если index < 0 или index >= size:
        ошибка "Index out of bounds"
    вернуть data[index]

Функция insert(index, value):
    если index < 0 или index > size:
        ошибка "Index out of bounds"
    если size == capacity:
        resize(capacity * 2)
    сдвинуть элементы с index..size-1 вправо на 1 позицию
    data[index] = value
    size = size + 1

Функция delete(index):
    если index < 0 или index >= size:
        ошибка "Index out of bounds"
    сдвинуть элементы с index+1..size-1 влево на 1 позицию
    size = size - 1
    если size <= capacity / 4:
        resize(capacity / 2)
```

## 6. Реализация на Python
```python
class DynamicArray:
    """
    Динамический массив: поддерживает автоматическое расширение и сжатие.

    Атрибуты:
        _capacity (int): текущая ёмкость внутреннего буфера.
        _size (int): количество реальных элементов в массиве.
        _data (list): список для хранения элементов.
    """
    def __init__(self):
        self._capacity = 1
        self._size = 0
        self._data = [None] * self._capacity

    def __len__(self):
        """Возвращает число элементов"""
        return self._size

    def get(self, index):
        """Получить элемент по индексу"""
        if index < 0 or index >= self._size:
            raise IndexError("Index out of bounds")
        return self._data[index]

    def set(self, index, value):
        """Установить значение в элемент"""
        if index < 0 or index >= self._size:
            raise IndexError("Index out of bounds")
        self._data[index] = value

    def append(self, value):
        """Добавить элемент в конец массива"""
        if self._size == self._capacity:
            self._resize(self._capacity * 2)
        self._data[self._size] = value
        self._size += 1

    def insert(self, index, value):
        """Вставить элемент по индексу"""
        if index < 0 or index > self._size:
            raise IndexError("Index out of bounds")
        if self._size == self._capacity:
            self._resize(self._capacity * 2)
        # сдвиг элементов
        for i in range(self._size, index, -1):
            self._data[i] = self._data[i - 1]
        self._data[index] = value
        self._size += 1

    def delete(self, index):
        """Удалить элемент по индексу"""
        if index < 0 or index >= self._size:
            raise IndexError("Index out of bounds")
        # сдвиг элементов
        for i in range(index, self._size - 1):
            self._data[i] = self._data[i + 1]
        self._size -= 1
        self._data[self._size] = None
        if 0 < self._size <= self._capacity // 4:
            self._resize(self._capacity // 2)

    def _resize(self, new_capacity):
        """Изменить ёмкость массива"""
        new_data = [None] * new_capacity
        for i in range(self._size):
            new_data[i] = self._data[i]
        self._data = new_data
        self._capacity = new_capacity
```

