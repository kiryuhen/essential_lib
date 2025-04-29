# Шаблон Nuclear Reaction (Ядерная реакция)

## Описание
Nuclear Reaction — это паттерн динамического масштабирования потоков, при котором потоки могут «расщепляться» (fission) для обработки задач и «сливаться» (fusion) после завершения, адаптируясь к текущей нагрузке.

## Проблема
Фиксированное число потоков или статический пул плохо справляются с переменной нагрузкой: при пиках нагрузки недостаток потоков ведёт к узким местам, а при простое избыток потоков расходует ресурсы впустую.

## Решение
Nuclear Reaction вводит два механизма:
1. **Fission** — породить новые потоки по мере роста очереди задач.
2. **Fusion** — объединить или завершить избыточные потоки при снижении нагрузки.

Таким образом, пул потоков динамически расширяется и сжимается в зависимости от метрик загрузки (длина очереди, время отклика).

## Структура
1. **Thread Pool** — базовый пул исполнителей.
2. **Fission Controller** — логика создания новых потоков при превышении порога (например, длины очереди).
3. **Fusion Controller** — логика завершения или объединения потоков при снижении нагрузки.
4. **Thresholds** — параметры: максимальное/минимальное количество потоков, метрики загрузки.

## Пример на Python
```python
import threading, queue, time

class NuclearPool:
    def __init__(self, min_threads=1, max_threads=10, threshold=5):
        self.tasks = queue.Queue()
        self.min_threads = min_threads
        self.max_threads = max_threads
        self.threshold = threshold
        self.threads = []
        self._lock = threading.Lock()
        for _ in range(min_threads):
            self._start_worker()

    def _start_worker(self):
        t = threading.Thread(target=self._worker, daemon=True)
        t.start()
        self.threads.append(t)

    def submit(self, task, *args):
        self.tasks.put((task, args))
        # Fission: если очередь растёт
        if self.tasks.qsize() > self.threshold and len(self.threads) < self.max_threads:
            with self._lock:
                if len(self.threads) < self.max_threads:
                    self._start_worker()

    def _worker(self):
        while True:
            task, args = self.tasks.get()
            task(*args)
            # Fusion: если очередь пуста и потоков больше min
            if self.tasks.empty() and len(self.threads) > self.min_threads:
                with self._lock:
                    if len(self.threads) > self.min_threads:
                        self.threads.remove(threading.current_thread())
                        break

# Использование

def work(x):
    print(f"Обработка {x}")
    time.sleep(0.1)

pool = NuclearPool(min_threads=1, max_threads=4, threshold=2)
for i in range(10):
    pool.submit(work, i)

# Даем время на завершение
import time; time.sleep(2)
```

## Применение
- Параллельная обработка батчей данных
- MapReduce-подобные вычисления
- Системы с переменной нагрузкой (веб-серверы, фоновые задачи)

## Плюсы
1. Адаптивное масштабирование пула под нагрузку
2. Оптимальное использование ресурсов
3. Простая концептуальная модель «fission/fusion»

## Минусы
1. Риск неконтролируемого роста потоков без правильных порогов
2. Накладные расходы на постоянное создание и завершение потоков
3. Сложность настройки параметров порогов

## Когда использовать
- Когда нагрузка сильно колеблется во времени
- Для одноразовых или периодических батчевых задач
- При необходимости динамического управления ресурсами потока

