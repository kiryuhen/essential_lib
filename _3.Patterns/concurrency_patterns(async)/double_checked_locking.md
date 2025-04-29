
<!-- ==================== File: double-checked-locking.md ==================== -->
# Шаблон Double-Checked Locking (Двойная проверка блокировки)

## Описание
Double-Checked Locking — паттерн для ленивой инициализации ресурсов с минимизацией накладных расходов на синхронизацию. Проверка состояния происходит дважды: без блокировки и внутри её.

## Проблема
Без синхронизации несколько потоков могут одновременно обнаружить неинициализированный ресурс и выполнить его создание повторно, что нарушает инварианты Singleton-а и портит производительность.

## Решение
1. Сначала проверить, создан ли объект (быстрый путь).  
2. Если нет — захватить `Lock` и внутри критической секции повторно проверить создан ли объект.  
3. Если ресурс всё ещё не создан — инициализировать.

## Структура
1. `instance` — статическое поле класса, изначально `None`.  
2. `lock` — статическая блокировка.  
3. Метод `get_instance()` с двойной проверкой.

## Пример на Python
```python
import threading

class Singleton:
    _instance = None
    _lock = threading.Lock()

    @classmethod
    def get_instance(cls):
        # Первая проверка без блокировки
        if cls._instance is None:
            with cls._lock:
                # Вторая проверка внутри блокировки
                if cls._instance is None:
                    cls._instance = cls()
        return cls._instance

# Использование
def worker():
    obj = Singleton.get_instance()
    print(obj)

threads = [threading.Thread(target=worker) for _ in range(3)]
for t in threads: t.start()
for t in threads: t.join()
```

## Применение
- Ленивое создание Singleton-а.  
- Инициализация тяжёлых ресурсов (например, подключение к БД).

## Плюсы
1. Минимизирует синхронизационные затраты при повторных вызовах.  
2. Гарантирует единственный экземпляр.

## Минусы
1. Сложно правильно реализовать в языках без надёжной модели памяти.  
2. Код громоздкий, трудно отлаживать.

## Когда использовать
- Когда создание ресурса дорогое, и нужно обеспечить ленивую инициализацию с минимальными задержками.

<!-- ==================== File: barrier.md ==================== -->
# Шаблон Barrier (Барьер)

## Описание
Barrier — синхронизирующий примитив, который заставляет группу потоков ждать друг друга на заранее определённой точке.

## Проблема
Необходимо гарантировать, что все участники завершили текущую фазу перед переходом к следующей.

## Решение
1. Инициализировать `Barrier` с количеством участников.  
2. Каждый поток выполняет работу фазы и вызывает `barrier.wait()`.  
3. После сбора всех участников барьер отпускает их и можно выполнять последующие действия.

## Структура
1. `count` — число потоков-участников.  
2. `barrier` — объект `threading.Barrier(count)`.  
3. Метод `wait()` для синхронизации.

## Пример на Python
```python
import threading
import time

barrier = threading.Barrier(3)

def worker(n):
    print(f"Worker {n} фаза 1")
    time.sleep(n)
    barrier.wait()
    print(f"Worker {n} фаза 2")

threads = [threading.Thread(target=worker, args=(i,)) for i in range(1, 4)]
for t in threads: t.start()
for t in threads: t.join()
```

## Применение
- Пошаговая обработка данных конвейером.  
- Координация фаз моделирования.

## Плюсы
1. Простой API.  
2. Чёткая синхронизация фаз.

## Минусы
1. Если один поток зависнет, все блокируются.  
2. Нельзя менять число участников динамически.

## Когда использовать
- Когда важно, чтобы все потоки завершили текущий этап перед началом следующего.

<!-- ==================== File: reactor.md ==================== -->
# Шаблон Reactor (Реактор)

## Описание
Reactor — паттерн для неблокирующей обработки I/O: один поток демультиплексирует события и диспатчит их в регистрируемые обработчики.

## Проблема
Модель «поток на соединение» создаёт много потоков и переключений контекста; блокирующий I/O тормозит выполнение.

## Решение
1. Использовать селектор (`select`/`poll`/`epoll`).  
2. Зарегистрировать интересующие дескрипторы с callback-функциями.  
3. В цикле `selector.select()` получать готовые события и вызывать соответствующие обработчики.

## Структура
1. `selector` — объект `selectors.DefaultSelector()`.  
2. `handlers` — словарь дескриптор → функция.  
3. Главный цикл: `while True: events = selector.select(); for key, _ in events: handlers[key.fd]()`.

## Пример на Python
```python
import selectors
import socket

sel = selectors.DefaultSelector()

# Регистрация слушающего сокета
server = socket.socket()
server.bind(('localhost', 9000))
server.listen()
server.setblocking(False)
sel.register(server, selectors.EVENT_READ, data=None)


def accept(sock):
    conn, _ = sock.accept()
    conn.setblocking(False)
    sel.register(conn, selectors.EVENT_READ, data=echo)

def echo(conn):
    data = conn.recv(1024)
    if data:
        conn.sendall(data)
    else:
        sel.unregister(conn)
        conn.close()

while True:
    events = sel.select()
    for key, mask in events:
        callback = key.data or accept
        callback(key.fileobj)
```

## Применение
- Серверы реального времени, прокси, web-сокеты.

## Плюсы
1. Высокая производительность на числе коротких соединений.  
2. Низкие накладные расходы на потоки.

## Минусы
1. Асинхронная архитектура сложнее для отладки.  
2. Не подходит для тяжёлых вычислений внутри колбэков.

## Когда использовать
- I/O-bound сервисы с большим количеством соединений.

<!-- ==================== File: scheduler-pattern.md ==================== -->
# Шаблон Scheduler Pattern (Шаблон планировщика)

## Описание
Scheduler Pattern управляет очередью и порядком выполнения задач по расписанию или приоритету.

## Проблема
Нужно контролировать, когда и в каком порядке выполняются независимые задачи с учётом зависимостей и времени.

## Решение
1. Использовать очередь задач с приоритетами.  
2. Компонент `Scheduler` планирует запуск через таймер или события.  
3. Воркеры или пул потоков исполняют задачи.

## Структура
1. `tasks` — очередь (`queue.PriorityQueue`).  
2. `scheduler` — объект `sched.scheduler(timefunc, delayfunc)`.  
3. Воркеры для выполнения задач.

## Пример на Python
```python
import sched
import time
import threading

scheduler = sched.scheduler(time.time, time.sleep)

def task(name):
    print(f"Задача {name} выполнена в {time.time()}")

# Планируем задачи
scheduler.enter(delay=2, priority=1, action=task, argument=('A',))
scheduler.enter(delay=1, priority=1, action=task, argument=('B',))

# Запускаем в отдельном потоке
threading.Thread(target=scheduler.run).start()
```

## Применение
- Планировщики задач, cron-подобные системы, отложенные вычисления.

## Плюсы
1. Гибкость в расписании и приоритетах.  
2. Поддержка отложенных и периодических задач.

## Минусы
1. Сложнее, чем простой `ThreadPoolExecutor`.  
2. Возможные ошибки при синхронизации доступа к очереди.

## Когда использовать
- Когда важны точные временные ограничения и упорядоченность выполнения.

<!-- ==================== File: active-object.md ==================== -->
# Шаблон Active Object (Активный объект)

## Описание
Active Object разделяет вызов метода и его выполнение: клиент помещает запрос в очередь, исполнитель в другом потоке обрабатывает его.

## Проблема
Синхронные вызовы блокируют клиентов; ручные блокировки усложняют код.

## Решение
1. Проксирующий объект агрегирует запросы в `Queue`.  
2. Внутренний поток или пул потребляет очередь и выполняет реальные методы.  
3. Клиент получает `Future` для получения результата.

## Структура
1. `queue` — `queue.Queue()` для запросов.  
2. `worker` — поток, обрабатывающий запросы.  
3. `Future` для ответа клиенту.

## Пример на Python
```python
import queue
import threading
from concurrent.futures import Future

class ActiveObject:
    def __init__(self):
        self._requests = queue.Queue()
        threading.Thread(target=self._run, daemon=True).start()

    def _run(self):
        while True:
            func, args, future = self._requests.get()
            try:
                result = func(*args)
                future.set_result(result)
            except Exception as e:
                future.set_exception(e)

    def submit(self, func, *args):
        future = Future()
        self._requests.put((func, args, future))
        return future

# Использование
ao = ActiveObject()
fut = ao.submit(lambda x: x * 2, 10)
print(fut.result())  # 20
```

## Применение
- Асинхронный интерфейс к синхронным библиотекам, GUI, серверные запросы.

## Плюсы
1. Убирает явные блокировки из клиентского кода.  
2. Чёткая асинхронная семантика через `Future`.

## Минусы
1. Задержка из-за очереди.  
2. Необходимость обрабатывать ошибки и отмены.

## Когда использовать
- Когда требуется асинхронный внешний API поверх синхронного кода.

<!-- ==================== File: guarded-suspension.md ==================== -->
# Шаблон Guarded Suspension (Охраняемая приостановка)

## Описание
Guarded Suspension блокирует вызов до выполнения предусловия (guard), а потом продолжает работу.

## Проблема
Нельзя выполнять операцию, если ресурс или условие не готовы; выброс ошибки неприемлем.

## Решение
1. Проверить условие внутри `with cond:`.  
2. Если `False`, вызвать `cond.wait()`.  
3. После изменения состояния вызвать `cond.notify()`.

## Структура
1. `condition` — `threading.Condition()`.  
2. Метод `get()` с `while not ready: cond.wait()`.  
3. Метод `set()` с `cond.notify()`.

## Пример на Python
```python
import threading

class GuardedQueue:
    def __init__(self):
        self._q = []
        self._cond = threading.Condition()

    def put(self, item):
        with self._cond:
            self._q.append(item)
            self._cond.notify()

    def get(self):
        with self._cond:
            while not self._q:
                self._cond.wait()
            return self._q.pop(0)

# Использование
gq = GuardedQueue()
def producer():
    for i in range(3):
        gq.put(i)

def consumer():
    for _ in range(3):
        print(gq.get())

import threading
threading.Thread(target=consumer).start()
threading.Thread(target=producer).start()
```

## Применение
- Producer-consumer, пул ресурсов.

## Плюсы
1. Убирает активный опрос.  
2. Чистое разделение ожидания и работы.

## Минусы
1. Блокировка потоков.  
2. Риск потерянного уведомления.

## Когда использовать
- Когда нужно ждать до готовности данных без активного опроса.

<!-- ==================== File: balking.md-pattern.md ==================== -->
# Шаблон Balking Pattern (Отказ)

## Описание
Balking Pattern немедленно отказывается выполнять метод, если объект не в нужном состоянии.

## Проблема
Необходимо избежать блокировки при неготовности ресурса, при этом не ждать.

## Решение
1. В начале метода проверять `if not ready:` и сразу `return`.  
2. Приготовить ресурс и изменить флаг `ready`.

## Структура
1. Флаг `ready`.  
2. Метод `start()` с проверкой `if not ready: return`.

## Пример на Python
```python
import threading
import time

class Service:
    def __init__(self):
        self.ready = False
        threading.Thread(target=self._init).start()

    def _init(self):
        time.sleep(2)
        self.ready = True

    def start(self):
        if not self.ready:
            print("Service не готов, отказ")
            return
        print("Service запущен")

svc = Service()
svc.start()  # быстрый отказ
import time; time.sleep(2.1); svc.start()  # запустится
```

## Применение
- Инициализация ресурсов, которые не должны блокировать клиента.

## Плюсы
1. Простая логика.  
2. Никаких блокировок.

## Минусы
1. Клиенту нужно обрабатывать отказ самостоятельно.  
2. Возможен пропуск выполнения.

## Когда использовать
- Когда неблокирующее отклонение лучше, чем ожидание.

<!-- ==================== File: leader-followers.md ==================== -->
# Шаблон Leader/Followers (Лидер/Преследователи)

## Описание
Leader/Followers позволяет одному потоку быть «лидером», демультиплексировать события, а другим быть «преследователями».

## Проблема
Нельзя одновременно нескольким потокам ждать одного и того же I/O без гонки.

## Решение
1. Один поток — лидер, ждёт события на `selector`.  
2. После события лидер передаёт роль новому лидеру и сам обрабатывает.  
3. Роли циклично меняются.

## Структура
1. Пул потоков.  
2. `selector`.  
3. Координация ролей (`Lock` + флаг `is_leader`).

## Пример на Python
```python
# Упрощённая схема, реальная реализация сложнее
import selectors, threading

sel = selectors.DefaultSelector()

# Регистрируем дескрипторы...

def leader_loop():
    while True:
        events = sel.select()
        # Назначить нового лидера до обработки
        for key, _ in events:
            handle(key.fileobj)
        # Передача роли опущена

def handle(sock):
    data = sock.recv(1024)
    # обработка
```

## Применение
- Высокопроизводительные сетевые серверы.

## Плюсы
1. Эффективная демультиплексция.  
2. Нет лишних потоков под I/O.

## Минусы
1. Сложно реализовать правильно.  
2. Труднее отлаживать.

## Когда использовать
- Когда нужен очень низкий латентный I/O без генерации нитей.

<!-- ==================== File: nuclear-reaction.md ==================== -->
# Шаблон Nuclear Reaction (Ядерная реакция)

## Описание
Nuclear Reaction (Nuclear Computation) — динамическое разветвление (fission) и слияние (fusion) рабочих потоков по нагрузке.

## Проблема
Нужно гибко масштабировать параллелизм и собирать результаты по порогам.

## Решение
1. **Fission**: создаём подпотоки (`ThreadPoolExecutor.submit`).  
2. **Fusion**: ждем `Future.result()` по пороговому числу.

## Структура
1. `executor` — `ThreadPoolExecutor`.  
2. Механизм сбора результатов через `as_completed` или счётчик.

## Пример на Python
```python
from concurrent.futures import ThreadPoolExecutor, as_completed

def work(x): return x*x

with ThreadPoolExecutor(max_workers=4) as exec:
    futures = [exec.submit(work, i) for i in range(10)]
    results = []
    for fut in as_completed(futures):
        results.append(fut.result())
    print(results)
```

## Применение
- Обработка крупных данных, задач с динамическим ветвлением.

## Плюсы
1. Адаптивное масштабирование.  
2. Динамическое разветвление.

## Минусы
1. Риск переполнения пула.  
2. Сложность контроля порогов.

## Когда использовать
- Компьютинг в научных задачах, мультимедиа, аналитика с переменной нагрузкой.

