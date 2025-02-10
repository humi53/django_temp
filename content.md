# 내용
### 장고 주요 기능들
- Function Based Views : 함수로 http 요청 처리
- Models : 데이터베이스와의 인터페이스
- Templates : HTML 문자열 조합, 푸쉬 메시지나 이메일 내용을 만들때도 쓰면 편리.
- Admin 기초 : 심플 데이터베이스 레코드 관리 UI
- Logging : 메시지 로깅
- Static files : 개발 목적, 정적 파일
- Messages framework : 1회성 메시지 노출 목적
### a
- Class Based Views : 클래스로 함수 기반 뷰 만들기
- Forms : 입력폼, 입력값 유효성 검사 DB로의 저장까지.
- 테스팅
- 국제화 & 지역화
- 캐싱
- Geographic : DB의 Geo 기능 활용 (PostgreSQL 중심)
- Sending Emails
- Syndication Feeds (RSS/Atom)
- Sitemaps

### 장고 기본 앱
- `admin` `auth` `messages` `sessions` `staticfiles`
- `admindocs` `contenttypes` `flatpages` `gis` `gumanize` `postgres` `redirects` `sitemaps` `sites` `sydication`

### 장고 앱
- 하나의 앱은 유일 해야함
- 앱을 하나의 작은 서비스로 봐도 무관

## ORM
- RDB만 제공
- MSSQL : `django-pyodbc-azure` 라이브러리로 가능.
- `django-debug-toolbar` 도구로 SQL 컨트롤 가능해야함.
- 장고 강점 : Model/Form
- 장고 shell : >`python manage.py shell`
	```cmd
	>>> from django.db import connection
	>>> cursor = connection.cursor()
	>>> cursor.close()
	>>> exit()
	```

### 설계개념
- DB table 과 python class 1:1 매핑
- 모델 클래스명은 단수형으로 지정. (클래스이기 때문. : PascalCase 네이밍)
- 모델클래스는 DB 테이블 필드 내역이 일치 해야함.
- app폴더(앱명) 안의 `models.py`에 정의
	- `models.Model`을 상속받아 새로운 모델을 만든다.
	- `class Post(models.Model):`

### 모델활용
#### 장고 모델을 통한 형상 관리.
1. 모델 클래스 작성
2. 모델 클래스로부터 마이그레이션 파일 생성 -> `makemigrations` 명령
3. 마이그레이션 파일을 DB에 적용 -> `migrate` 명령
4. 모델활용
#### 외부 에서 형상 관리.
- DB로부터 모델 클래스 소스 생성 -> `inspectdb` 명령
- 모델활용

### 모델 & DB 네이밍
- blog앱 `Post` -> `blog_post`, `Comment` -> `blog_comment`
- shop앱 `Item` -> `shop_item`, `Review` -> `shop_review`
- 커스텀지정 : 모델 Meta 클래스의 db_table 속성. : 마이그래이션 하기전에.

## 장고 모델필드
### 기본 모델필드 타입
- Primary Key : `AutoField`, BigAutoField
- 문자열 : `CharField`, `TextField`, SlugField
- 날짜/시간 : DataField, TimeField, `DateTimeField`, DurationField
- 참/거짓: BooleanField, NullBooleanField
- 숫자 : IntegerField, SmallItegerField, PositiveIntegerField, PositiveSmallIntegerField, BigIntegerField, DecimalField, FloatField
- 파일 : BinaryField, FiledField, `ImageField`, FilePathField
- 이메일 : EmailField
- URL : URLField
- UUID : UUIDField
- 아이피 : GenericlPAddressField
- Relationshop Types
	- ForeignKey, ManyToManyField, OneToOneField
- 다양한 커스텀 필드.
	- django-model-utils : https://django-model-utils.readthedocs.io/en/latest/
### 모델필드는 DB필드타입을 반영
- 각 타입의 차이 : 디폴트로 적용된 유혀성 검사 등의 차이. (형식, 길이 등)
- 같은 모델 필드라도 DB에 따라 다른 타입이 될 수도 있다.
- 작업 후 꼭 DB를 확인하는 습관이 필요.
### 자주쓰는 필드 공통 옵션
- 모델과 연결된 장고 form에서 유효성 검사를 확인/ 성공 실패여부 . form을 안쓰고 모델만 쓰면 유효성 검사 로직을 거치지 않아서 그냥 저장됨.
- `blank`
- `null` : (defalut : False)
- `db_index` : (defalut : False)
- `default`
- `unique` : (defalut : False)
- `choices`
- `validators`
- `verbose_name`
- `help_text`
### ㅁ
- 설계한 데이터베이스 구조에 따라, 최대한 필드타입을 타이트하게 지정해주는것이, `입력값 오류`를 막을 수 있다.
- `black/null` 지정은 `최소화`
- `validators` 들이 다양하게/ 타이트하게 지정.
- 필요하다면 `validators`들을 추가로 타이트하게 지정.
- 프론트엔드에서의 유효성 검사는 사용자 편의, 백엔드에서의 유효성 검사는 필수. (프론트엔드의 값은 신뢰하면 안됨.)
- 직접 유효성 로직을 만들지 않기. 이미 잘 구성된 Features 들을 가져다 쓰기. 장고의 `Form/Model`을 통해 지원되며, `django-rest-freamwork`의 `Serializer`를 통해서도 지원 된다.

## django admin을 통한 데이터 관리.
### ㅇ
- `django.contrib.admin` 앱을 통해 제공. `/admin/`
- `django-admin-honeypot` 가짜 페이지 노출.
- 서비스 초기에, DB 관리 UI로 보면 마땅
- 관리도구 만들 시간을 줄이고, End-User 서비스에 집중.
- 내부의 Django-Form을 적응 이용.
### 모델 클래스를 admin에 등록.
- `admin.site.Register(Item)` 첫번째
- 두번째
```python
class PostAdmin(admin.ModelAdmin):
	pass

 admin.site.register(Item,ItemAdmin)
```
- 세번째
```python
@admin.register(Item)
class PostAdmin(admin.ModelAdmin):
	pass
```
### list_display 속성 정의
- 공통으로 사용 `models.py`의 model에서 속성 or 함수 추가.
- admin에서만 사용 `admin.py`에서 속성 정의 or 함수 추가.
```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['id','message', 'message_length', 'is_public', 'created_at', 'updated_at']
    list_display_links = ['message']
    list_filter = ['created_at', 'is_public']
    search_fields = ['message']

    def message_length(self, post):
        return f"{len(post.message)} 글자"
```
- 살펴본것. 
- 중요포인트는 장고 admin 공식 문서를 꼼꼼히 읽어보는것이 중요.
	- https://docs.djangoproject.com/en/3.0/ref/contrib/admin/

## Media with 장고.
 - 링크 : 

