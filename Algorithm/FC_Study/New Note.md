# Queue

- 기본큐 [FIFO]
```python
import queue

data_queue = queue.Queue()

data_queue.put("첫번째 삽입")
data_queue.put("두번째 삽입")

data_queue.get()
data_queue.get()
```
- Lifo큐 [LIFO]
- Priority큐 [우선순위]

