# Django 기초

django의 기초에 대해 학습해보고자 합니다. django의 기본서라고 할 수 있는 Two scoops of django를 읽으며 평소 몰랐던 내용, 중요한 내용들 위주로 정리해 볼 예정입니다.
## Import 규칙과 Best Practices

### Import 순서
Django 프로젝트에서는 일관된 import 순서를 유지하는 것이 중요합니다:

1. **표준 라이브러리 import**
2. **코어 Django import** 
3. **Django와 무관한 외부 앱 import**
4. **프로젝트 앱 import**

```python
# 1. 표준 라이브러리
import os
from pathlib import Path

# 2. 코어 Django
from django.db import models
from django.contrib.auth.models import User

# 3. 외부 앱
import requests
from rest_framework import serializers

# 4. 프로젝트 앱
from core.views import FoodMixin
from .models import WaffleCone
```

### Import 방식별 사용법

**절대 Import (권장)**
```python
from core.views import FoodMixin
```
- **용도**: 외부에서 임포트해서 현재 앱에서 이용할 때
- **장점**: 명확하고 프로젝트 구조 변경에 안정적

**명시적 상대 Import**
```python
from .models import WaffleCone
```
- **용도**: 같은 앱 내 다른 모듈에서 import할 때
- **장점**: 앱 내부 의존성이 명확함

**암묵적 상대 Import (지양)**
```python
from models import WaffleCone  # 사용 지양
```
- **문제점**: 모호하고 예측하기 어려움

> ⚠️ **주의사항**: `import *`는 네임스페이스 오염을 야기하므로 사용을 지양해야 합니다.

### URL 패턴 명명 규칙

URL 패턴 이름에는 대시(-) 대신 밑줄(_)을 사용해야 합니다:

```python
# ❌ 나쁜 예제
from django.urls import path

urlpatterns = [
    path('add/', views.add_topping, name='add-topping'),  # 대시 사용
]

# ✅ 좋은 예제  
urlpatterns = [
    path('add/', views.add_topping, name='add_topping'),  # 밑줄 사용
]
```

## Django 프로젝트 시작하기

### 프로젝트 생성 명령어

```bash
# 프로젝트 생성
$ django-admin startproject mysite
$ cd mysite

# 앱 생성
$ python manage.py startapp my_app
```

### 기본 프로젝트 구조

```
mysite/
    ├── manage.py
    │
    ├── 📂 my_app/   
    │   ├── __init__.py 
    │   ├── admin.py
    │   ├── apps.py
    │   ├── migrations/
    │   ├── models.py
    │   ├── tests.py
    │   ├── urls.py
    │   └── views.py
    │
    └── 📂 mysite/
        ├── __init__.py     
        ├── settings.py
        ├── urls.py
        ├── wsgi.py
        └── asgi.py
```

### startproject의 역할
기본적인 Django 프로젝트 템플릿을 생성하고 바로 프로젝트 개발을 가능하게 해줍니다.

## Django 앱 설계 기본 원칙

### 핵심 개념 정리

**Django 프로젝트**
- Django 웹 프레임워크를 기반으로 한 전체 웹 애플리케이션

**Django 앱**
- 프로젝트의 특정 기능을 담당하는 작은 라이브러리
- 하나의 명확한 목적을 가져야 함
- 앱 이름은 복수형으로 작성 권장 (users, posts, orders)

**INSTALLED_APPS**
- 프로젝트에서 사용할 Django 앱들을 settings.py에 등록한 목록

**Third Party Django 패키지**
- PyPI 등을 통해 배포되는 재사용 가능한 Django 앱

### 앱 구조와 모듈별 역할

#### 공통 앱 모듈

```
scoops/
    ├── __init__.py
    ├── admin.py          # Django 관리자 설정
    ├── apps.py           # 앱 설정
    ├── forms.py          # Django 폼 정의
    ├── management/       # 커스텀 관리 명령어
    ├── migrations/       # 데이터베이스 마이그레이션
    ├── models.py         # 데이터 모델 정의
    ├── templatetags/     # 커스텀 템플릿 태그
    ├── tests/           # 테스트 코드
    ├── urls.py          # URL 패턴 정의
    └── views.py         # 뷰 로직
```

#### 비공통 앱 모듈 (선택적)

```
scoops/
    ├── behaviors.py         # 모델 믹스인
    ├── constants.py         # 앱 레벨 상수
    ├── context_processors.py # 템플릿 컨텍스트 프로세서
    ├── decorators.py        # 커스텀 데코레이터
    ├── db/                  # 커스텀 데이터베이스 컴포넌트
    ├── exceptions.py        # 커스텀 예외
    ├── fields.py           # 커스텀 모델 필드
    ├── factories.py        # 테스트 데이터 팩터리
    ├── helpers.py          # 헬퍼 함수
    ├── managers.py         # 커스텀 모델 매니저
    ├── middleware.py       # 커스텀 미들웨어
    ├── signals.py          # Django 시그널
    ├── utils.py            # 유틸리티 함수
    └── viewmixins.py       # 뷰 믹스인
```

#### 각 모듈의 상세 설명

**behaviors.py** - 모델 믹스인 정의
```python
from django.db import models

class TimeStampedModel(models.Model):
    """자동으로 생성일시와 수정일시를 관리하는 추상 모델"""
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        abstract = True

class SoftDeleteModel(models.Model):
    """소프트 삭제를 지원하는 추상 모델"""
    is_deleted = models.BooleanField(default=False)
    deleted_at = models.DateTimeField(null=True, blank=True)
    
    class Meta:
        abstract = True
```

**constants.py** - 앱 레벨 상수 정의
```python
# 사용자 상태
USER_STATUS_CHOICES = [
    ('active', '활성'),
    ('inactive', '비활성'),
    ('pending', '대기중'),
]

# 기본 설정값
DEFAULT_PAGE_SIZE = 20
MAX_UPLOAD_SIZE = 5 * 1024 * 1024  # 5MB
```

**managers.py** - 커스텀 모델 매니저
```python
from django.db import models

class ActiveUserManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(is_active=True)

class PublishedPostManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(status='published')
```

## Settings와 Requirements 파일 관리

### 환경별 Settings 분리

비밀 정보 보호와 환경별 최적화를 위해 settings를 여러 파일로 분리하여 관리합니다:

```
settings/
    ├── __init__.py
    ├── base.py         # 공통 설정
    ├── local.py        # 로컬 개발환경
    ├── development.py  # 개발 서버환경
    ├── staging.py      # 스테이징 환경
    ├── test.py         # 테스트 환경
    └── production.py   # 운영 환경
```

#### base.py - 공통 설정
```python
import os
from pathlib import Path
from django.core.exceptions import ImproperlyConfigured

# Build paths
BASE_DIR = Path(__file__).resolve().parent.parent.parent

def get_env_variable(var_name):
    """환경 변수를 가져오거나 예외를 발생시킴"""
    try:
        return os.environ[var_name]
    except KeyError:
        error_msg = f"Set the {var_name} environment variable"
        raise ImproperlyConfigured(error_msg)

# Application definition
DJANGO_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]

THIRD_PARTY_APPS = [
    'rest_framework',
    'corsheaders',
]

LOCAL_APPS = [
    'users',
    'posts',
    'comments',
]

INSTALLED_APPS = DJANGO_APPS + THIRD_PARTY_APPS + LOCAL_APPS

# 미디어 및 정적 파일
STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'
STATICFILES_DIRS = [BASE_DIR / 'static']

MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

#### local.py - 로컬 개발환경
```python
from .base import *

# 개발 전용 설정
DEBUG = True
ALLOWED_HOSTS = ['localhost', '127.0.0.1']

# 개발용 데이터베이스
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

# 개발 도구
INSTALLED_APPS += [
    'django_extensions',
    'debug_toolbar',
]

MIDDLEWARE += [
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]

# 디버그 툴바 설정
INTERNAL_IPS = ['127.0.0.1']
```

#### production.py - 운영환경
```python
from .base import *

# 보안 설정
DEBUG = False
ALLOWED_HOSTS = ['.yourdomain.com']

# 보안 강화
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
SECURE_CONTENT_TYPE_NOSNIFF = True
SECURE_BROWSER_XSS_FILTER = True
X_FRAME_OPTIONS = 'DENY'

# 운영 데이터베이스
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': get_env_variable('DB_NAME'),
        'USER': get_env_variable('DB_USER'),
        'PASSWORD': get_env_variable('DB_PASSWORD'),
        'HOST': get_env_variable('DB_HOST'),
        'PORT': get_env_variable('DB_PORT'),
    }
}

# 캐시 설정
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': get_env_variable('REDIS_URL'),
    }
}
```

### 환경 변수 관리

#### .env 파일 사용 (python-decouple 라이브러리)
```python
# settings/base.py
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
```

#### 환경 변수 예시 (.env 파일)
```bash
SECRET_KEY=your-secret-key-here
DEBUG=True
DB_NAME=myproject_db
DB_USER=myproject_user
DB_PASSWORD=secure_password
DB_HOST=localhost
DB_PORT=5432
```

### Requirements 파일 관리

환경별로 필요한 패키지만 설치하기 위해 requirements를 분리합니다:

```
requirements/
    ├── base.txt        # 공통 패키지
    ├── local.txt       # 로컬 개발용
    ├── staging.txt     # 스테이징용
    └── production.txt  # 운영용
```

#### base.txt - 공통 패키지
```txt
Django==4.2.7
djangorestframework==3.14.0
django-cors-headers==4.3.1
psycopg2-binary==2.9.7
pillow==10.0.1
python-decouple==3.8
```

#### local.txt - 개발용 패키지
```txt
-r base.txt

# 개발 도구
django-debug-toolbar==4.2.0
django-extensions==3.2.3
ipython==8.15.0

# 테스팅
pytest==7.4.2
pytest-django==4.5.2
factory-boy==3.3.0
coverage==7.3.2
```

#### production.txt - 운영용 패키지
```txt
-r base.txt

# 운영 서버용
gunicorn==21.2.0
redis==5.0.0
sentry-sdk==1.32.0
```

### 파일 경로 처리

Django settings에서 파일 경로는 하드코딩하지 말고 BASE_DIR을 기준으로 설정합니다:

```python
from pathlib import Path

# 올바른 방법
BASE_DIR = Path(__file__).resolve().parent.parent

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',  # pathlib 사용
    }
}

STATICFILES_DIRS = [
    BASE_DIR / 'static',
    BASE_DIR / 'assets',
]

# 템플릿 경로
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        # ...
    },
]
```

## 프로젝트 실행 방법

### 환경별 실행 명령어

```bash
# 로컬 개발환경
python manage.py runserver --settings=myproject.settings.local

# 스테이징 환경
python manage.py runserver --settings=myproject.settings.staging

# 운영 환경
python manage.py runserver --settings=myproject.settings.production
```

### 환경 변수로 설정 지정
```bash
# .bashrc 또는 .zshrc에 추가
export DJANGO_SETTINGS_MODULE=myproject.settings.local

# 또는 프로젝트별 .env 파일에서
DJANGO_SETTINGS_MODULE=myproject.settings.production
```

## 마이그레이션 관리

```bash
# 마이그레이션 파일 생성
python manage.py makemigrations

# 특정 앱의 마이그레이션만 생성
python manage.py makemigrations users

# 마이그레이션 적용
python manage.py migrate

# 마이그레이션 상태 확인
python manage.py showmigrations
```

이러한 구조와 원칙을 따르면 확장 가능하고 유지보수가 용이한 Django 프로젝트를 구축할 수 있습니다.

지금까지 Two scoops of django의 1 ~ 5장까지의 내용을 요약, 정리한 내용입니다. 