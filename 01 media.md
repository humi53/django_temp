## Media.
### a
- static 파일.
	- 개발 리소스. 정적파일
	- 앱 / 프로젝트 단위로 저장/서빙
- Media 파일.
	- FileField/ImageField

### 사용
- >`pip install pillow`
	- 간단한 섬네일, 가로세로 크기변경 유용
	- 포토샾 같은 고품질 편집 - 이미지 매직 같은 라이브러리 사용.

### FileFiled ImageField 설명
#### FileFiled
- File Storage API를 통해 파일을 저장
	- 장고에서는 File System Storage만 지원. django-storages를 통해 확장 지원
- 해당 필드를 옵션 필드로 두고자 할 경우, blank=True 옵션 적용
#### ImageField (FileField 상속)
- Pillow를 통해 이미지 width/height 획등
	- Pillow 미설치 시에, ImageField를 추가한 makemigrations 수행에 실패합니다.
- 위 필드를 상속받은 커스텀 필드를 만들수 있음.
	- PDFField, ExcelField 등.

### 설치관리
- `requirements.txt` 생성
```txt
django~=3.0.0
pillow
```
- >`pip install -r requirements.txt` 
	- 라이브러리 한번에 설치.

- setting.py 편집
	- 최하단에 추가.
```python
MEDIA_URL = '/media/'	# 각 media 파일에 대한 URL Prefix, 필드명.url 속성에 의해서 참조되는 설정
MEDIA_ROOT = os.path.join(BASE_DIR, '..', 'public', 'media') # 파일필드를 통한 저장시에, 실제 파일을 저장할 ROOT 경로.
```
- `urls.py` 편집
```python
# from django.conf import global_settings
# from askcompany import settings # 2개를 합쳐줘야된다.
from django.conf import settings # 장고에서 자동으로 합쳐줌.
settings.MEDIA_URL
settings.MEDIA_ROOT
```


### 사용
- models적용 : `photo = models.ImageField(blank=True)`
- >`python manage.py makemigrations instagram`
- >`python manage.py migrate instagram`

### Media 파일 처리 순서.
- HttpRequest.FILES를 통해 파일이 전달
- 뷰 로직이나 폼 로직을 통해, 유효성 검증을 수행하고,
- FileField/ImageField 필드에 "경로(문자열)"를 저장하고,
- settings.MEDIA_ROOT 경로에 파일을 저장합니다.

### 사용할만한 필드옵션
- blank 
	- 업로드 옵션처리 여부 default : false
- upload_to
	- settings.MEDIA_ROOT 하위에서 저장한 파일명/경로명 결정
	- default : 파일명 그대로 settings.MEDIA_ROOT에 저장
	- `photo = models.ImageField(blank=True, upload_to='instagram/post/%Y%m/%d')`

### HTML Form enctype
- 추후에 다시.

### UUID를 통한 파일명 예시
```python
import os
from uuid import uuid4
from django.utils import timezone
def uuid_name_upload_to(instance, filename):
	app_label = instance.__class__._meta.app_label # 앱 별로
	cls_name = instance.__class__.__name__.lower() # 모델 별로
	ymd_path = timezone.now().strftime('%Y/%m/%d') # 업로드하는 년/월/일 별로
	uuid_name = uuid4().hex
	extension = os.path.splitext(filename)[-1].lower() # 확장자 추출하고, 소문자로 변환
	return '/'.join([
		app_label,
		cls_name,
		ymd_path,
		uuid_name[:2],
		uuid_name + extension,
	])
```

### 템플릿 media URL 처리
- 필드에 값이 없을경우 .url 실패됨. 안전하게 필드명 값 유무를 확인.
	`<img src="{{ post.photo.url }}" %}" />`
```python
{% if post.photo %}
	<img src="{{ post.photo.url }}" />
{% endif %}
```
- 참고 : 파일시스템상의절대경로가필요하다면, .path속성을활용하세요.
	- settings.MEDIA_ROOT와 조합

### 개발환경 media 파일서빙
- static 파일과 다르게, 장고 개발서버에서 서빙 미지원 개발편의성목적으로직접서빙Rule 추가가능
```python
from django.conf import settings
from django.conf.urls.static import static
# 중략
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### File Upload Handler
- 파일크기가2.5MB 이하일 경우
	- 메모리에담겨전달
	- MemoryFileUploadHandler
- 파일크기가2.5MB 초과일 경우
	- 디스크에담겨전달
	- TemporaryFileUploadHandler
- 관련설정
	- settings.FILE_UPLOAD_MAX_MEMORY_SIZE : defalut 2.5MB