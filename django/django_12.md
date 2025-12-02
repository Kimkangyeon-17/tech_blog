Two Scoops of Django 13장 "템플릿의 모범적인 이용"을 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

## 템플릿의 위치

프로젝트 템플릿의 대부분은 메인 `templates/` 디렉토리 안에 위치시켜야 합니다.

```
✅ 좋은 예시
myproject/
    ├── templates/
    │   ├── base.html
    │   ├── accounts/
    │   │   ├── login.html
    │   │   └── profile.html
    │   └── posts/
    │       ├── list.html
    │       └── detail.html
    └── accounts/
        ├── models.py
        └── views.py

❌ 나쁜 예시
myproject/
    ├── templates/
    │   └── base.html
    └── accounts/
        ├── templates/
        │   ├── login.html
        │   └── profile.html
        ├── models.py
        └── views.py
```

위와 같은 경우 어떤 방법이든 크게 상관은 없는 것은 사실입니다. Django 패키지는 일반적으로 자기 자신 안에 `templates/` 디렉토리를 가지고 있습니다. 이 앱 자체의 템플릿들을 메인 `templates/` 디렉토리 안의 템플릿으로 오버라이딩함으로써 우리만의 디자인과 스타일을 입힐 수 있습니다.

```
# settings.py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],  # 메인 템플릿 디렉토리
        'APP_DIRS': True,  # 앱별 템플릿 디렉토리 검색
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
            ],
        },
    },
]
```

## 템플릿 아키텍처 패턴

### 2중 템플릿 구조 예시

```
templates/
    ├── base.html
    ├── dashboard.html          # base.html 확장
    └── profiles/
        ├── profile_detail.html # base.html 확장
        └── profile_form.html   # base.html 확장
```

**2중 템플릿 구조**를 보면 모든 템플릿은 하나의 `base.html` 파일로부터 상속받아 생성됩니다. **여러 앱을 일관된 구성으로 보여주기에 최적화된 구성**입니다.

```
<!-- base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Site{% endblock %}</title>
</head>
<body>
    <nav>{% block navigation %}{% endblock %}</nav>
    <main>{% block content %}{% endblock %}</main>
</body>
</html>

<!-- dashboard.html -->
{% extends "base.html" %}
{% block title %}Dashboard{% endblock %}
{% block content %}
    <h1>Dashboard</h1>
{% endblock %}
```

### 3중 템플릿 구조 예시

```
templates/
    ├── base.html
    ├── dashboard.html          # base.html 확장
    └── profiles/
        ├── base_profiles.html  # base.html 확장
        ├── profile_detail.html # base_profiles.html 확장
        └── profile_form.html   # base_profiles.html 확장
```

**3중 구조 구성**은 **섹션별로 레이아웃이 다른 경우에 최적화된 구성**입니다. 섹션별로 HTML이 각기 다르지만 기능적으로는 그룹으로 묶이길 바랄 때 유용합니다.

```
<!-- base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Site{% endblock %}</title>
</head>
<body>
    {% block content %}{% endblock %}
</body>
</html>

<!-- profiles/base_profiles.html -->
{% extends "base.html" %}
{% block content %}
    <div class="profile-sidebar">
        {% block sidebar %}{% endblock %}
    </div>
    <div class="profile-main">
        {% block profile_content %}{% endblock %}
    </div>
{% endblock %}

<!-- profiles/profile_detail.html -->
{% extends "profiles/base_profiles.html" %}
{% block sidebar %}
    <ul>
        <li>내 정보</li>
        <li>설정</li>
    </ul>
{% endblock %}
{% block profile_content %}
    <h1>{{ user.username }}</h1>
{% endblock %}
```

**중요**: 템플릿의 계층 체계가 복잡해지면 HTML을 디버그하고 수정하고 확장하며 CSS 스타일을 엮기가 매우 어려워집니다. **템플릿 블록들을 가능한 한 단순한 상속 관계로 구성**해야 템플릿을 작업하기 쉽고 좀 더 유지 관리하기 편해집니다.

## 템플릿에서 프로세싱 제한하기

템플릿에서 처리하는 프로세싱은 **적으면 적을수록 좋습니다**. 템플릿 레이어에서의 쿼리 수행과 이터레이션은 특히 문제가 됩니다.

### 주의 사항 1: 템플릿상에서 처리하는 aggregation 메서드

템플릿에서 집계 연산을 수행하면 성능이 저하됩니다. 이러한 연산은 뷰나 모델에서 처리해야 합니다.

```
<!-- ❌ 나쁜 예 - 템플릿에서 집계 -->
{% for post in posts %}
    <h2>{{ post.title }}</h2>
    <p>댓글 수: {{ post.comments.count }}</p>  <!-- N+1 문제 발생 -->
{% endfor %}
```

```
# ✅ 좋은 예 - 뷰에서 미리 집계
def post_list(request):
    posts = Post.objects.annotate(
        comment_count=Count('comments')
    )
    return render(request, 'posts/list.html', {'posts': posts})
```

```
<!-- 템플릿에서는 미리 계산된 값만 표시 -->
{% for post in posts %}
    <h2>{{ post.title }}</h2>
    <p>댓글 수: {{ post.comment_count }}</p>
{% endfor %}
```

### 주의 사항 2: 템플릿상에서 조건문으로 하는 필터링

템플릿 상에서 거대한 루프문과 if문을 돌려서 필터링을 하는 것은 **성능이 심각하게 저하**될 것입니다. 모델 매니저로 필터링 로직을 이전함으로써 쉽게 성능을 향상시킬 수 있습니다.

```
<!-- ❌ 나쁜 예 - 템플릿에서 필터링 -->
{% for post in all_posts %}
    {% if post.is_published and post.author == user %}
        <h2>{{ post.title }}</h2>
    {% endif %}
{% endfor %}
```

```
# ✅ 좋은 예 - 뷰에서 필터링
def my_posts(request):
    posts = Post.objects.filter(
        is_published=True,
        author=request.user
    )
    return render(request, 'posts/my_list.html', {'posts': posts})
```

### 주의 사항 3: 템플릿상에서 복잡하게 얽힌 쿼리들

Django 템플릿에서 로직을 구현하는 것을 자제해야 하며 뷰에서도 불필요한 쿼리를 호출하지 않아야 합니다. 또한, **템플릿으로부터 호출된 메서드에 너무 많은 쿼리 로직을 넣는 것도 자제**해야 합니다.

```
<!-- ❌ 나쁜 예 - 템플릿에서 복잡한 쿼리 -->
{% for post in posts %}
    <h2>{{ post.title }}</h2>
    {% for tag in post.tags.all %}  <!-- 각 post마다 쿼리 발생 -->
        <span>{{ tag.name }}</span>
    {% endfor %}
{% endfor %}
```

```
# ✅ 좋은 예 - prefetch_related 사용
def post_list(request):
    posts = Post.objects.prefetch_related('tags')
    return render(request, 'posts/list.html', {'posts': posts})
```

### 주의 사항 4: 템플릿에서 생기는 CPU 부하

많은 양의 이미지나 데이터를 처리하는 프로젝트에서는 사이트 성능을 올리기 위해 **이미지 프로세싱 작업을 템플릿에서 분리**해 뷰나 모델, 헬퍼 메서드, 또는 Celery 등을 이용한 비동기 메시지 큐 시스템으로 처리해야 합니다.

```
# ❌ 나쁜 예 - 템플릿에서 이미지 처리
# {{ post.image|resize:"800x600" }}  # 요청마다 이미지 리사이즈

# ✅ 좋은 예 - 모델에서 미리 처리
from django.db import models
from PIL import Image

class Post(models.Model):
    image = models.ImageField(upload_to='posts/')
    thumbnail = models.ImageField(upload_to='thumbnails/', blank=True)

    def save(self, *args, **kwargs):
        super().save(*args, **kwargs)
        if self.image:
            # 저장 시 썸네일 생성
            img = Image.open(self.image.path)
            img.thumbnail((800, 600))
            img.save(self.thumbnail.path)
```

### 주의 사항 5: 템플릿에서 숨겨진 REST API 호출

템플릿에서 객체 메서드를 호출함으로써 로딩 시간이 늘어나기도 합니다. 이는 **REST API를 호출하는 메서드에서도 마찬가지**입니다. 그렇다면 REST API를 어디서 호출해야 할까?

-   **자바스크립트 코드**: 페이지 내용이 다 제공된 다음에 클라이언트 브라우저에서 자바스크립트로 처리합니다
-   **뷰의 Python 코드**: 느린 프로세스를 메시지 큐, 스레드, 멀티프로세스 등의 방법으로 처리

```
<!-- ✅ 좋은 예 - JavaScript로 API 호출 -->
<div id="weather-widget">로딩 중...</div>

<script>
fetch('/api/weather/')
    .then(response => response.json())
    .then(data => {
        document.getElementById('weather-widget').textContent = data.temperature;
    });
</script>
```

## HTML 코드를 정돈하는 데 너무 신경 쓰지 마라

브라우저 인스펙터가 HTML 코드 자체를 보기 좋게 다듬어줄 것이기 때문에 Django 템플릿으로부터 렌더링된 HTML을 잘 정리되어 보이게 하려고 고생하는 것은 **시간 낭비**일 뿐 아니라 나중에 코드를 읽는 사람에게 혼란만 가중시킵니다.

```
<!-- 가독성 좋은 템플릿 코드가 우선 -->
{% extends "base.html" %}
{% block content %}
    {% for post in posts %}
        <article>
            <h2>{{ post.title }}</h2>
            <p>{{ post.content }}</p>
        </article>
    {% endfor %}
{% endblock %}
```

## 템플릿의 상속

### base.html에서 사용한 템플릿 태그들

| 템플릿 태그 | 목적 |
| --- | --- |
| `{% load %}` | 정적 파일의 내장 템플릿 태그 라이브러리를 로드 |
| `{% block %}` | base.html이 부모가 되는 템플릿이기 때문에 해당 블록을 자식 템플릿에서 이용할 수 있게 함 |
| `{% static %}` | 정적 미디어 서버에 이용될 정적 미디어 인자 |

```
<!-- base.html -->
{% load static %}
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Site{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
    {% block extra_css %}{% endblock %}
</head>
<body>
    {% block content %}{% endblock %}
    <script src="{% static 'js/main.js' %}"></script>
    {% block extra_js %}{% endblock %}
</body>
</html>
```

### 자식 템플릿에서 사용한 템플릿 객체들

| 템플릿 객체 | 목적 |
| --- | --- |
| `{% extends %}` | Django에게 해당 템플릿이 base.html을 상속, 확장한 것임을 알려줌 |
| `{% block %}` | 자식 템플릿이기 때문에 block은 base.html의 내용으로 오버라이드됨 |
| `{{ block.super }}` | 자식 템플릿 블록에 위치하여 부모의 내용이 블록 안에 그대로 존재하게 함 |

```
<!-- about.html -->
{% extends "base.html" %}

{% block title %}About - {{ block.super }}{% endblock %}

{% block extra_css %}
    <link rel="stylesheet" href="{% static 'css/about.css' %}">
{% endblock %}

{% block content %}
    <h1>About Us</h1>
    <p>회사 소개 내용</p>
{% endblock %}
```

## 강력한 기능의 block.super

`{{ block.super }}`를 이용한다면 **추가적인 베이스 템플릿 파일이 필요하지 않습니다**.

```
<!-- base.html -->
{% block extra_js %}
    <script src="{% static 'js/analytics.js' %}"></script>
{% endblock %}

<!-- child.html -->
{% extends "base.html" %}
{% block extra_js %}
    {{ block.super }}  <!-- 부모의 analytics.js 포함 -->
    <script src="{% static 'js/custom.js' %}"></script>
{% endblock %}
```

## 그 외 유용한 사항들

### Python 코드와 스타일을 긴밀하게 연결하지 않도록 한다

**CSS와 JavaScript를 이용하여 모든 템플릿 렌더링 스타일을 구현하는 것을 목표**로 해야 합니다.

```
<!-- ❌ 나쁜 예 - Python 로직에 의존 -->
<div class="{% if user.is_premium %}premium{% else %}basic{% endif %}">

<!-- ✅ 좋은 예 - CSS 클래스로 처리 -->
<div class="user-card" data-membership="{{ user.membership_type }}">
```

### 일반적인 관례

-   템플릿 이름, 블록 이름, 그리고 템플릿에서 이용되는 여러 이름에서 **대시(-)보다는 밑줄(\_)을 선호**합니다
-   직관적이고 명확한 블록 이름을 이용합니다. `{% block javascripts %}`가 좋은 예입니다
-   블록의 맺음으로 블록 이름을 포함하게 합니다. `{% endblock %}`이 아닌 `{% endblock javascripts %}`를 이용하도록 합니다

```
{% block page_title %}
    <h1>Welcome</h1>
{% endblock page_title %}

{% block main_content %}
    <p>Content here</p>
{% endblock main_content %}
```

-   다른 템플릿으로부터 호출되는 템플릿의 이름은 **밑줄(\_)을 접두어**로 가집니다

```
<!-- _header.html -->
<header>Header content</header>

<!-- main.html -->
{% include "_header.html" %}
```

### 콘텍스트 객체에 모델의 이름을 붙여 이용하기

제네릭 디스플레이 클래스 기반 뷰를 이용한다면 템플릿에서 `{{ object_list }}`와 `{{ object }}`를 이용하거나 그냥 모델의 이름을 따서 그대로 이용할 수 있습니다.

```
class PostListView(ListView):
    model = Post
    # context_object_name을 지정하지 않으면 'object_list'와 'post_list' 둘 다 사용 가능
    context_object_name = 'posts'  # 명시적으로 지정 권장
```

```
<!-- 명시적 이름 사용 (권장) -->
{% for post in posts %}
    <h2>{{ post.title }}</h2>
{% endfor %}
```

### 하드코딩된 경로 대신 URL 이름 사용하기

하드 코딩된 URL의 문제점은 사이트의 URL 패턴이 변경되면 사이트 전반에 걸친 모든 URL이 변경되어야 한다는 점입니다. 따라서 **하드 코딩된 URL 대신 URLConf 파일 안의 이름을 참조하는 `{% url %}` 태그를 이용**합니다.

```
<!-- ❌ 나쁜 예 - 하드코딩 -->
<a href="/posts/123/">글 보기</a>

<!-- ✅ 좋은 예 - URL 이름 사용 -->
<a href="{% url 'post_detail' pk=post.pk %}">글 보기</a>

<!-- 네임스페이스 사용 -->
<a href="{% url 'blog:post_detail' pk=post.pk %}">글 보기</a>
```

```
# urls.py
urlpatterns = [
    path('posts/<int:pk>/', views.PostDetailView.as_view(), name='post_detail'),
]
```

### 복잡한 템플릿 디버깅하기

템플릿들이 복잡해져서 어디서 변수들이 문제를 일으키고 있는지 찾아내기 어려울 정도가 되면 **TEMPLATES 세팅의 OPTIONS에서 string\_if\_invalid 옵션을 설정**함으로써 좀 더 자세한 에러 메시지를 받아 볼 수 있습니다.

```
# settings.py (개발 환경에서만 사용)
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'string_if_invalid': 'INVALID_VARIABLE: %s',  # 개발 환경에서만
        },
    },
]
```

## 요약

Django 템플릿은 프레젠테이션 로직만 담당해야 하며, 비즈니스 로직과 데이터 처리는 뷰와 모델에서 처리하는 것이 성능과 유지보수성 측면에서 중요합니다.