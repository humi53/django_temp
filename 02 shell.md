## Shell
- IPython : https://ipython.org
- Jupyter Notebook with Python Kernel
	- 웹상에서 쓸수 있음 : IPython 포함
- BPython
- `python manage.py shell`
	- 우선순위 : IPython > BPython > 기본쉘
	- 옵션 `-i(--interface)`	: 인터프리터 인터페이스 커스텀 지정
	- `-c(--command)` : 실행할 파이썬 코드를 문자열로 지정
### 설치 anaconda 기준
- `conda install jupyter` : ipython도 되는데 jupyter에 포함되있어서 이걸로.
- `jupyter notebook` : 주피터노트북 서버실행
- 안되면 : `conda install -c conda-forge jupyter --debug`
- 그래도 안되면 : (가상환경)>`pip install jupyter`
	- 패키지 충돌 문제를 피할수 있지만, 일부라이브러리들이 제대로 설치되지 않거나 충돌가능성이 존재함.
#### jupyter에서 장고오류
- SynchronousOnlyOperation                  
- 장고3.0부터 비동기가 지원되어서 그런것.
	- https://docs.djangoproject.com/en/3.0/topics/async/
	- `os.environ["DJANGO_ALLOW_ASYNC_UNSAFE"] = "true"` 실행