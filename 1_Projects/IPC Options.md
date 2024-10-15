#ipc #python #cqrs

> [!note]
> 해당 문서는 IPC의 다양한 옵션들을 설명한 문서이다. 나는 Reader와 Saver라는 두 프로그램이 있다고 가정했고, Reader는 외부 데이터를 받아서 Saver에게 전달하고, 데이터를 전달받은 Saver는 DB에 데이터를 저장하는 프로그램이다. 기본적인 IPC 구조에 대한 가이드는 아래 문서를 참고하면 된다. 그리고 해당 프로그램을 간단하게 구현하고 나서 테스트를 하고 싶다면 아래 문서를 참고하면 된다. [[제한된 리소스 내 부하테스트]]

There are several options for Inter-Process Communication (IPC) in Python, depending on your needs for speed, complexity, and scalability. Here are some popular IPC mechanisms:

### 1. **Multiprocessing Queue (Built-in IPC)**
   Python’s `multiprocessing.Queue` can be used to transfer data between processes. This is an easy way to switch from threading to multiprocessing.

   ```python
   from multiprocessing import Process, Queue

   def reader(queue):
       # Read from API and put data in the queue
       queue.put(data_from_api)

   def saver(queue):
       # Get data from the queue, process and save to DB
       data = queue.get()
       # Process and save

   if __name__ == "__main__":
       q = Queue()
       p1 = Process(target=reader, args=(q,))
       p2 = Process(target=saver, args=(q,))
       p1.start()
       p2.start()
       p1.join()
       p2.join()
   ```

### 2. **Pipes (Multiprocessing)**
   You can also use `Pipe` from the `multiprocessing` module for simple communication between two processes.

   ```python
   from multiprocessing import Process, Pipe

   def reader(conn):
       # Send data through the pipe
       conn.send(data_from_api)
       conn.close()

   def saver(conn):
       # Receive data from the pipe
       data = conn.recv()
       # Process and save

   if __name__ == "__main__":
       parent_conn, child_conn = Pipe()
       p1 = Process(target=reader, args=(child_conn,))
       p2 = Process(target=saver, args=(parent_conn,))
       p1.start()
       p2.start()
       p1.join()
       p2.join()
   ```

### 3. **Shared Memory (Multiprocessing)**
   For performance, if you need to share large amounts of data efficiently, you can use `SharedMemory` from the `multiprocessing.shared_memory` module.

   ```python
   from multiprocessing import shared_memory
   import numpy as np

   # Create shared memory
   shm = shared_memory.SharedMemory(create=True, size=10)

   # Attach to shared memory from different processes and use it
   ```

> [!warning] Detail of Shared Memory
> 자세한 내용은 다음 문서를 참고한다. [[Shared Memory 디테일]]

### 4. **Sockets**
   You can set up a socket server and client for communication between processes, especially if they are running on different machines. Sockets are powerful for distributed systems but introduce some complexity.

   - **Server (Reader):**

     ```python
     import socket

     def reader():
         s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
         s.bind(("localhost", 12345))
         s.listen(5)
         while True:
             client, addr = s.accept()
             data = "data_from_api"
             client.send(data.encode())
             client.close()

     reader()
     ```

   - **Client (Saver):**

     ```python
     import socket

     def saver():
         s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
         s.connect(("localhost", 12345))
         data = s.recv(1024).decode()
         # Process and save
         s.close()

     saver()
     ```

### 5. **ZeroMQ (ZMQ)**
   ZeroMQ is a high-performance asynchronous messaging library that is excellent for building complex distributed systems.

   - **Reader (Server):**

     ```python
     import zmq

     context = zmq.Context()
     socket = context.socket(zmq.PUSH)
     socket.bind("tcp://*:5555")

     while True:
         socket.send_string("data_from_api")
     ```

   - **Saver (Client):**

     ```python
     import zmq

     context = zmq.Context()
     socket = context.socket(zmq.PULL)
     socket.connect("tcp://localhost:5555")

     while True:
         data = socket.recv_string()
         # Process and save
     ```

### 6. **Redis (Pub/Sub)**
   If you need persistence or distributed IPC, Redis offers Pub/Sub functionality. Redis can be used when processes might run on different machines or when you need to decouple them further.

   - **Reader:**
     ```python
     import redis

     r = redis.StrictRedis(host='localhost', port=6379)
     r.publish('channel', 'data_from_api')
     ```

   - **Saver:**
     ```python
     import redis

     r = redis.StrictRedis(host='localhost', port=6379)
     pubsub = r.pubsub()
     pubsub.subscribe('channel')

     for message in pubsub.listen():
         if message['type'] == 'message':
             data = message['data']
             # Process and save
     ```

### 7. **Message Queues (RabbitMQ, Kafka)**
   For highly scalable systems, message queues like RabbitMQ or Kafka offer robust message delivery guarantees and can handle large volumes of data.
   - Use RabbitMQ with `pika` or Kafka with `kafka-python`.

   **Example for RabbitMQ (Reader with pika):**
   ```python
   import pika

   connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
   channel = connection.channel()
   channel.queue_declare(queue='data_queue')

   channel.basic_publish(exchange='', routing_key='data_queue', body='data_from_api')
   connection.close()
   ```

   **Saver:**
   ```python
   import pika

   connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
   channel = connection.channel()
   channel.queue_declare(queue='data_queue')

   def callback(ch, method, properties, body):
       # Process and save
       print(f"Received {body}")

   channel.basic_consume(queue='data_queue', on_message_callback=callback, auto_ack=True)
   channel.start_consuming()
   ```

---

Choose based on your complexity needs, deployment (local/distributed), and whether you prefer built-in solutions or third-party libraries like Redis, ZeroMQ, or RabbitMQ.