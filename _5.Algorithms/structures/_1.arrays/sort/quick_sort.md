**Быстрая сортировка**

## 1. Описание
Разделяй и властвуй: выбирается опорный элемент (pivot), массив разбивается на две части (меньше pivot, больше pivot), каждая сортируется рекурсивно.

## 2. Сложность
- **Время:** O(n log n) в среднем, O(n²) в худшем (неудачный выбор pivot).
- **Память:** O(log n) — стек рекурсии.

## 3. Псевдокод
```text
Функция quick_sort(array, low, high):
    если low < high:
        pi = partition(array, low, high)
        quick_sort(array, low, pi - 1)
        quick_sort(array, pi + 1, high)

Функция partition(array, low, high):
    pivot = array[high]
    i = low - 1
    для j от low до high - 1:
        если array[j] <= pivot:
            i = i + 1
            обмен(array[i], array[j])
    обмен(array[i+1], array[high])
    вернуть i + 1
```

## 4. Реализация на Python
```python
from typing import List

def quick_sort(arr: List[int], low: int = 0, high: int = None) -> None:
    """
    Быстрая сортировка на месте.
    """
    if high is None:
        high = len(arr) - 1
    if low < high:
        pi = partition(arr, low, high)
        quick_sort(arr, low, pi - 1)
        quick_sort(arr, pi + 1, high)

 def partition(arr: List[int], low: int, high: int) -> int:
    pivot = arr[high]
    i = low - 1
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1
```

