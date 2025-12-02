# Django 내장 템플릿 태그 완전 가이드

Django 개발자라면 반드시 알아야 할 모든 내장 템플릿 태그를 실무 예제와 함께 정리했습니다.

## 1. {% autoescape %}
**역할**: HTML 자동 이스케이프를 켜거나 끄는 태그
```django
{% autoescape on %}
    {{ user_input }}  <!-- HTML 이스케이프됨 -->
{% endautoescape %}

{% autoescape off %}
    {{ safe_html }}   <!-- HTML 이스케이프 안됨 -->
{% endautoescape %}
```

## 2. {% block %}
**역할**: 템플릿 상속에서 자식 템플릿이 오버라이드할 수 있는 블록 정의
```django
<!-- base.html -->
<title>{% block title %}기본 제목{% endblock %}</title>

<!-- child.html -->
{% extends "base.html" %}
{% block title %}새로운 제목{% endblock %}
```

## 3. {% comment %}
**역할**: 템플릿에서 주석 처리 (렌더링되지 않음)
```django
<p>보이는 텍스트</p>
{% comment "설명" %}
    <p>이 부분은 렌더링되지 않습니다</p>
    {{ variable }}
{% endcomment %}
```

## 4. {% csrf_token %}
**역할**: CSRF 보안 토큰을 폼에 삽입
```django
<form method="post">
    {% csrf_token %}
    <input type="text" name="username">
    <button type="submit">제출</button>
</form>
```

## 5. {% cycle %}
**역할**: 값들을 순환하면서 출력 (테이블 행 색상 변경 등에 유용)
```django
{% for item in items %}
    <tr class="{% cycle 'odd' 'even' %}">
        <td>{{ item }}</td>
    </tr>
{% endfor %}

<!-- 변수 저장 -->
{% cycle 'red' 'blue' as colors %}
```

## 6. {% debug %}
**역할**: 현재 컨텍스트와 임포트된 모듈의 디버그 정보 출력 (DEBUG=True일 때만)
```django
{% debug %}
<!-- 컨텍스트 변수들과 모듈 정보가 출력됨 -->
```

## 7. {% extends %}
**역할**: 부모 템플릿을 상속받음
```django
{% extends "base.html" %}
{% extends variable_template_name %}
{% extends "./relative/path.html" %}
```

## 8. {% filter %}
**역할**: 블록 전체에 필터를 적용
```django
{% filter force_escape|lower %}
    이 텍스트는 HTML 이스케이프되고 소문자로 변환됩니다.
    <strong>태그도 이스케이프됩니다</strong>
{% endfilter %}
```

## 9. {% firstof %}
**역할**: 첫 번째로 참인 값을 출력 (여러 변수 중 첫 번째 유효한 값)
```django
{% firstof var1 var2 var3 "기본값" %}

<!-- 변수에 저장 -->
{% firstof var1 var2 var3 as result %}
```

## 10. {% for %}
**역할**: 리스트나 쿼리셋을 반복 처리
```django
<ul>
{% for athlete in athlete_list %}
    <li>{{ athlete.name }} ({{ forloop.counter }}번째)</li>
{% empty %}
    <li>선수가 없습니다.</li>
{% endfor %}
</ul>

<!-- 딕셔너리 반복 -->
{% for key, value in data.items %}
    {{ key }}: {{ value }}
{% endfor %}

<!-- 역순 반복 -->
{% for item in items reversed %}
    {{ item }}
{% endfor %}
```

## 11. {% if %}
**역할**: 조건문 처리
```django
{% if user.is_authenticated %}
    <p>환영합니다, {{ user.username }}님!</p>
{% elif user.is_anonymous %}
    <p>로그인해주세요.</p>
{% else %}
    <p>알 수 없는 상태입니다.</p>
{% endif %}

<!-- 논리 연산자 -->
{% if athlete_list and coach_list %}
    <p>선수와 코치가 모두 있습니다.</p>
{% endif %}

<!-- 비교 연산자 -->
{% if age >= 18 %}
    <p>성인입니다.</p>
{% endif %}

<!-- in 연산자 -->
{% if "admin" in user.groups %}
    <p>관리자입니다.</p>
{% endif %}
```

## 12. {% ifchanged %}
**역할**: 반복문에서 값이 변경되었을 때만 출력
```django
{% for date in days %}
    {% ifchanged date.date %}
        <h3>{{ date.date }}</h3>
    {% endifchanged %}
    <p>{{ date.hour }}시</p>
{% endfor %}

<!-- else 절 사용 -->
{% for item in items %}
    {% ifchanged item.category %}
        <h2>{{ item.category }}</h2>
    {% else %}
        <!-- 카테고리가 변경되지 않았을 때 -->
    {% endifchanged %}
{% endfor %}
```

## 13. {% include %}
**역할**: 다른 템플릿을 현재 위치에 포함
```django
{% include "header.html" %}
{% include template_name %}

<!-- 추가 컨텍스트 전달 -->
{% include "sidebar.html" with user="김철수" %}

<!-- 특정 변수만 전달 -->
{% include "sidebar.html" with user="김철수" only %}
```

## 14. {% load %}
**역할**: 커스텀 태그나 필터 라이브러리를 로드
```django
{% load static %}
{% load humanize %}
{% load custom_tags %}

<!-- 특정 태그/필터만 로드 -->
{% load static from staticfiles %}
{% load slugify from django.template.defaultfilters %}
```

## 15. {% lorem %}
**역할**: 더미 "Lorem ipsum" 텍스트 생성
```django
{% lorem %}                    <!-- 기본 Lorem ipsum 문단 -->
{% lorem 3 p %}               <!-- 3개 문단을 <p> 태그로 감싸서 -->
{% lorem 5 w %}               <!-- 5개 단어 -->
{% lorem 2 w random %}        <!-- 2개 랜덤 단어 -->
```

## 16. {% now %}
**역할**: 현재 날짜/시간을 지정된 형식으로 출력
```django
현재 시간: {% now "Y-m-d H:i:s" %}
{% now "jS F Y" %}            <!-- 1st January 2024 -->

<!-- 변수에 저장 -->
{% now "Y" as current_year %}
Copyright {{ current_year }}

<!-- 이스케이프 -->
{% now "jS \o\f F" %}         <!-- 1st of January -->
```

## 17. {% querystring %}
**역할**: URL 쿼리스트링을 생성하고 수정
```django
<!-- 현재 쿼리스트링 유지 -->
{% querystring %}

<!-- 파라미터 추가/수정 -->
{% querystring size="M" color="red" %}

<!-- 파라미터 제거 -->
{% querystring color=None %}

<!-- 변수에 저장 -->
{% querystring page=page.next_page_number as next_url %}

<!-- 페이지네이션 예제 -->
<a href="{% querystring page=page.next_page_number %}">다음 페이지</a>
```

## 18. {% regroup %}
**역할**: 리스트를 공통 속성으로 그룹화
```django
{% regroup cities by country as country_list %}
<ul>
{% for country in country_list %}
    <li>{{ country.grouper }}
        <ul>
        {% for city in country.list %}
            <li>{{ city.name }}: {{ city.population }}</li>
        {% endfor %}
        </ul>
    </li>
{% endfor %}
</ul>

<!-- 정렬 후 그룹화 -->
{% regroup cities|dictsort:"country" by country as country_list %}
```

## 19. {% resetcycle %}
**역할**: cycle 태그를 첫 번째 값으로 리셋
```django
{% for coach in coaches %}
    <h1>{{ coach.name }}</h1>
    {% for athlete in coach.athletes %}
        <p class="{% cycle 'odd' 'even' %}">{{ athlete.name }}</p>
    {% endfor %}
    {% resetcycle %}  <!-- 다음 코치의 선수들은 'odd'부터 시작 -->
{% endfor %}

<!-- 특정 cycle 리셋 -->
{% resetcycle mycycle %}
```

## 20. {% spaceless %}
**역할**: HTML 태그 사이의 공백 제거 (태그와 텍스트 사이 공백은 유지)
```django
{% spaceless %}
    <p>
        <a href="/foo/">링크</a>
    </p>
{% endspaceless %}
<!-- 결과: <p><a href="/foo/">링크</a></p> -->
```

## 21. {% templatetag %}
**역할**: 템플릿 태그 문자들을 출력 (템플릿 문법 문자를 표시할 때)
```django
{% templatetag openblock %} = {%
{% templatetag closeblock %} = %}
{% templatetag openvariable %} = {{
{% templatetag closevariable %} = }}
{% templatetag openbrace %} = {
{% templatetag closebrace %} = }
{% templatetag opencomment %} = {#
{% templatetag closecomment %} = #}

<!-- 사용 예 -->
템플릿 변수는 {% templatetag openvariable %} variable {% templatetag closevariable %}로 표시합니다.
```

## 22. {% url %}
**역할**: URL 패턴 이름을 사용해 URL 생성 (하드코딩 방지)
```django
<!-- 기본 사용법 -->
<a href="{% url 'article_detail' article.id %}">기사 보기</a>

<!-- 키워드 인자 -->
<a href="{% url 'article_detail' id=article.id slug=article.slug %}">기사 보기</a>

<!-- 변수에 저장 -->
{% url 'article_detail' article.id as article_url %}
<a href="{{ article_url }}">기사 보기</a>

<!-- 네임스페이스 사용 -->
{% url 'blog:article_detail' article.id %}

<!-- 옵셔널 URL (존재하지 않아도 에러 안남) -->
{% url 'optional_view' as optional_url %}
{% if optional_url %}
    <a href="{{ optional_url }}">옵셔널 링크</a>
{% endif %}
```

## 23. {% verbatim %}
**역할**: 템플릿 엔진이 렌더링하지 않도록 보호 (JavaScript 템플릿과 충돌 방지)
```django
{% verbatim %}
    <script>
        var template = '{{name}}님 안녕하세요!';  <!-- Django가 렌더링하지 않음 -->
    </script>
{% endverbatim %}

<!-- 커스텀 종료 태그 -->
{% verbatim myblock %}
    {% verbatim %}과 {% endverbatim %}를 그대로 출력할 수 있습니다.
{% endverbatim myblock %}
```

## 24. {% widthratio %}
**역할**: 비율을 계산해서 차트나 진행바 만들 때 사용
```django
<!-- 진행바 너비 계산 -->
<div style="width: {% widthratio progress 100 200 %}px; background: blue;">
    <!-- progress가 75이고 최대값이 100이면, 150px 너비 -->
</div>

<!-- 변수에 저장 -->
{% widthratio this_value max_value 100 as percentage %}
<div style="width: {{ percentage }}%">{{ percentage }}% 완료</div>
```

## 25. {% with %}
**역할**: 복잡한 변수나 메소드 호출을 간단한 이름으로 캐싱
```django
{% with total=business.employees.count %}
    {{ total }}명의 직원이 있습니다.
    {% if total > 100 %}
        <p>대기업입니다!</p>
    {% endif %}
{% endwith %}

<!-- 여러 변수 할당 -->
{% with alpha=1 beta=2 gamma=3 %}
    {{ alpha }} + {{ beta }} = {{ alpha|add:beta }}
{% endwith %}

<!-- 구버전 문법 (여전히 지원됨) -->
{% with business.employees.count as total %}
    직원 수: {{ total }}
{% endwith %}
```

## 주요 활용 팁

### 1. 반복문에서 유용한 forloop 변수들
```django
{% for item in items %}
    {% if forloop.first %}<ul>{% endif %}
    
    <li class="{% if forloop.counter0|divisibleby:2 %}even{% else %}odd{% endif %}">
        {{ forloop.counter }}. {{ item }}
        ({{ forloop.revcounter }}개 남음)
    </li>
    
    {% if forloop.last %}</ul>{% endif %}
{% endfor %}
```

### 2. 조건문 고급 사용법
```django
{% if messages|length >= 100 %}
    <p>메시지가 너무 많습니다!</p>
{% elif not messages %}
    <p>메시지가 없습니다.</p>
{% endif %}
```

### 3. 템플릿 상속 패턴
```django
<!-- base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}기본 제목{% endblock %}</title>
    {% block extra_css %}{% endblock %}
</head>
<body>
    {% block content %}{% endblock %}
    {% block extra_js %}{% endblock %}
</body>
</html>
```

이 25개의 내장 태그들을 마스터하면 Django 템플릿을 효과적으로 활용할 수 있습니다!


## 자주 실수하는 부분들

### 1. `{% csrf_token %}` 빼먹기
```django
<!-- ❌ 틀린 예 - 403 에러 발생 -->
<form method="post">
    <input type="text" name="title">
    <button type="submit">제출</button>
</form>

<!-- ✅ 올바른 예 -->
<form method="post">
    {% csrf_token %}
    <input type="text" name="title">
    <button type="submit">제출</button>
</form>
```

### 2. `{% url %}` 태그에서 따옴표 빼먹기
```django
<!-- ❌ 틀린 예 - URL 패턴명을 변수로 인식 -->
<a href="{% url article_detail article.id %}">기사 보기</a>

<!-- ✅ 올바른 예 -->
<a href="{% url 'article_detail' article.id %}">기사 보기</a>
```

### 3. `{% for %}` 태그에서 empty 절 사용 안하기
```django
<!-- ❌ 데이터가 없을 때 처리 안함 -->
<ul>
{% for item in items %}
    <li>{{ item }}</li>
{% endfor %}
</ul>

<!-- ✅ 빈 상태도 고려한 템플릿 -->
<ul>
{% for item in items %}
    <li>{{ item }}</li>
{% empty %}
    <li>항목이 없습니다.</li>
{% endfor %}
</ul>
```


## 🔧 성능 최적화 팁

### 1. `{% with %}` 태그로 데이터베이스 쿼리 최적화
```django
<!-- ❌ 비효율적 - 같은 쿼리를 여러 번 실행 -->
<p>댓글 수: {{ article.comments.count }}</p>
{% if article.comments.count > 0 %}
    <p>최신 댓글: {{ article.comments.first.content }}</p>
{% endif %}

<!-- ✅ 효율적 - 쿼리 결과를 캐시 -->
{% with comment_count=article.comments.count %}
    <p>댓글 수: {{ comment_count }}</p>
    {% if comment_count > 0 %}
        <p>최신 댓글: {{ article.comments.first.content }}</p>
    {% endif %}
{% endwith %}
```

### 2. `{% include %}` vs `{% extends %}` 선택하기
- **`{% include %}`**: 재사용 가능한 작은 컴포넌트 (헤더, 푸터, 카드 등)
- **`{% extends %}`**: 페이지 전체 레이아웃 상속

### 3. `{% cycle %}` 태그로 CSS 클래스 관리
```django
<!-- 테이블 행의 배경색을 번갈아가며 표시 -->
<table>
{% for user in users %}
    <tr class="{% cycle 'row-even' 'row-odd' %}">
        <td>{{ user.username }}</td>
        <td>{{ user.email }}</td>
    </tr>
{% endfor %}
</table>
```

## 마무리

Django 템플릿 태그는 백엔드 개발자도 프론트엔드를 다룰 수 있게 해주는 강력한 도구입니다. 특히 `{% if %}`, `{% for %}`, `{% url %}`, `{% csrf_token %}`은 거의 모든 프로젝트에서 사용하니 우선적으로 사용법을 익히는 것이 좋아보입니다.
