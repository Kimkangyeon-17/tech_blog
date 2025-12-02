# Django 과목평가 준비

## Django 시작 명령어 및 순서

### 1. 가상환경 세팅
```bash
python -m venv venv
```

### 2. 가상환경 활성화
`source venv/Scripts/activate`


### 3. 필요한 패키지 설치
```bash
pip install django
```
또는
```bash
pip install -r requirements.txt
```

### 4. Django 프로젝트 시작
```bash
django-admin startproject <프로젝트명> .
```

### 5. 초기 DB 설정 (Django 기본 테이블 생성)
```bash
python manage.py migrate
```

### 6. App 생성
```bash
python manage.py startapp <앱이름>
```

### 7. settings.py에 앱 등록
```python
# 프로젝트/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myapp',  # 👈 생성한 앱 이름 추가
]
```

### 8. Model 작성
```python
# 앱/models.py
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.IntegerField()
    created_at = models.DateTimeField(auto_now_add=True)
```

### 9. Model을 DB에 반영
```bash
python manage.py makemigrations  # 설계도 생성
python manage.py migrate         # DB에 적용
```

차이점:
- `makemigrations`: 모델 변경사항을 migration 파일(설계도)로 생성
- `migrate`: migration 파일을 실제 DB에 반영

### 10. Views 작성
```python
# 앱/views.py
from django.shortcuts import render
from .models import Product

def product_list(request):
    products = Product.objects.all()
    context = {'products': products}
    return render(request, 'myapp/product_list.html', context)
```

### 11. 앱 urls.py 생성 및 작성
```python
# 앱/urls.py (새로 생성)
from django.urls import path
from . import views

app_name = 'myapp'
urlpatterns = [
    path('', views.product_list, name='product_list'),
]
```

### 12. 프로젝트 urls.py에 앱 URL 연결
```python
# 프로젝트/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('products/', include('myapp.urls')),  # 👈 앱 URL 연결
]
```

### 13. Templates 작성
```
앱/templates/앱/파일명.html
```

폴더 구조:
```
myapp/
    templates/
        myapp/
            product_list.html
            product_detail.html
```

### 14. Forms 작성 (필요시)

### 15. 관리자 계정 생성
```bash
python manage.py createsuperuser
```

### 16. Admin에 모델 등록
```python
# 앱/admin.py
from django.contrib import admin
from .models import Product

admin.site.register(Product)
```

---

## 자주 쓰는 명령어

- `python manage.py runserver` : 개발 서버 실행 (http://127.0.0.1:8000)
- `python manage.py createsuperuser`  관리자 계정 생성

---

## Static 파일 (CSS, JS, 이미지)

### 폴더 구조
```
myapp/
    static/
        myapp/
            css/
                style.css
            js/
                script.js
            images/
                logo.png
```

### settings.py 설정 확인
```python
STATIC_URL = '/static/'
```

## {{ }} vs {% %} 사용 차이점

- `{{ }}`: 변수값 출력할 때 사용, Python에서 `print()`랑 유사
- `{% %}`: 로직을 실행하거나 제어할 때 사용 (if, for 등)

## auto_add랑 auto_add_now의 차이점
- auto_add_now=True : 생성 시 딱 한 번만 설정됨, 이후 수정해도 값이 변하지 않음, 생성일시를 기록할 때 사용
- auto_now=True : 객체가 저장될 때마다 현재 시간으로 자동 업데이트, 최종 수정일시를 기록할 때 사용

## 템플릿 태그
- extends 태그는 2개 이상 사용 불가능

## 디자인 패턴
MVC vs MTV
MVC : Model, View, Controller
MTV : Model, Template, View
- django는 MTV 패턴 사용, Template가 Veiw의 역할, View가 Controller 역할을 함

## render와 redirect의 차이점
- render : 템플릿(HTML)을 렌더링해서 바로 응답, 데이터를 조회해서 화면에 보여줄 때와 GET 요청 처리할 때 사용
- redirect : 다른 URL로 이동, 데이터를 생성/수정/삭제한 후, POST 요청 처리 후에 사용, URL이 변경됨

## Form과 ModelForm의 차이
- Form : 사용자가 입력 데이터를 DB에 저장하지 않을 때
- ModelForm : 사용자 입력 데이터 DB에 저장해야 할 때, Model과 연결된 Form을 자동으로 생성해주는 기능 제공

## AbstractUser vs AbstractbaseUser 
- AbstractUser : Django 기본 User 모델을 그대로 사용하면서 필드만 추가
- AbstractBaseUser : User 모델을 처음부터 직접 만들어야 함

- from django.contrib.auth.models import AbstractUser를 통해서 AbstractUser 상속
- settings.py에 AUTH_USER_MODEL = 'accounts.User' 설정 필요


## 쿠키와 세션
- 쿠키 : 서버가 사용자의 웹 브라우저에 전송하는 작은 데이터 조각, key-value 형식
- 세션 : 서버에 저장되는 사용자 정보, 서버 측에서 생성되어 클라이언트와 서버 간의 상태를 유지, 상태 정보를 저장하는 데이터 저장 방식

## 회원가입 후 로그인 방법
form.save() 후 login() 호출

## 회원 탈퇴 순서
계정 삭제 후 로그아웃

## 레인보우 테이블 공격
- 대응 방안 : 임의의 문자열(salt)을 비밀번호에 붙여서 암호화 진행 

## 무차별 대입 공격
- 대응 방안 : 연산 속도를 늦추기 위해 의도적으로 비밀번호 검증 과정을 느리게 만듦, 키 스트레칭 기법 사용

## django 기본 비밀번호 해싱 알고리즘
PBKDF2

## 비밀번호 초기화
settings.py에 EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend' 설정