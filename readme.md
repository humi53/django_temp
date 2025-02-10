# 리스트
## 개발환경
1. Anaconda Python 추천.
- Pyton 3.7 : https://repo.anaconda.com/archive/Anaconda3-2019.10-Windows-x86_64.exe
- path 옵션 추가.
- 가상환경
	- 가상환경생성 : `conda create --name askcompany python=3.7`
	- 가상환경활성 : `conda activate askcompany` `:deactivate`
	- win`where python` mac`which python`
	- 파이썬확인 : `python --verison` `where python` `where pip`
	- **장고설치** : `pip install "django~=3.0.0"`
	- `django-admin --version`

2. 장고프로젝트 생성.
	- 가상환경 목록 : `conda env list`
	- 활성 : `conda activate askcompany`
	- 생성 : 상위폴더> `django-admin startproject askcompany`
	- 창띄우기 : w`start .` m`open .`
	> `python manage.py migrate`
	> `python manage.py createsuperuser`
	> `python manage.py runserver`

3. VSC 설치.
	- 확장프로그램 : 'python'
	- `view > command palette > python:select interpreter > conda..`
	- 터미널에 가상환경이름 표시하려면 : cmd로 변경 `Command Palette > Select Default Profile > Command Prompt`
4. Jupyter notebook 설치.
	- [02 shell](./02shell.md)

## 코딩 실습
1. 앱생성
	- >`python manage.py startapp blog1`
	- `urls.py` 생성 : `urlpatterns=[]`

2. sttings.py 설정
	- `sttings.INSTALLED_APPS` 등록 : 새롭게 생성, 외부 라이브러리 형태의 장고앱은 필히 등록시켜야 함. 
	```python
	INSTALLED_APPS = [
	    'django.contrib.admin',
	    'django.contrib.auth',
	    'django.contrib.contenttypes',
	    'django.contrib.sessions',
	    'django.contrib.messages',
	    'django.contrib.staticfiles',
	    'blog1',
	]
	```

3. 모델 작성
	- `blog1/models.py` 에 모델 작성
	- 마이그래이션 실행 
		- >`python manage.py makemigrations blog1`
		- >`python manage.py migrate blog1`
	- 마이그래이션 쿼리 확인
		- >`python manage.py sqlmigrate blog1 0001_initial`
	- `blog1/admin.py` 수정
	```python
	from django.contrib import admin
	from .models import Post

	admin.site.register(Post)
	```

4. 앱 띄워보기

	- `urls.py`에서 설정.
	```python
	from django.contrib import admin
	from django.urls import path, include

	urlpatterns = [
	    path('admin/', admin.site.urls),
	    path('blog1/', include('blog1.urls')),
	]
	```

	- `blog1/views.py` 작성
	``` python
	from django.shortcuts import render
	from .models import Post

	def post_list(request):
	    qs = Post.objects.all()
	    return render(request, 'blog1/post_list.html', {
	        'post_list': qs,
	    })
	```

	- `blog1/templates/blog1/post_list.html` 작성
	```html
	<h1>Post List</h1>

	{% for post in post_list %}
	    <h2>{{post.title}}</h2>
	    {{post.content}}
	{% endfor %}
	```

	- `blog1/urls.py` 편집
	```python
	from django.urls import path
	from .import views

	urlpatterns = [
	    path('', views.post_list, name='post_list'),
	]
	```
4. 간략 정리.
- app 생성
- `setting.py` `INSTALLED_APPS` 수정.(추가)
- `app(명)/urls.py` 생성.
- `urls.py` 수정.(추가)
- `models.py` 모델 작성.
- 마이그레이션
	- 생성 `python manage.py makemigrations 앱`
	- 적용 `python manage.py migrate 앱`
	- 쿼리확인 `python manage.py sqlmigrate 앱 0001_ooo`
	- DB확인 : DB 종류에 따라 각각 확인.
	- 마이그래이션 적용 확인 : `python manage.py showmigrations 앱`
- `python manage.py dbshell` : sqlite 쉘.
- 모델 admin 등록. : `blog1/admin.py` 편집
```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    pass
```
