#python #log #flush #realtime

실시간 데이터를 받아서 파일로 남기는 작업을 진행 중이었다. 종류는 크게 두가지였다. TPS가 3000이 넘게 들어오는 데이터가 있고, 10초 마다 들어오는 데이터가 있다. 그런데 10초 마다 들어오는 데이터는 파일 쓰기가 안되었다. 코드는 아래와 같다.

```python
with ExitStack() as stack:  
    files = [stack.enter_context(open(fname, "a")) for fname in file_paths]  
     
    while True:  
        data = None  
        try:  
            data = q.get(block=True, timeout=1)  
        except:  
            pass  
  
        if data:  
            if data[0] == "10초마다":  
                data.append(datetime.now().strftime("%H%M%S.%f")[:-3])  
                files[0].write(str(data) + "\n")
			if data[0] == "완전실시간":
				data.append(datetime.now().strftime("%H%M%S.%f")[:-3])  
                files[1].write(str(data) + "\n")
```

위의 문제는 파일을 쓰고 `flush()` 를 해주면 해결이 된다. 

```python
files[0].write(str(data) + "\n")  
files[0].flush()
```

하지만 '완전실시간'으로 들어오는 데이터는 flush를 안해도 잘 돌아간다. 그 이유가 궁금하여 열심히 ChatGPT에 물어봤고, 아래와 같은 답변을 받았다.

```
The flush() method in Python is used to clear the buffer and force write operations to complete. However, Python's file handling is usually smart enough to handle buffering in the background without explicit calls to flush().  
  
When you write to a file, Python doesn't always immediately write the data to disk. Instead, it often keeps the data in a buffer and writes it to disk later for efficiency. This is usually fine for most applications and you won't notice any difference.  
  
However, in some cases, such as when you're writing a log file and want to be able to see the entries immediately, or when you're dealing with critical data that must be saved immediately, you might want to manually call flush() to ensure the data is written to disk right away.  
  
In your case, when you're getting real-time data, it's likely that the data is being written to the file quickly enough that you don't notice any delay due to buffering. This is why it works fine without flush(). But if you were to encounter a situation where the write operations were delayed or not completed, using flush() could help ensure the data is written immediately.
```

마지막 문단에서 말한다. python이 내가 알아차리지 못할 정도로 빠르게 버퍼링 프로세스를 진행시켰다고. 일반적으로 python 프로그램에서는 flush()를 안해도 잘 된단다. 중요한 데이터거나 바로 보고 싶을 때 flush()를 수동으로 쓴다고 하는데, 실시간 데이터에서 오히려 버퍼링 프로세스가 빠르고, 10초 마다 들어오는 데이터에서는 버퍼링 프로세스가 진행 중인게 여전히 의문이긴 하다. 개운치않다. 이렇게 생각하려 한다. python에서 10초 마다 들어오는 데이터에 대헤서는 데이터가 여전히 수신 중이기에 disk에 안쓴다고 말이다. 