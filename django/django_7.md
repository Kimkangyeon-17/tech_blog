# Django 템플릿 네임스페이싱

Django를 배우면서 템플릿 구조를 설정할 때 많은 개발자들이 헷갈리는 부분이 있습니다. 바로 `accounts/templates/index.html`과 `accounts/templates/accounts/index.html` 중 어떤 구조를 사용해야 하는지에 대한 것입니다.

결론부터 말하자면, **`accounts/templates/accounts/index.html` 구조를 사용하는 것이 올바른 방법**입니다. 이번 글에서는 그 이유를 자세히 알아보겠습니다.

## Django의 템플릿 검색 메커니즘

Django는 템플릿을 찾을 때 `settings.py`의 `INSTALLED_APPS`에 등록된 순서대로 각 앱의 `templates` 폴더를 검색합니다.

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'accounts',      # 첫 번째로 검색
    'products',      # 두 번째로 검색  
    'orders',        # 세 번째로 검색
]
```

## 문제 상황: 템플릿 이름 충돌

### 잘못된 구조
```
myproject/
├── accounts/
│   └── templates/
│       ├── index.html      # 회원 메인 페이지
│       └── profile.html
├── products/
│   └── templates/
│       ├── index.html      # 상품 메인 페이지
│       └── detail.html
└── orders/
    └── templates/
        ├── index.html      # 주문 메인 페이지
        └── history.html
```

### 발생하는 문제

```python
# products/views.py
def product_main(request):
    # products/templates/index.html을 렌더링하고 싶지만...
    return render(request, 'index.html')
```

위 코드를 실행하면 Django는 **무조건 `accounts/templates/index.html`만 사용**합니다. 왜냐하면 `INSTALLED_APPS`에서 `accounts`가 `products`보다 먼저 등록되어 있기 때문입니다.

결과적으로 모든 앱에서 `render(request, 'index.html')`을 호출해도 계정 관련 페이지만 보이게 됩니다!

## 해결책: 템플릿 네임스페이싱

### 올바른 구조

```
myproject/
├── accounts/
│   └── templates/
│       └── accounts/           # 앱 이름으로 네임스페이스 생성
│           ├── index.html
│           ├── login.html
│           └── profile.html
├── products/
│   └── templates/
│       └── products/           # 앱 이름으로 네임스페이스 생성
│           ├── index.html
│           ├── list.html
│           └── detail.html
└── orders/
    └── templates/
        └── orders/             # 앱 이름으로 네임스페이스 생성
            ├── index.html
            └── history.html
```

### 올바른 사용법

```python
# accounts/views.py
def login_view(request):
    return render(request, 'accounts/login.html')

def profile_view(request):
    return render(request, 'accounts/profile.html')

# products/views.py
def product_list(request):
    return render(request, 'products/list.html')

def product_detail(request, product_id):
    return render(request, 'products/detail.html')

# orders/views.py
def order_history(request):
    return render(request, 'orders/history.html')
```


## 템플릿 네임스페이싱의 장점

### 1. **이름 충돌 방지**
- 여러 앱에서 같은 이름의 템플릿을 안전하게 사용할 수 있습니다.

### 2. **코드 가독성 향상**  
- `render(request, 'products/list.html')`만 봐도 어떤 앱의 템플릿인지 바로 알 수 있습니다.

### 3. **유지보수성 향상**
- 템플릿 파일을 찾기 쉬워지고, 수정할 때 실수할 가능성이 줄어듭니다.

### 4. **팀 개발에 유리**
- 여러 개발자가 동시에 작업할 때 템플릿 충돌을 방지할 수 있습니다.

## 템플릿 상속과 함께 사용하기

네임스페이싱은 템플릿 상속과도 잘 어울립니다.

```html
<!-- templates/base.html (프로젝트 루트 템플릿) -->
<!DOCTYPE html>
<html>
<head>
    <title>My Shop</title>
</head>
<body>
    {% block content %}
    {% endblock %}
</body>
</html>

<!-- accounts/templates/accounts/login.html -->
{% extends 'base.html' %}

{% block content %}
<div class="login-form">
    <h2>로그인</h2>
    <!-- 로그인 폼 내용 -->
</div>
{% endblock %}

<!-- products/templates/products/list.html -->
{% extends 'base.html' %}

{% block content %}
<div class="product-list">
    <h2>상품 목록</h2>
    <!-- 상품 목록 내용 -->
</div>
{% endblock %}
```

## 정리

Django에서 템플릿을 사용할 때는 반드시 **앱 이름으로 네임스페이스를 생성**해야 합니다.

- ❌ 잘못된 방법: `accounts/templates/index.html`
- ✅ 올바른 방법: `accounts/templates/accounts/index.html`

이는 Django 공식 문서에서도 권장하는 모범 사례이며, 실제 프로덕션 환경에서는 필수적인 구조입니다. 번거로울 수 있지만, 프로젝트가 커질수록 그 중요성을 실감하게 될 것입니다.