#aws #lambda #layer #python #pymysql #geopy #googlemaps 

#### 목표
AWS에서 python3.9 가상환경의 lambda function을 만들었는데, pymysql이 설치가 안되어 있어서 이를 해결하고자 함

#### 과정
- 로컬에서 docker로 pymsql을 설치하여 zip파일로 만든다.
- 해당 zip파일을 lambda에 새로운 layer(계층)로 추가한다.
- 이미 만든 lambda function에 나의 layer를 적용시킨다.
- code에서 pymysql을 사용할 수 있다.

#### pymysql layer zip파일 만들기
1. ubuntu 컨테이너 실행
	1. docker run -it ubuntu:22:04
2. ubuntu 컨테이너 접속
3. apt update
4. apt install software-properties-common -y
5. add-apt-repository ppa:deadsnakes/ppa
6. apt install zip -y
7. apt install python3.9 -y
8. apt install python3-pip -y
9. mkdir -p layer/python/lib/python3.9/site-packages
10. python3.9 -m pip install pymysql -t layer/python/lib/python3.9/site-packages/
11. cd layer
12. zip -r mypackage.zip *
13. 컨테이너를 빠져나옴
14. 작업한 ubuntu 컨테이너 id 확인하기
	1. docker ps -a
15. 그 결과 예를 들어 abcd가 나왔다면, 아래와 같은 명령어로 zip파일을 host로 복사해온다.
```
docker cp abcd:/layer/mypackage.zip .
```
16. 이제 AWS lambda 콘솔에서 레이어 추가하면 된다.

#### ❗ googlemaps를 위한 Layer 만드는 과정에서 발생한 오류

아래의 명령어는 에러를 발생시켰다.

```sh
python3.9 -m pip install -U googlemaps -t layer/python/lib/python3.9/site-packages/
```

에러는 아래와 같다.

```sh
Traceback (most recent call last):
  File "/usr/lib/python3.9/runpy.py", line 197, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "/usr/lib/python3.9/runpy.py", line 87, in _run_code
    exec(code, run_globals)
  File "/usr/lib/python3/dist-packages/pip/__main__.py", line 29, in <module>
    from pip._internal.cli.main import main as _main
  File "/usr/lib/python3/dist-packages/pip/_internal/cli/main.py", line 9, in <module>
    from pip._internal.cli.autocompletion import autocomplete
  File "/usr/lib/python3/dist-packages/pip/_internal/cli/autocompletion.py", line 10, in <module>
    from pip._internal.cli.main_parser import create_main_parser
  File "/usr/lib/python3/dist-packages/pip/_internal/cli/main_parser.py", line 8, in <module>
    from pip._internal.cli import cmdoptions
  File "/usr/lib/python3/dist-packages/pip/_internal/cli/cmdoptions.py", line 23, in <module>
    from pip._internal.cli.parser import ConfigOptionParser
  File "/usr/lib/python3/dist-packages/pip/_internal/cli/parser.py", line 12, in <module>
    from pip._internal.configuration import Configuration, ConfigurationError
  File "/usr/lib/python3/dist-packages/pip/_internal/configuration.py", line 26, in <module>
    from pip._internal.utils.logging import getLogger
  File "/usr/lib/python3/dist-packages/pip/_internal/utils/logging.py", line 27, in <module>
    from pip._internal.utils.misc import ensure_dir
  File "/usr/lib/python3/dist-packages/pip/_internal/utils/misc.py", line 39, in <module>
    from pip._internal.locations import get_major_minor_version
  File "/usr/lib/python3/dist-packages/pip/_internal/locations/__init__.py", line 14, in <module>
    from . import _distutils, _sysconfig
  File "/usr/lib/python3/dist-packages/pip/_internal/locations/_distutils.py", line 9, in <module>
    from distutils.cmd import Command as DistutilsCommand
ModuleNotFoundError: No module named 'distutils.cmd'
```

결국에는 맨 아래 내용에 주목했다.

```sh
ModuleNotFoundError: No module named 'distutils.cmd'
```

아래와 같이 인스톨한 후, 다시 googlemaps를 설치 했고, 결과는 성공이었다.

```sh
apt install python3.7-distutils
```

#### ❗ Lambda에서 boto와 같이 import할 때 발생한 오류

위에서 성공적으로 설치를 완료하고 zip으로 압축하여 Lambda layer로 추가했으나, 실제 코드에서 boto와 함께 import할 때 에러를 발생시킨다.

```sh
cannot import name 'DEFAULT_CIPHERS' from 'urllib3.util.ssl_'
```

이는 urllib3 2.0를 지원하지 않는 botocore를 import하려고 했기 때문이다. 이는 urllib3 2.0 미만의 버전으로 고정하여 layer로 만들면 문제가 발생하지 않는다. 즉 아래와 같이 다시 인스톨한다.

```sh
python3.9 -m pip install -U googlemaps "urllib3<2" -t /layer/python/lib/python3.9/site-packages/
```

더이상 해당 문제는 발생하지 않는다.
#### postgresql은 psycopg2를 대신 설치하면 된다.