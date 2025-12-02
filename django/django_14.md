# Django 템플릿과 Jinja2

Two Scoops of Django 15장 "Django 템플릿과 Jinja2"를 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

Django는 다중 템플릿 엔진을 지원합니다. **Django 템플릿 언어(Django Template Language, DTL)**와 **Jinja2**이며 전부 Django에 내장되어 릴리즈되고 있습니다.

## 문법 차이

### 주요 차이점

| 용례 | DTL | Jinja2 |
| ---- | ---- | ---- |
| 메서드 호출 | `{{ user.get_favorites }}` | `{{ user.get_favorites() }}` |
| 인자 필터 | `{{ toppings|join:", " }}` | `{{ toppings|join(", ") }}` |
| 인자가 없는 루프 | `{% empty %}` | `{% else %}` |
| 루프 변수 | `{{ forloop }}` | `{{ loop }}` |
| 사이클 | `{% cycle "odd" "even" %}` | `{{ loop.cycle("odd", "even") }}` |

```html
<!-- DTL 예시 -->
{% for item in items %}
    <li class="{% cycle 'odd' 'even' %}">{{ item }}</li>
{% empty %}
    <li>항목이 없습니다</li>
{% endfor %}

<!-- Jinja2 예시 -->
{% for item in items %}
    <li class="{{ loop.cycle('odd', 'even') }}">{{ item }}</li>
{% else %}
    <li>항목이 없습니다</li>
{% endfor %}
```

### 공통점

아래는 많은 공통점을 보이는 문법입니다.

| 용도 | DTL | Jinja2 |
| ---- | ---- | ---- |
| 조건절 | `{% if topping=='sprinkles' %}` | `{% if topping=='sprinkles' %}` |
| 조건절 | `{% elif topping=='fudge' %}` | `{% elif topping=='fudge' %}` |
| 조건절 | `{% else %}` | `{% else %}` |
| is 연산 | `{% if customer is happy %}` | `{% if customer is happy %}` |

```html
<!-- DTL과 Jinja2 모두 동일 -->
{% if user.is_authenticated %}
    <p>환영합니다, {{ user.username }}님!</p>
{% elif user.is_guest %}
    <p>게스트로 로그인되었습니다.</p>
{% else %}
    <p>로그인이 필요합니다.</p>
{% endif %}
```

## 다중 템플릿 엔진 설정

`settings.TEMPLATES`를 설정하여 **특정 디렉토리의 템플릿에는 DTL을, 다른 디렉토리의 템플릿에는 Jinja2를 사용**할 수 있습니다.

```python
# settings.py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],  # DTL 템플릿
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
            ],
        },
    },
    {
        'BACKEND': 'django.template.backends.jinja2.Jinja2',
        'DIRS': [BASE_DIR / 'jinja2'],  # Jinja2 템플릿
        'APP_DIRS': True,
        'OPTIONS': {
            'environment': 'myproject.jinja2.environment',
        },
    },
]
```

```
프로젝트 구조 예시:
myproject/
    ├── templates/           # DTL 템플릿
    │   ├── base.html
    │   └── posts/
    │       └── list.html
    ├── jinja2/             # Jinja2 템플릿
    │   ├── base.html
    │   └── reports/
    │       └── detail.html
    └── myproject/
        └── jinja2.py       # Jinja2 환경 설정
```

## DTL의 장점

- 이용을 위해 필요한 모든 것이 이미 Django 안에 포함되어 있으며, Django의 문서화 시스템 안에 DTL의 기능이 훌륭하게 문서화되어 있습니다
- **DTL + Django 조합은 DTL + Jinja2 조합보다 더 많이 이용되면서 검증되었고 더 성숙**합니다
- **대부분의 서드 파티 Django 패키지에서 DTL을 이용**하고 있습니다. 이를 Jinja2로 변경하려면 추가적인 작업이 필요합니다
- 많은 양의 DTL 코드를 Jinja2로 변경하는 것은 상당히 큰 작업입니다

```html
<!-- DTL의 장점: Django admin, 서드파티 패키지와 완벽한 호환 -->
{% load static %}
{% load crispy_forms_tags %}

<form method="post">
    {% csrf_token %}
    {{ form|crispy }}
    <button type="submit">제출</button>
</form>
```

## Jinja2의 장점

- **Django와 독립적으로 사용**할 수 있습니다
- Jinja2의 문법이 **Python 문법에 좀 더 가깝기** 때문에 많은 이들에게 Jinja2 문법이 더 직관적입니다
- 일반적으로 **Jinja2가 좀 더 명시적이고 명확하게 구성**되어 있습니다
- Jinja2에는 로직상의 임의적 제약이 덜합니다. 예를 들면 **Jinja2에서는 필터에 무한대의 인자를 사용할 수 있는 반면 DTL에선 오직 한 개만 허용**됩니다
- **Jinja2가 일반적으로 더 빠릅니다**

```html
<!-- Jinja2의 장점: Python에 가까운 문법 -->
{% for user in users if user.is_active %}
    <p>{{ user.get_full_name() }}</p>
{% endfor %}

<!-- 메서드에 인자 전달 가능 -->
{{ post.get_summary(150) }}

<!-- 복잡한 표현식 사용 가능 -->
{{ (price * quantity * (1 - discount/100))|round(2) }}
```

## Jinja2를 Django에서 사용할 때 유의점

### 1. CSRF와 Jinja2

Jinja2는 DTL과는 다른 방식으로 Django의 CSRF에 접근합니다. Jinja2 템플릿에 CSRF를 포함시키기 위해서는 **폼을 렌더링할 때 필요한 특별한 HTML**이 있습니다.

```html
<!-- Jinja2에서 CSRF 토큰 사용 -->
<form method="post">
    <div style="display:none">
        <input type="hidden" name="csrfmiddlewaretoken" value="{{ csrf_token }}">
    </div>
    <!-- 또는 간단하게 -->
    <input type="hidden" name="csrfmiddlewaretoken" value="{{ csrf_token }}">
    
    <input type="text" name="username">
    <button type="submit">제출</button>
</form>
```

```python
# myproject/jinja2.py - CSRF 지원을 위한 설정
from django.templatetags.static import static
from django.urls import reverse
from jinja2 import Environment

def environment(**options):
    env = Environment(**options)
    env.globals.update({
        'static': static,
        'url': reverse,
    })
    return env
```

### 2. Jinja2 템플릿에서 템플릿 태그 이용하기

Django 스타일의 템플릿 태그는 Jinja2에서는 불가능합니다. 특정 템플릿 태그 기능이 필요하다면 태그의 기능에 따라 **다음 기술 중 하나를 이용하여 변환**해야 합니다:

- 해당 기능을 함수로 변환
- Jinja2 확장 프로그램을 작성

```python
# myproject/jinja2.py - 함수로 변환
from jinja2 import Environment

def get_popular_posts(count=5):
    from posts.models import Post
    return Post.objects.filter(is_published=True).order_by('-views')[:count]

def environment(**options):
    env = Environment(**options)
    env.globals.update({
        'get_popular_posts': get_popular_posts,
    })
    return env
```

```html
<!-- Jinja2 템플릿에서 사용 -->
<h3>인기 게시글</h3>
<ul>
{% for post in get_popular_posts(3) %}
    <li>{{ post.title }}</li>
{% endfor %}
</ul>
```

### 3. Jinja2 템플릿에서 Django 스타일의 필터 이용하기

DTL에서 꾸준히 이용해온 Django 필터들은 기본적으로 함수이기 때문에 **커스텀 Jinja2 환경에 템플릿 필터를 손쉽게 포함**시킬 수 있습니다.

```python
# myproject/jinja2.py
from jinja2 import Environment
from django.contrib.humanize.templatetags.humanize import intcomma, naturaltime

def environment(**options):
    env = Environment(**options)
    
    # Django 필터를 Jinja2 필터로 추가
    env.filters['intcomma'] = intcomma
    env.filters['naturaltime'] = naturaltime
    
    return env
```

```html
<!-- Jinja2 템플릿에서 Django 필터 사용 -->
<p>조회수: {{ post.views|intcomma }}</p>
<p>작성: {{ post.created_at|naturaltime }}</p>
```

**뷰에서 속성으로 Django 템플릿 필터를 추가하기 위한 믹스인**을 생성할 수 있습니다.

```python
# common/mixins.py
class Jinja2FilterMixin:
    """Jinja2 템플릿에 Django 필터를 추가하는 믹스인"""
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        
        # Django 필터 함수들을 컨텍스트에 추가
        from django.contrib.humanize.templatetags.humanize import intcomma
        context['intcomma'] = intcomma
        
        return context

# views.py
from django.views.generic import ListView
from .mixins import Jinja2FilterMixin

class PostListView(Jinja2FilterMixin, ListView):
    model = Post
    template_name = 'posts/list.html'  # Jinja2 템플릿
```

### 4. Jinja2 템플릿에서 콘텍스트 프로세서가 호출되지 않는 것을 유의

**콘텍스트 프로세서**란 DTL을 이용할 때 `settings.TEMPLATES`의 `context_processors` 옵션에서 정의된 내장 함수의 리스트이며 요청 객체를 받아 콘텍스트에 추가될 아이템의 리스트를 반환해 줍니다. 하지만 **이를 Jinja2에서 이용할 때는 문제가 발생**할 수 있습니다.

```python
# myproject/jinja2.py - 콘텍스트 프로세서 대체
from jinja2 import Environment

def environment(**options):
    env = Environment(**options)
    
    # DTL의 콘텍스트 프로세서 기능을 Jinja2에서 구현
    env.globals.update({
        'MEDIA_URL': '/media/',
        'STATIC_URL': '/static/',
    })
    
    return env
```

```python
# 또는 커스텀 콘텍스트 함수 작성
# myproject/context_processors.py
def site_settings(request):
    return {
        'SITE_NAME': 'My Site',
        'ADMIN_EMAIL': 'admin@example.com',
    }

# myproject/jinja2.py
from .context_processors import site_settings

def environment(**options):
    env = Environment(**options)
    
    # 요청 객체가 필요한 경우
    def request_context(request):
        return site_settings(request)
    
    env.globals['request_context'] = request_context
    
    return env
```

### 5. Jinja2의 Environment 객체는 정적인 것으로 간주

프로젝트에서 **가장 첫 템플릿이 로드되었을 때 Jinja2 Environment 클래스를 정적 객체처럼 실체화(instantiate)하여 이용**합니다.

```python
# myproject/jinja2.py
from jinja2 import Environment

def environment(**options):
    """
    이 함수는 앱 시작 시 한 번만 호출됩니다.
    따라서 동적으로 변경되는 값은 함수로 제공해야 합니다.
    """
    env = Environment(**options)
    
    # ❌ 나쁜 예 - 정적 값만 할당됨
    from django.conf import settings
    env.globals['DEBUG'] = settings.DEBUG  # 앱 시작 시점의 값만 사용
    
    # ✅ 좋은 예 - 동적 값은 함수로 제공
    def get_debug():
        from django.conf import settings
        return settings.DEBUG
    
    env.globals['get_debug'] = get_debug
    
    # 항상 최신 사용자 정보를 가져오는 함수
    def get_current_user(request):
        return request.user if request else None
    
    env.globals['get_current_user'] = get_current_user
    
    return env
```

```html
<!-- Jinja2 템플릿에서 동적 값 사용 -->
{% if get_debug() %}
    <div class="debug-info">디버그 모드</div>
{% endif %}

<p>현재 사용자: {{ get_current_user(request).username }}</p>
```

## 실전 Jinja2 환경 설정 예시

```python
# myproject/jinja2.py - 완전한 예시
from jinja2 import Environment
from django.templatetags.static import static
from django.urls import reverse
from django.contrib.humanize.templatetags.humanize import intcomma, naturaltime

def environment(**options):
    env = Environment(**options)
    
    # Django 기본 기능들
    env.globals.update({
        'static': static,
        'url': reverse,
    })
    
    # Django 필터들
    env.filters.update({
        'intcomma': intcomma,
        'naturaltime': naturaltime,
    })
    
    # 커스텀 함수들
    def get_site_name():
        from django.conf import settings
        return getattr(settings, 'SITE_NAME', 'My Site')
    
    env.globals['get_site_name'] = get_site_name
    
    # 커스텀 필터
    def currency(value):
        return f"{value:,}원"
    
    env.filters['currency'] = currency
    
    return env
```

## DTL vs Jinja2 선택 가이드

### DTL을 선택해야 하는 경우

- Django admin을 많이 사용하는 프로젝트
- 많은 서드 파티 패키지를 사용하는 프로젝트
- 팀원들이 Django에 익숙한 경우
- 기존 DTL 코드가 많은 프로젝트

### Jinja2를 선택해야 하는 경우

- 템플릿 렌더링 성능이 중요한 프로젝트
- Python 문법에 가까운 템플릿을 선호하는 경우
- Django와 독립적인 템플릿이 필요한 경우
- 복잡한 필터나 함수 호출이 많은 경우

```python
# 실용적인 접근: 혼합 사용
# - Admin, 폼 등은 DTL 사용
# - 성능이 중요한 리포트, 대시보드는 Jinja2 사용

# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('posts/', views.PostListView.as_view()),  # DTL 사용
    path('reports/', views.ReportView.as_view()),  # Jinja2 사용
]
```

DTL과 Jinja2는 각각의 장단점이 있으므로, **프로젝트의 요구사항과 팀의 상황에 맞춰 적절히 선택하거나 혼합하여 사용**하는 것이 중요합니다.