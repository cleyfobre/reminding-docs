#python #windows #service #windowsserivice

참고 블로그: https://bitcoder.tistory.com/m/116

- 필수 라이브러리 설치
```
pip install pywin32
pip install requests
```

- 샘플 코드
```python
import win32serviceutil
import servicemanager
import ctypes
import sys
import time

OutputDebugString = ctypes.windll.kernel32.OutputDebugStringW

class MyServiceFramework(win32serviceutil.ServiceFramework):
  _svc_name_ = 'MyPythonService'
  _svc_display_name_ = 'My Python Service'
  is_running = False

  def SvcStop(self):
    OutputDebugString("MyServiceFramework __SvcStop__")
    self.is_running = False

  def SvcDoRun(self):
    self.is_running = True
    while self.is_running:
      OutputDebugString("MyServiceFramework __loop__")
      time.sleep(1)

if '__main__' == __name__:
  win32serviceutil.HandleCommandLine(MyServiceFramework)
```

