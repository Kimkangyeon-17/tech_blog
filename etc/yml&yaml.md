# yml과 yaml 파일 이해하기

## yaml
### yaml은 무엇인가?

YAML은 "YAML Ain't Markup Language" 혹은 "Yet Another Markup Language"라고 알려져 있습니다. 2001년 Clark Evans에 의해 개발되었고 데이터 직렬화 표준으로 사람이 읽기 쉬운 데이터 표현 형식입니다. yaml은 SON이나 XML과 비교했을 때 YAML은 들여쓰기와 간단한 문법으로 구성되어 있어 사람이 읽고 이해하기 훨씬 쉽습니다. 또한, 주석 기능 지원으로 설정에 대한 설명을 추가할 수 있고 팀원 간 설정 파일 공유 시 이해도가 증가합니다.

### .yml vs .yaml의 차이점

yml과 yaml의 기능적 차이는 전혀 없습니다. `.yml`파일 확장자를 사용하는 이유는 3글자 확장자를 선호하는 시스템에서 유래되었으며 `.yaml`이 공식 권장 확장자입니다.


### yaml의 특징과 장점

아래는 yaml의 특징과 장점입니다.

- 가독성: 들여쓰기로 구조화, 직관적
- 간결성: JSON보다 간단한 문법
- 다양한 데이터 타입 지원: 문자열, 숫자, 불린, 배열, 객체
- 주석 지원: #을 사용한 주석 작성 가능
- 다중 문서: 하나의 파일에 여러 문서 저장 가능
- 언어 독립적: 대부분의 프로그래밍 언어에서 지원

## yaml 기본 문법과 구조

yaml의 기본 문법과 구조에 대해서 알아보겠습니다.

### 들여쓰기 규칙
- 스페이스만 사용 가능합니다.(tab 사용 금지)
- 보통 2칸 혹은 4칸으로 일관된 들여쓰기를 합니다.
- 같은 레벨은 같은 들여쓰기를 해야 합니다.
- 중첩 구조 표현
```yaml
# 올바른 예시
parent:
  child1: value1
  child2: value2
  child3:
    grandchild: value3

# 잘못된 예시 (탭 사용)
parent:
	child: value  # 탭 사용 - 오류 발생
```

### 데이터 타입별 작성법
다음은 데이터 타입별 yaml 파일 작성법을 살펴보겠습니다.

1. 키-값 쌍(Key-Value)

```yaml
# 기본 형태
key: value
name: "홍길동"
age: 25

# 중첩 객체
person:
  name: "김철수"
  age: 30
  address:
    city: "서울"
    district: "강남구"
```

2. 리스트/배열

```yaml
# 방법 1: 대시(-) 사용
fruits:
  - apple
  - banana
  - orange

# 방법 2: 인라인 형태
colors: [red, green, blue]

# 객체 리스트
users:
  - name: "김철수"
    age: 25
  - name: "이영희"
    age: 30
```

3. 문자열

```yaml
# 일반 문자열
title: Hello World

# 따옴표 사용
description: "특수문자 포함: @#$%"
path: 'C:\Program Files\App'

# 여러 줄 문자열
multiline1: |
  첫 번째 줄
  두 번째 줄
  세 번째 줄

multiline2: >
  긴 문장을
  한 줄로
  합쳐서 표현
```
4. 숫자와 boolean 값

```yaml
# 숫자
integer: 42
float: 3.14
scientific: 1.2e+3

# 불린
is_active: true
is_deleted: false
is_public: yes
is_private: no
```

5. null 값

```yaml
empty_value: null
another_empty: ~
no_value:  # 값 없음도 null
```

### 주석 작성법

아래는 yaml에서 주석을 작성하는 방법입니다.

```yaml
# 전체 줄 주석
name: "홍길동"  # 인라인 주석

# 여러 줄 주석
# 이것은 첫 번째 줄입니다.
# 이것은 두 번째 줄입니다.

person:
  # 개인 정보
  name: "김철수"
  age: 30
```

## yaml 작성 시 주의사항
### 문법 오류 방지
- 들여쓰기 일관성 : 같은 레벨은 반드시 같은 들여쓰기를 하여야 합니다.
- tab 사용 금지 : yaml에서는 tab 대신 스페이스만 사용 가능합니다.
- 콜론 뒤 스페이스 : `key: value`처럼 콜론 뒤에는 스페이스를 처리해야 합니다. `key:value`는 잘못된 문법입니다.
- 특수문자 이스케이프 : 특수문자 이스케이프는 따옴표 내에서 처리해야 합니다.

### 데이터 타입 주의사항
yaml에서는 각 데이터 타입별 올바른 방법으로 작성해야 합니다.
```yaml
# 문제가 될 수 있는 예시들
version: 1.0          # 숫자로 인식됨
postal_code: 01234    # 앞의 0이 제거될 수 있음
is_true: yes          # true로 변환됨

# 올바른 해결법
version: "1.0"        # 문자열로 명시
postal_code: "01234"  # 문자열로 명시
is_true: "yes"        # 문자열로 유지하려면 따옴표 사용
```

### 보안 주의사항

민감한 정보등 보안 사항은 환경 변수를 통해 작성해야 합니다.
```yaml
# 민감한 정보는 환경변수로 처리
database:
  password: ${DB_PASSWORD}  # 환경변수 사용
  
# GitHub Secrets 활용 (GitHub Actions)
env:
  SECRET_KEY: ${{ secrets.SECRET_KEY }}
```

## YAML 사용 예시

### Django 설정 예시

Django 프로젝트에서 YAML 파일을 설정 파일로 활용하는 방법을 알아보겠습니다.

```yaml
# config/settings.yaml
database:
  default:
    engine: 'django.db.backends.postgresql'
    name: 'myproject'
    user: 'postgres'
    password: 'password'
    host: 'localhost'
    port: 5432

redis:
  host: 'localhost'
  port: 6379
  db: 0

logging:
  level: 'INFO'
  format: '%(asctime)s - %(name)s - %(levelname)s - %(message)s'

debug: true
allowed_hosts:
  - 'localhost'
  - '127.0.0.1'
```

```python
# settings.py
import yaml
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

# YAML 파일 읽기
with open(os.path.join(BASE_DIR, 'config', 'settings.yaml'), 'r', encoding='utf-8') as file:
    config = yaml.safe_load(file)

# 설정 적용
DATABASES = {
    'default': {
        'ENGINE': config['database']['default']['engine'],
        'NAME': config['database']['default']['name'],
        'USER': config['database']['default']['user'],
        'PASSWORD': config['database']['default']['password'],
        'HOST': config['database']['default']['host'],
        'PORT': config['database']['default']['port'],
    }
}

DEBUG = config['debug']
ALLOWED_HOSTS = config['allowed_hosts']
```

위와 같은 방법으로 Django에서 YAML 파일을 읽어와 설정값으로 활용할 수 있습니다. 이렇게 하면 코드와 설정을 분리하여 더 유연하고 관리하기 쉬운 프로젝트 구조를 만들 수 있습니다.

### github actions 사용 예시

CI/CD 파이프라인에서 YAML을 활용하는 예시입니다.

```yaml
# .github/workflows/django.yml
name: Django CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: 3.10
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    
    - name: Run tests
      run: |
        python manage.py test
```

## 마무리
YAML은 설정 파일, CI/CD 파이프라인, 컨테이너 오케스트레이션 등 다양한 영역에서 활용되는 중요한 데이터 형식입니다. 특히 Django 개발자에게는 프로젝트 설정 관리, GitHub Actions를 통한 자동화, Docker Compose 설정 등에서 필수적으로 사용됩니다. 

올바른 문법과 베스트 프랙티스를 따라 YAML을 작성하면 더 효율적이고 관리하기 쉬운 개발 환경을 구축할 수 있습니다.