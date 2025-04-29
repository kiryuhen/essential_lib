**Бинарный поиск**

## 1. Описание
Алгоритм эффективного поиска элемента в отсортированном массиве путём сравнения с центральным элементом и рекурсивного/итеративного сужения границ.

## 2. Сложность
- **Время:** O(log n)
- **Память:** O(1) для итеративного варианта, O(log n) для рекурсивного (стек вызовов).

## 3. Требования
- Массив должен быть отсортирован по возрастанию или убыванию.

## 4. Псевдокод
```text
Функция binary_search(array, target):
    low = 0
    high = length(array) - 1
    пока low <= high:
        mid = (low + high) // 2
        если array[mid] == target:
            вернуть mid
        иначе если array[mid] < target:
            low = mid + 1
        иначе:
            high = mid - 1
    вернуть -1  // элемент не найден
```

## 5. Реализация на Python
```python
from typing import List, Any

def binary_search(arr: List[Any], target: Any) -> int:
    """
    Итеративный бинарный поиск.
    Возвращает индекс target или -1, если не найден.
    """
    low, high = 0, len(arr) - 1
    while low <= high:
        mid = (low + high) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            low = mid + 1
        else:
            high = mid - 1
    return -1
```

