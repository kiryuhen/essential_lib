**Сортировка кучей**

## 1. Описание
Алгоритм сортировки «кучей» (heap sort) преобразует массив в бинарную кучу, затем последовательно извлекает корень (максимальный элемент) и восстанавливает кучу.

## 2. Сложность
- **Время:** O(n log n)
- **Память:** O(1)

## 3. Псевдокод
```text
Функция heapify(array, n, i):
    largest = i
    l = 2*i + 1
    r = 2*i + 2
    если l < n и array[l] > array[largest]:
        largest = l
    если r < n и array[r] > array[largest]:
        largest = r
    если largest != i:
        обмен(array[i], array[largest])
        heapify(array, n, largest)

Функция heap_sort(array):
    n = length(array)
    для i от n//2-1 до 0:
        heapify(array, n, i)
    для i от n-1 до 1:
        обмен(array[0], array[i])
        heapify(array, i, 0)
```

## 4. Реализация на Python
```python
from typing import List

def heapify(arr: List[int], n: int, i: int) -> None:
    largest = i
    l = 2 * i + 1
    r = 2 * i + 2
    if l < n and arr[l] > arr[largest]:
        largest = l
    if r < n and arr[r] > arr[largest]:
        largest = r
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)

 def heap_sort(arr: List[int]) -> None:
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)
```

