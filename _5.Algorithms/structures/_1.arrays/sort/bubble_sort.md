**Пузырьковая сортировка**

## 1. Описание
Простейший алгоритм сортировки, многократно проходящий по массиву и «всплывающий» (меняющий местами) соседние элементы, если они стоят в неправильном порядке.

## 2. Сложность
- **Время:** O(n²)
- **Память:** O(1)

## 3. Псевдокод
```text
Функция bubble_sort(array):
    n = length(array)
    для i от 0 до n-2:
        для j от 0 до n-2-i:
            если array[j] > array[j+1]:
                обмен(array[j], array[j+1])
```

## 4. Реализация на Python
```python
from typing import List

def bubble_sort(arr: List[int]) -> None:
    """
    Сортировка списка на месте пузырьковым методом.
    """
    n = len(arr)
    for i in range(n - 1):
        for j in range(n - 1 - i):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
```

