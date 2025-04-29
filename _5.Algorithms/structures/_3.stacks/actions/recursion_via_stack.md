**Эмуляция рекурсии через стек**

## 1. Описание
Преобразует рекурсивный алгоритм в итеративный, используя стек для хранения состояния (параметров и локальных переменных) вместо системного стека вызовов.

## 2. Сложность
Зависит от исходного рекурсивного алгоритма, обычно **время** и **память** остаются теми же, что и при рекурсии, но контролируются явно.

## 3. Псевдокод
Пример: вычисление факториала n!
```text
Функция factorial(n):
    стек = пустой стек
    result = 1
    push (frame с n)
    пока стек не пуст:
        frame = стек.pop()
        если frame.n == 0 или frame.n == 1:
            result *= 1
        иначе:
            result *= frame.n
            push(frame.n - 1)
    вернуть result
```

## 4. Реализация на Python
```python
from typing import List, Dict, Any

def factorial_iter(n: int) -> int:
    """Итеративный расчёт факториала через явный стек."""
    stack: List[int] = [n]
    result = 1
    while stack:
        current = stack.pop()
        if current > 1:
            result *= current
            stack.append(current - 1)
    return result
```

