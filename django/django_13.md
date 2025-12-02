# 템플릿 태그와 필터

Two Scoops of Django 14장 "템플릿 태그와 필터"를 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

Django는 다음 특성을 가지는 수십 가지 기본 템플릿 태그와 필터를 제공합니다:

- 모든 기본 템플릿 태그와 필터의 이름은 **명확하고 직관적**이어야 한다
- 모든 기본 템플릿 태그와 필터는 각각 **한 가지 목적만을 수행**한다
- 기본 템플릿 태그와 필터는 **영속 데이터에 변형을 가하지 않는다**

## 필터는 함수다

필터는 **하나 또는 두 개의 인자를 받는 함수**이지, 개발자에게 Django 템플릿의 작동을 통제하는 기능을 제공하지는 않습니다. 기본적으로 Django 템플릿 안에서 Python을 이용할 수 있게 해주는 데코레이터를 가진 함수일 뿐입니다.

```python
# flavors/templatetags/flavors_tags.py
from django import template

register = template.Library()

# 간단한 필터 예시
@register.filter
def add_won(value):
    """숫자에 '원' 단위 추가"""
    return f"{value:,}원"

@register.filter
def multiply(value, arg):
    """두 개의 인자를 받는 필터"""
    return value * arg
```

```html
<!-- 템플릿에서 사용 -->
{% load flavors_tags %}
<p>가격: {{ price|add_won }}</p>  <!-- 10000원 -->
<p>합계: {{ quantity|multiply:price|add_won }}</p>  <!-- 50000원 -->
```

### 필터와 코드의 재사용

필터는 Python 함수이기 때문에 정말 단순한 로직이 아니라면 전부 `utils.py` 모듈 같은 유틸리티 함수로 옮길 수 있습니다. 이렇게 유틸리티로 필터를 몰아넣음으로써 **코드에 좀 더 집중할 수 있고 테스트를 쉽게** 할 뿐 아니라 import 문도 훨씬 적어집니다.

```python
# flavors/utils.py
def format_currency(value):
    """통화 포맷팅 로직"""
    if value is None:
        return "0원"
    return f"{value:,}원"

def calculate_discount(price, discount_rate):
    """할인가 계산 로직"""
    return price * (1 - discount_rate / 100)

# flavors/templatetags/flavors_tags.py
from django import template
from ..utils import format_currency, calculate_discount

register = template.Library()

@register.filter
def currency(value):
    """유틸리티 함수를 필터로 래핑"""
    return format_currency(value)

@register.filter
def discount(price, rate):
    return format_currency(calculate_discount(price, rate))
```

```python
# tests.py - 유틸리티 함수는 테스트하기 쉬움
from flavors.utils import format_currency, calculate_discount

def test_format_currency():
    assert format_currency(10000) == "10,000원"
    assert format_currency(None) == "0원"

def test_calculate_discount():
    assert calculate_discount(10000, 10) == 9000
```

### 언제 필터를 작성해야 하는가

필터는 **데이터의 외형을 수정하는 데 매우 유용**하며 이러한 필터들은 REST API와 다른 출력 포맷에서 손쉽게 재사용할 수 있습니다. 두 개의 인자만 받을 수 있는 기능적 제약 때문에 필터를 복잡하게 이용하기가 매우 어렵습니다.

```python
# ✅ 필터가 적합한 경우
@register.filter
def truncate_chars(value, length):
    """문자열 자르기"""
    if len(value) <= length:
        return value
    return value[:length] + '...'

# ❌ 필터로 부적절한 경우 (복잡한 로직)
@register.filter
def complex_calculation(value, args):
    # 여러 인자가 필요한 복잡한 계산
    # 이런 경우는 템플릿 태그나 뷰에서 처리해야 함
    pass
```

## 커스텀 템플릿 태그

템플릿 태그는 개발자 스스로가 잘 조절하여 이용한다면 상당히 유용한 도구입니다.

```python
# flavors/templatetags/flavors_tags.py
from django import template

register = template.Library()

# Simple tag - 값을 반환
@register.simple_tag
def current_time(format_string):
    from datetime import datetime
    return datetime.now().strftime(format_string)

# Inclusion tag - 템플릿을 렌더링
@register.inclusion_tag('flavors/_flavor_list.html')
def show_flavors(count=5):
    from flavors.models import Flavor
    flavors = Flavor.objects.all()[:count]
    return {'flavors': flavors}
```

```html
<!-- 템플릿에서 사용 -->
{% load flavors_tags %}
<p>현재 시간: {% current_time "%Y-%m-%d %H:%M" %}</p>
{% show_flavors 3 %}

<!-- flavors/_flavor_list.html -->
<ul>
{% for flavor in flavors %}
    <li>{{ flavor.name }}</li>
{% endfor %}
</ul>
```

### 템플릿 태그들은 디버깅하기가 쉽지 않다

복잡한 템플릿 태그들은 디버깅하기가 쉽지 않습니다. 템플릿 태그에서 문제를 발견하고 수정하기 까다로운 경우에는 **로그와 테스트를 통해 도움을 받을 수 있습니다**.

```python
# 로깅 활용
import logging
from django import template

logger = logging.getLogger(__name__)
register = template.Library()

@register.simple_tag
def complex_tag(value):
    logger.debug(f"complex_tag called with value: {value}")
    try:
        result = some_complex_operation(value)
        logger.debug(f"complex_tag result: {result}")
        return result
    except Exception as e:
        logger.error(f"Error in complex_tag: {e}")
        return ""
```

### 템플릿 태그들은 재사용하기가 쉽지 않다

REST API, RSS 피드 또는 PDF/CSV 생성을 위한 출력 포맷 등을 동일 템플릿 태그를 이용하여 처리하기란 쉽지 않은 일입니다. **여러 종류의 포맷이 필요하다면 다른 뷰에서도 쉽게 접근할 수 있도록 템플릿 태그 안의 모든 로직을 utils.py로 옮기는 것을 고려**해봐야 합니다.

```python
# flavors/utils.py
def get_top_flavors(count=5):
    """재사용 가능한 비즈니스 로직"""
    from flavors.models import Flavor
    return Flavor.objects.filter(
        is_active=True
    ).order_by('-rating')[:count]

# flavors/templatetags/flavors_tags.py
from django import template
from ..utils import get_top_flavors

register = template.Library()

@register.inclusion_tag('flavors/_top_list.html')
def show_top_flavors(count=5):
    return {'flavors': get_top_flavors(count)}

# flavors/views.py - 동일 로직을 뷰에서도 사용
from .utils import get_top_flavors

def api_top_flavors(request):
    flavors = get_top_flavors(10)
    return JsonResponse({
        'flavors': [f.name for f in flavors]
    })
```

### 템플릿 태그의 성능 문제

템플릿 태그는 템플릿 태그 안에서 또 다른 템플릿 태그를 로드할 경우 **심각한 성능 문제를 야기**할 수도 있습니다. 커스텀 템플릿 태그가 많은 템플릿을 로드한다면, **로드된 템플릿을 캐시하는 방법을 고려**해볼 수 있습니다.

```python
from django import template
from django.core.cache import cache

register = template.Library()

@register.inclusion_tag('flavors/_cached_widget.html')
def cached_widget():
    cache_key = 'widget_data'
    data = cache.get(cache_key)
    
    if data is None:
        # 무거운 연산
        data = expensive_operation()
        cache.set(cache_key, data, 60 * 15)  # 15분 캐시
    
    return {'data': data}
```

### 언제 템플릿 태그를 이용할 것인가

새로운 템플릿 태그를 추가하기 앞서 다음 사항을 반드시 고려해야 합니다:

- **데이터를 읽고 쓰는 작업**을 할 것이라면 모델이나 객체 메서드(Object Method)가 더 나은 장소일 것입니다
- 프로젝트 전반에서 일관된 작명법을 이용하고 있기 때문에 **추상화 기반의 클래스 모델을 core.models 모듈에 추가**할 수 있습니다
- 프로젝트의 추상화 기반 클래스 모델에서 어떤 메서드나 프로퍼티가 우리가 작성하려는 커스텀 템플릿 태그와 같은 일을 하는가?

**새로운 템플릿 태그를 작성하기 좋은 시기는 HTML을 렌더링하는 작업이 필요할 때**입니다.

```python
# ✅ 템플릿 태그가 적합한 경우 - HTML 렌더링
@register.inclusion_tag('common/_breadcrumb.html')
def breadcrumb(items):
    """빵부스러기 네비게이션 HTML 생성"""
    return {'items': items}

# ❌ 템플릿 태그로 부적절 - 모델 메서드가 더 적합
# 이런 경우는 모델에 메서드로 추가
class Article(models.Model):
    def get_read_time(self):
        words = len(self.content.split())
        return words // 200  # 분당 200단어 기준
```

## 템플릿 태그 라이브러리 이름 짓기

템플릿 태그 라이브러리 작명 관례는 **`<app_name>_tags.py`** 입니다.

```
✅ 좋은 예시
flavors/
    └── templatetags/
        ├── __init__.py
        └── flavors_tags.py  # 앱 이름 + _tags

❌ 나쁜 예시
flavors/
    └── templatetags/
        ├── __init__.py
        └── flavors.py  # 앱 이름과 동일 (문제 발생 가능)
```

템플릿 태그 라이브러리 이름을 앱과 똑같이 짓는다면 **Django가 템플릿 태그를 로드하는 방식으로 인해 문제가 생길 수 있습니다**.

## 템플릿 태그 모듈 로드하기

템플릿에서 `{% extends "base.html" %}` (또는 base.html 이외의 부모 템플릿) **바로 다음에 템플릿 태그가 로드될 수 있도록** 합니다.

```html
<!-- ✅ 좋은 예 -->
{% extends "base.html" %}
{% load flavors_tags %}
{% load static %}

{% block content %}
    {% show_top_flavors 5 %}
{% endblock %}

<!-- ❌ 나쁜 예 - 로드 순서가 뒤섞임 -->
{% extends "base.html" %}
{% block content %}
    {% load flavors_tags %}
    {% show_top_flavors 5 %}
{% endblock %}
```

### 반드시 피해야 할 안티 패턴 한 가지

```python
# ❌ 절대 사용하지 말 것
from django import template
template.add_to_builtins("flavors.templatetags.flavors_tags")
```

해당 안티패턴은 DRY(Don't Repeat Yourself) 하는 해결책을 제시하는 척하면서 앞에서 이용된 명시적인 로드 메서드를 암시적인 방향으로 변경합니다. 그리고 반복 작업을 피한다는 장점은 다음 문제점으로 인해 무의미해집니다:

#### 문제점 1: 성능 오버헤드
템플릿 태그 라이브러리를 `django.template.Template`에 의해 로드된 **모든 템플릿에 로드**함으로써 오버헤드를 일으킵니다. 이는 모든 상속된 템플릿, 템플릿 `{% include %}`, inclusion_tag 등에 영향을 미칩니다.

```python
# ❌ 모든 템플릿에서 불필요하게 로드됨
template.add_to_builtins("flavors.templatetags.flavors_tags")

# 이렇게 되면:
# - base.html 로드 시 → flavors_tags 로드
# - about.html 로드 시 → flavors_tags 로드
# - contact.html 로드 시 → flavors_tags 로드
# 필요하지 않은 곳에서도 로드되어 성능 저하
```

#### 문제점 2: 디버깅 어려움
템플릿 태그 라이브러리가 **암시적으로 로드**되기 때문에 디버깅에 상당한 애를 먹게 됩니다.

```python
# 어디서 로드되는지 명확하지 않아 디버깅 어려움
# 템플릿 오류 발생 시 어떤 태그가 문제인지 파악하기 힘듦
```

#### 문제점 3: 관례 위반
`add_to_builtins` 메서드는 관례적으로 이용되지 않습니다. 종종 `__init__` 모듈이나 템플릿 태그 라이브러리에서 발견되기도 하는데 예상하지 못한 문제를 일으킬 소지가 있습니다.

```python
# ✅ 올바른 방법 - 명시적 로드
{% load flavors_tags %}

# 장점:
# - 어떤 태그 라이브러리를 사용하는지 명확
# - 필요한 곳에서만 로드되어 성능 향상
# - 디버깅이 쉬움
```

## 실용적인 템플릿 태그 예시

```python
# common/templatetags/common_tags.py
from django import template
from django.utils.html import format_html

register = template.Library()

@register.simple_tag
def badge(text, style='primary'):
    """부트스트랩 배지 생성"""
    return format_html(
        '<span class="badge badge-{}">{}</span>',
        style, text
    )

@register.inclusion_tag('common/_pagination.html')
def pagination(page_obj):
    """페이지네이션 HTML 생성"""
    return {
        'page_obj': page_obj,
        'page_range': page_obj.paginator.page_range,
    }

@register.simple_tag(takes_context=True)
def query_transform(context, **kwargs):
    """URL 쿼리 파라미터 변경"""
    query = context['request'].GET.copy()
    for k, v in kwargs.items():
        query[k] = v
    return query.urlencode()
```

```html
<!-- 사용 예시 -->
{% load common_tags %}

<h1>{{ post.title }} {% badge post.status 'success' %}</h1>

{% pagination page_obj %}

<a href="?{% query_transform page=2 sort='date' %}">다음 페이지</a>
```


필터와 템플릿 태그는 강력한 도구지만, **단순하고 명확하게 유지**하고 복잡한 로직은 적절한 계층(모델, 뷰, 유틸리티)으로 분리하는 것이 중요합니다.