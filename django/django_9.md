# Django 핵심 템플릿 필터 가이드 🔧

Django 템플릿 태그에 이어 이번에는 **템플릿 필터**를 정리해봤습니다. 필터는 템플릿에서 데이터를 표시하는 방식을 제어하는 강력한 도구인데요, 총 57개나 되는 내장 필터 중에서 자주 쓰이는 핵심 필터들만 골라서 설명드리겠습니다.

## 문자열 처리 필터

### 1. `|lower` / `|upper` / `|title` / `|capfirst`
**역할**: 문자열 대소문자 변환
```django
{{ "Hello World"|lower }}     <!-- hello world -->
{{ "hello world"|upper }}     <!-- HELLO WORLD -->
{{ "hello world"|title }}     <!-- Hello World -->
{{ "hello world"|capfirst }}  <!-- Hello world -->
```

### 2. `|slugify`
**역할**: URL 친화적인 문자열로 변환 (한글, 공백, 특수문자 처리)
```django
{{ "안녕하세요! Django 입니다."|slugify }}  <!-- annyeonghaseyo-django-ibnida -->
{{ "Hello World!"|slugify }}                <!-- hello-world -->
```

### 3. `|truncatechars` / `|truncatewords`
**역할**: 문자열 자르기 (글자 수 / 단어 수 기준)
```django
{{ "긴 텍스트입니다."|truncatechars:5 }}     <!-- 긴 텍스트… -->
{{ "This is a long text"|truncatewords:3 }}  <!-- This is a… -->

<!-- HTML 인식 버전 -->
{{ "<p>HTML 태그가 있는 텍스트</p>"|truncatechars_html:10 }}
```

### 4. `|linebreaks` / `|linebreaksbr`
**역할**: 줄바꿈을 HTML로 변환
```django
{{ "첫 줄\n\n둘째 줄"|linebreaks }}    <!-- <p>첫 줄</p><p>둘째 줄</p> -->
{{ "첫 줄\n둘째 줄"|linebreaksbr }}    <!-- 첫 줄<br>둘째 줄 -->
```

### 5. `|striptags`
**역할**: HTML 태그 제거
```django
{{ "<p>안녕하세요 <strong>Django</strong></p>"|striptags }}
<!-- 안녕하세요 Django -->
```

## 수치 처리 필터

### 6. `|add`
**역할**: 숫자 더하기 또는 문자열/리스트 연결
```django
{{ 5|add:3 }}                    <!-- 8 -->
{{ "Hello"|add:" World" }}       <!-- Hello World -->
{{ list1|add:list2 }}            <!-- 리스트 합치기 -->
```

### 7. `|floatformat`
**역할**: 소수점 형식 지정
```django
{{ 3.14159|floatformat:2 }}      <!-- 3.14 -->
{{ 3.0|floatformat }}            <!-- 3 -->
{{ 3.14159|floatformat:-2 }}     <!-- 3.14 (소수점 있을 때만) -->
{{ 1234.56|floatformat:"2g" }}   <!-- 1,234.56 (천단위 구분) -->
```

### 8. `|filesizeformat`
**역할**: 파일 크기를 사람이 읽기 쉬운 형태로 변환
```django
{{ 1024|filesizeformat }}       <!-- 1.0 KB -->
{{ 1048576|filesizeformat }}    <!-- 1.0 MB -->
{{ 123456789|filesizeformat }}  <!-- 117.7 MB -->
```

### 9. `|divisibleby`
**역할**: 나누어떨어지는지 확인 (주로 if문과 함께 사용)
```django
{% if forloop.counter|divisibleby:2 %}
    <tr class="even">
{% else %}
    <tr class="odd">
{% endif %}
```

## 날짜/시간 필터

### 10. `|date`
**역할**: 날짜 형식 지정
```django
{{ now|date:"Y-m-d" }}           <!-- 2024-03-15 -->
{{ now|date:"Y년 m월 d일" }}      <!-- 2024년 03월 15일 -->
{{ now|date:"D d M Y" }}         <!-- Fri 15 Mar 2024 -->
{{ now|date:"SHORT_DATE_FORMAT" }} <!-- 설정된 기본 형식 -->
```

### 11. `|time`
**역할**: 시간 형식 지정
```django
{{ now|time:"H:i" }}             <!-- 14:30 -->
{{ now|time:"g:i A" }}           <!-- 2:30 PM -->
```

### 12. `|timesince` / `|timeuntil`
**역할**: 상대적 시간 표시 (SNS에서 많이 사용)
```django
{{ article.created|timesince }}  <!-- 2시간 전 -->
{{ event.date|timeuntil }}       <!-- 3일 후 -->
```

## 리스트/시퀀스 필터

### 13. `|length`
**역할**: 길이 반환 (리스트, 문자열, 쿼리셋)
```django
{{ "Hello"|length }}             <!-- 5 -->
{{ articles|length }}            <!-- 게시글 개수 -->
```

### 14. `|first` / `|last`
**역할**: 첫 번째/마지막 요소 반환
```django
{{ "Django"|first }}             <!-- D -->
{{ articles|last }}              <!-- 마지막 게시글 -->
```

### 15. `|join`
**역할**: 리스트를 문자열로 합치기 (Python의 join과 동일)
```django
{{ tags|join:", " }}             <!-- tag1, tag2, tag3 -->
{{ names|join:" and " }}         <!-- Alice and Bob and Charlie -->
```

### 16. `|slice`
**역할**: 리스트 슬라이싱
```django
{{ articles|slice:":3" }}        <!-- 처음 3개 -->
{{ articles|slice:"2:5" }}       <!-- 3번째~5번째 -->
```

### 17. `|dictsort`
**역할**: 딕셔너리 리스트 정렬
```django
{{ students|dictsort:"name" }}           <!-- 이름순 정렬 -->
{{ books|dictsort:"author.age" }}        <!-- 작가 나이순 -->
{{ items|dictsortreversed:"price" }}     <!-- 가격 역순 -->
```

### 18. `|random`
**역할**: 리스트에서 랜덤 요소 선택
```django
{{ quotes|random }}              <!-- 명언 중 하나 랜덤 선택 -->
{{ colors|random }}              <!-- 색상 중 하나 랜덤 -->
```

## 보안/HTML 필터

### 19. `|escape` / `|safe`
**역할**: HTML 이스케이프 제어
```django
{{ user_input|escape }}          <!-- HTML 태그 이스케이프 -->
{{ trusted_html|safe }}          <!-- HTML 태그 그대로 출력 -->
```

### 20. `|escapejs`
**역할**: JavaScript 문자열로 안전하게 이스케이프
```django
<script>
    var message = '{{ user_message|escapejs }}';
</script>
```

### 21. `|json_script`
**역할**: JSON을 안전하게 JavaScript로 전달
```django
{{ data|json_script:"my-data" }}
<!-- <script id="my-data" type="application/json">{"key": "value"}</script> -->

<script>
    const data = JSON.parse(document.getElementById('my-data').textContent);
</script>
```

## URL/링크 필터

### 22. `|urlencode`
**역할**: URL 인코딩
```django
{{ search_query|urlencode }}     <!-- 한글, 특수문자 URL 인코딩 -->
```

### 23. `|urlize`
**역할**: 텍스트의 URL을 클릭 가능한 링크로 변환
```django
{{ "Visit https://djangoproject.com"|urlize }}
<!-- Visit <a href="https://djangoproject.com">https://djangoproject.com</a> -->
```

## 조건/기본값 필터

### 24. `|default` / `|default_if_none`
**역할**: 기본값 설정
```django
{{ name|default:"익명" }}          <!-- 값이 없으면 "익명" -->
{{ value|default_if_none:"N/A" }} <!-- None일 때만 "N/A" -->
```

### 25. `|yesno`
**역할**: Boolean 값을 문자열로 변환
```django
{{ is_active|yesno:"활성,비활성" }}      <!-- True면 활성, False면 비활성 -->
{{ status|yesno:"Yes,No,Maybe" }}       <!-- None일 때 Maybe -->
```

### 26. `|pluralize`
**역할**: 복수형 처리 (영어/한국어 모두 유용)
```django
댓글 {{ count }}개{{ count|pluralize }}       <!-- 1개/2개들 -->
You have {{ count }} message{{ count|pluralize }}  <!-- message/messages -->
{{ count }} ox{{ count|pluralize:"en" }}            <!-- ox/oxen -->
```

## 실용적 필터

### 27. `|wordcount`
**역할**: 단어 개수 세기
```django
{{ article.content|wordcount }}  <!-- 500 -->
```

### 28. `|make_list`
**역할**: 문자열이나 숫자를 리스트로 변환
```django
{{ "Django"|make_list }}         <!-- ['D', 'j', 'a', 'n', 'g', 'o'] -->
{{ 123|make_list }}              <!-- ['1', '2', '3'] -->
```

### 29. `|center` / `|ljust` / `|rjust`
**역할**: 텍스트 정렬 (고정폭 텍스트에 유용)
```django
"{{ 'Django'|center:10 }}"      <!-- "  Django  " -->
"{{ 'Django'|ljust:10 }}"       <!-- "Django    " -->
"{{ 'Django'|rjust:10 }}"       <!-- "    Django" -->
```

### 30. `|phone2numeric`
**역할**: 전화번호 문자를 숫자로 변환
```django
{{ "1-800-COLLECT"|phone2numeric }}  <!-- 1-800-2655328 -->
```

## 필터 체이닝 활용법

여러 필터를 연결해서 사용할 수 있습니다:

```django
<!-- 문자열 처리 체이닝 -->
{{ article.title|truncatewords:5|title }}

<!-- 리스트 처리 체이닝 -->
{{ tags|slice:":3"|join:", " }}

<!-- 날짜와 기본값 체이닝 -->
{{ article.published_date|date:"Y-m-d"|default:"미발행" }}

<!-- 안전한 HTML 체이닝 -->
{{ content|linebreaks|safe }}

<!-- 수치 처리 체이닝 -->
{{ price|floatformat:0|add:" 원" }}
```

## 자주 하는 실수들

### 1. `|safe` 필터 남용하기
```django
<!-- ❌ 위험! XSS 공격에 취약 -->
{{ user_input|safe }}

<!-- ✅ 신뢰할 수 있는 데이터만 safe 사용 -->
{{ editor_content|safe }}  <!-- 에디터로 작성된 검증된 HTML -->
```

### 2. `|default`와 `|default_if_none` 헷갈리기
```django
<!-- value가 "" (빈 문자열)인 경우 -->
{{ value|default:"없음" }}          <!-- "없음" 출력 -->
{{ value|default_if_none:"없음" }}  <!-- "" 출력 (빈 문자열은 None이 아님) -->

<!-- value가 None인 경우 -->
{{ value|default:"없음" }}          <!-- "없음" 출력 -->
{{ value|default_if_none:"없음" }}  <!-- "없음" 출력 -->
```

### 3. 필터 체이닝 순서 실수
```django
<!-- ❌ 잘못된 순서 - escape 후에 safe 적용하면 의미 없음 -->
{{ content|escape|safe }}

<!-- ✅ 올바른 순서 - linebreaks 후에 safe 적용 -->
{{ content|linebreaks|safe }}
```

## 성능 최적화 팁

### 1. 템플릿에서 계산 최소화
```django
<!-- ❌ 템플릿에서 복잡한 계산 -->
{% for item in items %}
    <p>할인가: {{ item.price|floatformat:0|add:item.discount|floatformat:0 }}원</p>
{% endfor %}

<!-- ✅ 뷰나 모델에서 미리 계산 -->
{% for item in items %}
    <p>할인가: {{ item.discounted_price|floatformat:0 }}원</p>
{% endfor %}
```

### 2. 필터 체이닝 최적화
```django
<!-- ❌ 불필요한 필터 체이닝 -->
{{ content|striptags|safe|linebreaks }}

<!-- ✅ 필요한 필터만 사용 -->
{{ content|linebreaks }}
```

## 보안 주의사항

### XSS 방지를 위한 필터 사용법
```django
<!-- 사용자 입력 - 항상 이스케이프 -->
<p>{{ user_comment|escape }}</p>

<!-- 관리자가 작성한 HTML - 검증 후 safe 사용 -->
<div class="content">{{ admin_content|safe }}</div>

<!-- JavaScript에 데이터 전달 - json_script 사용 -->
{{ user_data|json_script:"user-data" }}
<script>
    const userData = JSON.parse(document.getElementById('user-data').textContent);
</script>
```

## 반응형 웹을 위한 필터 활용

### 디바이스별 컨텐츠 길이 조절
```django
<!-- 모바일에서는 짧게, 데스크톱에서는 길게 -->
<div class="content">
    <p class="mobile-only">{{ article.content|truncatechars:100 }}</p>
    <p class="desktop-only">{{ article.content|truncatechars:300 }}</p>
</div>

<!-- 파일 크기 표시 -->
<span class="file-size">
    파일 크기: {{ file.size|filesizeformat }}
</span>
```

## 국제화(i18n) 고려사항

```django
<!-- 날짜 형식은 로케일 설정 사용 -->
{{ post.created|date:"SHORT_DATE_FORMAT" }}

<!-- 숫자 형식도 로케일 고려 -->
{{ price|floatformat:2 }}
```

## 고급 활용 팁

### 1. 커스텀 필터와 내장 필터 조합
```django
<!-- 커스텀 필터와 함께 사용 -->
{{ content|remove_html_tags|truncatewords:50 }}
```

### 2. 조건부 필터 적용
```django
{% if user.is_premium %}
    {{ content }}  <!-- 프리미엄 사용자는 전체 내용 -->
{% else %}
    {{ content|truncatewords:100 }}  <!-- 일반 사용자는 제한 -->
{% endif %}
```

### 3. 데이터 타입별 필터 선택
```django
<!-- 문자열 -->
{{ text|truncatechars:50 }}

<!-- 리스트 -->
{{ items|slice:":5" }}

<!-- 숫자 -->
{{ number|floatformat:2 }}

<!-- 날짜 -->
{{ date|timesince }}
```

## 필터 치트시트

| 용도 | 필터 | 예시 |
|------|------|------|
| 빈 값 처리 | `\|default` | `{{ name\|default:"익명" }}` |
| 텍스트 자르기 | `\|truncatechars` | `{{ text\|truncatechars:50 }}` |
| HTML 안전 출력 | `\|safe` | `{{ html_content\|safe }}` |
| 날짜 형식 | `\|date` | `{{ now\|date:"Y-m-d" }}` |
| 상대 시간 | `\|timesince` | `{{ created\|timesince }}` |
| 리스트 합치기 | `\|join` | `{{ tags\|join:", " }}` |
| 개수 표시 | `\|length` | `{{ items\|length }}` |
| 복수형 처리 | `\|pluralize` | `{{ count\|pluralize }}` |

## 마무리

Django 템플릿 필터는 데이터 표시의 유연성을 크게 높여주는 도구입니다. 
특히 **보안 관련 필터**(`|escape`, `|safe`, `|json_script`)는 웹 애플리케이션의 안전성과 직결되니 반드시 올바른 사용법을 숙지해야 합니다..