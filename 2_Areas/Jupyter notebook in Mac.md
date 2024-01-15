#jupyter #jupyternotebook #brew #homebrew #mac

맥북 (Mac Book) 에서 brew를 통해 Jupyter notebook 설치 및 실행하면 에러를 발생시키거나, 별 짓을 다 해서 잘 돌아가도 찝찝한 익셉션을 발생시킨다... 대충 아래와 같다.

```sh
[W 2024-01-12 14:18:27.177 JupyterNotebookApp] Missing or misshapen translation settings schema:

    HTTP 404: Not Found (Schema not found: /opt/homebrew/Cellar/python@3.11/3.11.6_1/Frameworks/Python.framework/Versions/3.11/share/jupyter/lab/schemas/@jupyterlab/translation-extension/plugin.json)

[W 2024-01-12 14:18:27.178 JupyterNotebookApp] Settings directory does not exist at /opt/homebrew/Cellar/python@3.11/3.11.6_1/Frameworks/Python.framework/Versions/3.11/share/jupyter/lab/schemas
...
```

물론 훨씬 길지만 적당히 넣었다. 구글링 해보니 아래와 같은 variable들과 함께 jupyter lab을 실행시켜 사용하기를 권장하더라. brew를 통해 jupyter notebook을 설치했을 때 나오는 에러이기 때문에 큰 문제는 아니고, 그냥 아래와 같이 사용하자.

#### Solution is below!!

```sh
JUPYTER_PATH=/opt/homebrew/share/jupyter JUPYTER_CONFIG_PATH=/opt/homebrew/etc/jupyter jupyter lab
```

