**Сортировка выбором**

## 1. Описание
Алгоритм, на каждом шаге выбирающий минимальный (или максимальный) элемент из неотсортированной части массива и меняющий его местами с первым элементом этой части.

## 2. Сложность
- **Время:** O(n²)
- **Память:** O(1)

## 3. Псевдокод
```text
Функция selection_sort(array):
    n = length(array)
    для i от 0 до n-2:
        min_idx = i
        для j от i+1 до n-1:
            если array[j] < array[min_idx]:
                min_idx = j
        обмен(array[i], array[min_idx])
```

## 4. Реализация на Python
```python
from typing import List

def selection_sort(arr: List[int]) -> None:
    """
    Сортировка списка на месте методом выбора.
    """
    n = len(arr)
    for i in range(n - 1):
        min_idx = i
        for j in range(i + 1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
```

