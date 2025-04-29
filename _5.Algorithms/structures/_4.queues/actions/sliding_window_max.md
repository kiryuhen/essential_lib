**Максимум в скользящем окне**

## 1. Описание
Алгоритм находит максимальные значения в каждом подмассиве длины k (скользящем окне) входного массива, используя структуру данных dequeue для амортизированного O(1) доступа.

## 2. Сложность
- **Время:** O(n)
- **Память:** O(k) — для хранения индексов в deque.

## 3. Псевдокод
```text
Функция sliding_window_max(nums, k):
    создать пустую deque D (для индексов)
    создать массив результатов res
    для i от 0 до length(nums)-1:
        если D не пуст и D.front < i-k+1:
            D.popleft()
        пока D не пуст и nums[i] >= nums[D.back]:
            D.pop()
        D.append(i)
        если i >= k-1:
            res.append(nums[D.front])
    вернуть res
```

## 4. Реализация на Python
```python
from collections import deque
from typing import List

def sliding_window_max(nums: List[int], k: int) -> List[int]:
    """
    Возвращает список максимумов в каждом окне размером k.
    """
    if not nums or k == 0:
        return []
    D = deque()
    res: List[int] = []
    for i, num in enumerate(nums):
        # удалить индексы вне текущего окна
        if D and D[0] < i - k + 1:
            D.popleft()
        # поддерживать убывающий порядок в deque
        while D and nums[D[-1]] < num:
            D.pop()
        D.append(i)
        # запись результата
        if i >= k - 1:
            res.append(nums[D[0]])
    return res
```

