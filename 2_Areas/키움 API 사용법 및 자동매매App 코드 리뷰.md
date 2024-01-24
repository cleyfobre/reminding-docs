#키움 #키움API #키움openapi #주식 #주식매매 #매매 #자동매매

#### Anaconda3 설치
64bit용을 설치한다. 설치할 때 PATH 설정에 체크해준다. 설치 완료하고 아래 명령어를 친다.

```sh
conda --version
```

아래는 기본 conda 명령어 모음이다.

```sh
# 32bit로 변경하기
> set CONDA_FORCE_32BIT=1

# myenv 라는 이름의 python3.7 가상환경 만들기 (시간이 꽤 걸린다.)
> conda create -n myenv python=3.7 anaconda

# 가상환경 목록 보기
> conda env lsit

# 앞서 만든 가상환경 활성화 하기
> conda activate myenv

# 활성화하면 커맨드 라인이 아래처럼 바뀐다.
(myenv) >

# 가상환경으로 들어가서 python이라 입력을 하면 32bit로 나와야 정상이다.
(myenv) > python
Python 3.7.13 (default. Mar 23. 2019. 12:22:34) MSC v.1915 32 bit (intel) ...
```

#python #conda #가상환경 #venv #myenv 

> [!tip] 최근에 설치 명령어가 조금 바뀐 것 같다. 아래와 같이 해보자.

```sh
> conda create -n myenv
> conda activate myenv
(myenv) >
(myenv) > conda config --env --set subdir win-32
(myenv) > conda install python=3.7
(myenv) > python
Python 3.7.13 (default. Mar 23. 2019. 12:22:34) MSC v.1915 32 bit (intel) ...

# 필요한 라이브러리 미리 설치해주자
(myenv) > pip install --upgrade pip setuptools wheel sip
(myenv) > pip install pyqt5
```

> [!danger] 혹시 pyqt5 설치가 다음과 같은 에러와 실패한다면
> error: Microsoft Visual C++ 14.0 is required
>  http://visualstudio.microsoft.com/ko/visual-cpp-build-tools 에서 빌드툴을 설치해준 후,
>  다시 pyqt5를 설치해보자!

#### PyCharm에 가상환경 연동
앞서 만든 가상환경을 Pycharm 프로젝트에서 사용할 수 있도록 Settings에서 연동해줘야 한다. 가상환경은 보통 (Windows 기준) `~/anaconda3/envs/myenv/python.exe` 형태로 위치해 있다. 키움API를 사용하기 위해서는 앞서 설치한  `PyQt5` 라이브러리가 필수이다. 이는 python 에서 UI 개발에 필요한 라이브러리이나, 안에 유용한 패키지들이 많이 들어 있어서 이를 활용한다.

#### 자동매매App 핵심 사항 ✨

0. App Flow

![[Pasted image 20231215104736.png]]

1. PyQt5
application은 계속 실행되어야 한다. 아래 `exec_()` 은 해당 python 프로그램을 이벤트 루프에 진입시킨다. 이벤트 루프는 무한 루프이며 프로그램이 계속 진행되도록 한다. 또한 Kiwoom 클래스 안에 있는 QEventLoop 객체들을 처리한다.

```python
self.app = QApplication(sys.argv)
self.kiwoom = Kiwoom()
self.app.exec_()
```

2. OCX
OpenAPI는 Windows 환경에서 OCX 객체로서 설치되어 있다. 우리는 Python의 PyQt5를 가지고 OCX 객체를 제어할 것이다. 아래 처럼 `KHOPENAPI.KHOpenAPICtrl.1` 라는 명으로 OCX 객체가 레즈스트리에 저장되어 있으며, `setControl()` 을 호출해 놓으면 Python에서 OpenAPI 객체를 제어할 수 있다.

```python
class Kiwoom(QAxWidget):
	def __init__(self):
		super().__init__()
		self.setControl("KHOPENAPI.KHOpenAPICtrl.1")
```

3. 키움 OpenAPI 기본 사용법🔥
키움 OpenAPI에는 TR데이터, 실시간 데이터가 있다. 그 둘의 차이는 다음과 같다. 
- TR 데이터: 본인이 요청해서 받음
	- 보통 `opw20015` 형식으로 생겼다. TR 데이터 요청하기 위해 `CommRqData` 나 `GetCommData`와 같은  함수를 사용할 때 파라미터로 들어간다. 보통 사용자 지정의 문자열(RqName)과 함께 쓰인다.
- 실시간 데이터: 키움이 실시간으로 보냄

KOA Studio에 '개발가이드'를 보면 아래와 같이 흰색 표시의 함수와 노란색 표시의 함수가 있다.

![[Pasted image 20231213155832.png]]

==PyQt5에서는 노란색의 함수들을 QAxWidget을 통해 제공한다. 해당 함수들은 콜백함수를 인자로 받아 선언되고, 흰색 함수들이 호출되었을 때 그 응답 결과가 콜백함수로 전달된다. 이러한 일련의 과정들은 QEventLoop와의 결합이 필연적이다. 순서가 반드시 보장되어야 하는 로직이 넣기 위함이다. 글만 보면 무슨 말인지 모르겠으니 아래 예시를 보자. 이 문단과 아래 코드를 이해하는 것은 매우 매우 중요하다.==

```python
class Kiwoom(QAxWidget):
	def __init__(self):
		super().__init__()
		self.setControl("KHOPENAPI.KHOpenAPICtrl.1")

		self.login_event_loop = QEventLoop()
		
		self.OnEventConnect.connect(self.login_handler) 🛎️

		# login()에서 시작된 event loop가 끝나기 전까진 my_account_info()는 실행되지 않는다.
		self.login()
		self.my_account_info()

	def login(self):
		self.dynamicCall("commConnect()") 🧨
		self.login_event_loop.exec_()

	def login_handler(self, error_code): 
		if error_code == 0:
			# something...
		else:
			print("login failed")
		self.login_event_loop.exit()

	def my_account_info(self):
		# something...
```

TR 데이터를 얻으려면, 키움 API에서 정의한 TR 코드를 가지고 요청한다. 더불어 사용자가 정의한 명칭인 RqName과 함께 요청한다. 예제는 아래와 같다.

```python
class Kiwoom(QAxWidget):
	def __init__(self):
		super().__init__()
		self.setControl("KHOPENAPI.KHOpenAPICtrl.1")

		self.kiwoon_event_loop = QEventLoop()
		self.login_event_loop = QEventLoop()
		
		self.OnEventConnect.connect(self.login_handler)
		self.OnReceiveTrData.connect(self._trdata_handler) 🛎️

		self.login()
		self.my_account_info()

	def login(self):
		# something...

	def login_handler(self, error_code):
		# something...
		
	def my_account_info(self):
		self.dynamicCall("SetInputValue(QString, QString)", "계좌번호", "1234123411")
		self.dynamicCall("SetInputValue(QString, QString)", "비밀번호", "")
		self.dynamicCall("SetInputValue(QString, QString)", "비밀번호입력매체구분", "00")
		self.dynamicCall("SetInputValue(QString, QString)", "조회구분", "2")
		self.dynamicCall("CommRqData(QString, QString, QString, QString)",
                 "예수금상세현황요청", "opw00001", "0", 2000) 🧨
		self.kiwoon_event_loop.exec_()

	def _trdata_handler(self, scr_no, rq_name, tr_code, record_name, prev_next):
		if rq_name == "예수금상세현황요청":
			balance = int(self.dynamicCall("GetCommData(QString, QString, int, QString)", 
				tr_code, rq_name, 0, "예수금"))
			print(balance)
		self.kiwoon_event_loop.exit()
```

위 예제는 예수금 TR 데이터를 가져오는 코드이다. 예수금 정보의 TR 코드는 `opw00001` 이다. 해당 코드를 KOA Studio에서 검색해서 보면 Input과 Output에 어떤 컬럼이 들어가는지 알 수 있다. 아래 이미지를 확인해보자. 

![[Pasted image 20231214163322.png]]

위 이미지를 보면 예수금상세현황요청 `opw00001` TR 데이터 조회는 `SetInputValue` 와 `CommRqData` 를 조합하여 가져오도록 되어 있다. TR 코드에 맞는 Input 값을 넣어 위 코드와 같이 넣어 준다. 예제에서는 계좌번호, 비밀번호, 비밀번호입력매체구분, 조회구분 총 4가지이다. 그리고 handler에서는 `OnReceiveTrData` 에서 정의된 것과 똑같이 파라미터를 넣어주면, 그 순서대로 데이터를 받을 수 있다. 아래 이미지를 참고하자.

![[Pasted image 20231214172856.png]]

위 예제에서는 scr_no, rq_name, tr_code, record_name, prev_next 이 그것에 해당된다. 그리고 handler 안에서 TR 코드의 Output에 해당되는 컬럼을 가져올 수 있도록 했다. 첫번째 이미지를 보면 Output에 '예수금'을 볼 수 있다. 그것을 조회하기 위해 예제에서는 `GetCommData`를 가지고 예수금을 조회했다.

4. Screen
관련 설명 영상 참고하기. 프로그램 동산의 자동매매 구축 강의 영상 40강(5:09 부터) https://www.youtube.com/watch?v=L58xxJu0_jE&list=PLDtzZPtOGenaSknTbsb6x6L39V0VPz_rS&index=40 

스크린 번호는 일종의 바구니다. 요청들을 담는 바구니인데, 스크린 번호는 최대 200개까지 만들 수 있고 SetRealReg() 의 경우 최대 100개의 종목을 실시간 시세데이터로 등록할 수 있다. 즉 내가 실시간 데이터를 받고자 등록할 수 있는 수는 다음과 같다. 

`스크린 번호 갯수(200개) X 스크린 당 실시간 종목으로 등록할 수 있는 갯수(100개) = 20,000개`

> [!tip] 물론 조건 검색할 때 1초 당 받을 수 있는 건 수는 제한이 있다.

#### condition_search() 코드 flow
_condition_search()_ 의 코드 이해를 위한 정리이다. Tree 형태의 정리가 복잡할 수 있으므로 이모티콘과 함께 정리하도록 한다. 

1. 나의 검색기 정보를 요청한다.

```python
def condition_search():
	self.dynamicCall("GetConditionLoad()") 🧨
```

2. 나의 검색기를 가져와 검색을 시작한다.

```python
def __init__(self):
	self.OnReceiveConditionVer.connect(self._condition_ver_handler) 🛎️

def _condition_ver_handler(self):
	cond_list = self.dynamicCall("GetConditionNameList()").split(";")
	cond_search_success = self.dynamicCall(
		"SendCondition(QString, QString, int, int)", 
		"0156", cond_name, cond_idx, 1) 🧨
```

3. 검색기가 추천해준 종목을 확인한다.

```python
def __init__(self):
	self.OnReceiveTrCondition.connect(self._condition_search_handler) 🛎️
	self.OnReceiveRealCondition.connect(self._realtime_condition_search_handler) 🛎️

def _realtime_condition_search_handler(self, code, event, cond_name, cond_idx):
	self.dynamicCall("SetRealReg(QString, QString, QString, QString)",
		2000, code, 20, "1") 🧨
```

4. 종목의 실시간 데이터를 받아서 매매한다.

```python
def __init__(self):
	self.OnReceiveRealData.connect(self._real_data_handler) 🛎️

def _real_data_handler(self, s_code, real_type, real_data):
	self.dynamicCall("SendOrder(QString, QString, QString, int, QString, int, int, QString, QString)",
		["신규매도", self.candidates_dict[s_code]["주문용화면번호"], self.account_num, 2,
		s_code, quantity, 0, "03", ""]) 🧨
	self.dynamicCall("SendOrder(QString, QString, QString, int, QString, int, int, QString, QString)",
		["신규매수", self.candidates_dict[s_code]["주문용화면번호"], self.account_num, 1,
		s_code, quantity, 0, "00", ""]) 🧨
```

5. 매매 결과를 확인하고, 필요없는 실시간 연결을 끊는다.

```python
def __init__(self):
	self.OnReceiveChejanData.connect(self._chejan_handler) 🛎️

def _chejan_handler(self, sGubun, nItemCnt, sFidList):
	self.dynamicCall("SetRealRemove(QString, QString)", 
		self.candidates_dict[s_code]['화면번호'], s_code)
```

