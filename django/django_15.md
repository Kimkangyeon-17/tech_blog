# REST API 구현하기

Two Scoops of Django 16장 "REST API"를 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

오늘날 인터넷은 HTML 기반의 웹 사이트 모음 이상의 것으로 발전하며 개발자들은 AJAX와 모바일 앱을 지원해야 하며 JSON, YAML, XML 등 여러 형식을 지원해야 하는 일이 과제가 되었습니다.

**REST(Representational State Transfer) API**는 다양한 환경과 용도에 맞는 데이터를 제공하는 디자인을 정의하고 있습니다.

## 기본 REST API 디자인의 핵심

REST API는 HTTP를 기반으로 삼고 있으므로 각 액션에 대해 알맞은 HTTP 메서드를 이용하면 됩니다.

### HTTP 메서드와 용도

| 요청 목적 | HTTP 메서드 | 대략적으로 매칭되는 SQL |
| ---- | ---- | ---- |
| 새로운 리소스 생성 | POST | INSERT |
| 리소스 읽기 | GET | SELECT |
| 리소스의 메타데이터 요청 | HEAD | - |
| 리소스 데이터 업데이트 | PUT | UPDATE |
| 리소스의 부분 변경 | PATCH | UPDATE |
| 리소스 삭제 | DELETE | DELETE |
| 특정 URL에 대해 지원되는 HTTP 메서드 출력 | OPTIONS | - |
| 요청(request)에 대한 반환 에코 | TRACE | - |
| TCP/IP 터널링(일반적으로 구현되어 있지는 않음) | CONNECT | - |

```python
# Django REST Framework 예시
from rest_framework import viewsets
from rest_framework.decorators import action
from rest_framework.response import Response

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    
    # GET /api/posts/ - 목록 조회
    def list(self, request):
        posts = self.get_queryset()
        serializer = self.get_serializer(posts, many=True)
        return Response(serializer.data)
    
    # POST /api/posts/ - 새 게시글 생성
    def create(self, request):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=201)
    
    # GET /api/posts/{id}/ - 특정 게시글 조회
    def retrieve(self, request, pk=None):
        post = self.get_object()
        serializer = self.get_serializer(post)
        return Response(serializer.data)
    
    # PUT /api/posts/{id}/ - 전체 업데이트
    def update(self, request, pk=None):
        post = self.get_object()
        serializer = self.get_serializer(post, data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data)
    
    # PATCH /api/posts/{id}/ - 부분 업데이트
    def partial_update(self, request, pk=None):
        post = self.get_object()
        serializer = self.get_serializer(post, data=request.data, partial=True)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data)
    
    # DELETE /api/posts/{id}/ - 삭제
    def destroy(self, request, pk=None):
        post = self.get_object()
        post.delete()
        return Response(status=204)
```

### HTTP 메서드 사용 가이드라인

- **읽기 전용 API**만 구현한다면 GET 메서드만 구현하면 됩니다
- **읽기/쓰기 API**를 구현한다면 최소한 POST 메서드는 구현해야 하며 PUT과 DELETE 또한 고려해야 합니다
- 단순화하기 위해 때때로 REST API는 **GET과 POST만으로 구현**되도록 설계하기도 합니다
- **GET, PUT, DELETE는 멱등관계**(여러 번 실행해도 그 결과가 변하지 않음)이며 POST와 PATCH는 그렇지 않습니다
- PATCH가 구현되어 있지 않은 경우도 있지만 **API가 PUT 요청을 지원한다면 PATCH 또한 구현하는 것이 좋습니다**
- **Django REST Framework**와 **Django Tastypie**는 앞의 모든 경우를 처리합니다

### HTTP 상태 코드

아래의 표는 REST API를 구현할 때 일반적으로 알아야 할 HTTP 상태 코드들입니다.

| HTTP 상태 코드 | 성공/실패 | 의미 |
| ---- | ---- | ---- |
| 200 OK | Success | GET - 리소스 반환, PUT - 상태 메시지 제공 또는 리소스 반환 |
| 201 Created | Success | POST - 상태 메시지 반환 또는 새로 생성된 리소스 반환 |
| 204 No Content | Success | DELETE - 성공적으로 삭제된 요청의 응답 |
| 304 Unchanged | Redirect | ALL - 이전 요청으로부터 아무런 변화가 없음, 마지막으로 수정된 리소스나 ETag 헤더 확인 |
| 400 Bad Request | Failure | ALL - 폼 검증 에러를 포함한 에러 메시지 반환 |
| 401 Unauthorized | Failure | ALL - 인증 요청을 했으나 사용자가 인증 요건을 제공하지 않음 |
| 403 Forbidden | Failure | ALL - 사용자가 허용되지 않은 콘텐츠로 접근을 시도함 |
| 404 Not Found | Failure | ALL - 리소스 없음 |
| 405 Method Not Allowed | Failure | ALL - 허가되지 않은 HTTP 메서드로 시도됨 |
| 410 Gone | Failure | ALL - 더는 제공되지 않는 메서드로 호출 |
| 429 Too Many Requests | Failure | ALL - 제한 시간 내에 너무 많은 요청을 보냄 |

```python
# 상태 코드 사용 예시
from rest_framework import status
from rest_framework.response import Response
from rest_framework.views import APIView

class PostCreateView(APIView):
    def post(self, request):
        serializer = PostSerializer(data=request.data)
        
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class PostDeleteView(APIView):
    def delete(self, request, pk):
        try:
            post = Post.objects.get(pk=pk)
            post.delete()
            return Response(status=status.HTTP_204_NO_CONTENT)
        except Post.DoesNotExist:
            return Response(status=status.HTTP_404_NOT_FOUND)
```

## REST API 아키텍처

Django REST Framework나 Django Tastypie를 이용하여 빠르게 API를 구현할 수 있습니다.

### 프로젝트 코드들은 간결하게 정리되어 있어야 한다

서로 긴밀히 연관된 작은 앱으로 구성된 프로젝트의 경우 특정 API의 위치를 찾기 쉽지 않기 때문에 모든 API 코드를 각각의 해당 앱에 위치시키는 것보다 **API만 전담해서 처리하는 앱을 따로 구성하는 것**이 때때로 더 적절하기도 합니다.

```
✅ API 전담 앱 구조
myproject/
    ├── api/
    │   ├── __init__.py
    │   ├── serializers.py
    │   ├── views.py
    │   └── urls.py
    ├── posts/
    │   ├── models.py
    │   └── views.py
    └── users/
        ├── models.py
        └── views.py
```

이러한 API 앱은 API 구현에 대한 모든 Serializer, Renderer, View가 위치하는 곳이 될 것입니다. 하지만 이런 식의 API 구성의 단점은 **해당 API 앱이 너무 커질 수 있고** 또한 각 API를 지원하는 개별 앱으로부터 해당 API 앱이 단절될 수도 있다는 점입니다.

### 앱의 코드는 앱 안에 두자

REST API는 단순한 뷰의 모음입니다. 단순한 소규모 프로젝트의 경우 REST API의 뷰는 `views.py`나 `viewsets.py` 모듈 안에 위치하면서 일반적인 뷰가 따르는 가이드를 똑같이 따르면 됩니다.

```
✅ 앱별 API 구조
myproject/
    ├── posts/
    │   ├── models.py
    │   ├── serializers.py
    │   ├── views.py
    │   └── api_views.py  # 또는 viewsets.py
    └── users/
        ├── models.py
        ├── serializers.py
        └── api_views.py
```

하지만 하나의 `views.py`나 `viewsets.py` 모듈에 담기에는 너무 많은 REST API 뷰 클래스를 포함하는 경우에는 **이를 분할하는 방법을 사용**해야 합니다.

```
✅ 뷰셋 패키지로 분할
posts/
    ├── viewsets/
    │   ├── __init__.py
    │   ├── post_viewsets.py
    │   ├── comment_viewsets.py
    │   └── category_viewsets.py
    ├── models.py
    └── serializers.py
```

이러한 구성의 단점은 너무 작게 나뉘고 수가 많아지면 **API 컴포넌트들이 어디에 있으며 앞으로 어떻게 쓰이는지 관리하기 너무 어려워질 수도 있다**는 점입니다.

### 비즈니스 로직을 API 뷰에서 분리하기

어떤 구조를 따르든지 간에 **가능한 한 로직을 API 뷰에서 분리**하는 것이 좋습니다.

```python
# ❌ 나쁜 예 - 비즈니스 로직이 뷰에 포함
class PostViewSet(viewsets.ModelViewSet):
    def create(self, request):
        # 복잡한 비즈니스 로직이 뷰에 포함됨
        data = request.data
        if data['price'] > 10000:
            data['discount'] = data['price'] * 0.1
        # ...더 많은 로직
        serializer = self.get_serializer(data=data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data)

# ✅ 좋은 예 - 비즈니스 로직을 분리
# services.py
class PostService:
    @staticmethod
    def create_post(data):
        # 비즈니스 로직
        if data['price'] > 10000:
            data['discount'] = data['price'] * 0.1
        post = Post.objects.create(**data)
        return post

# viewsets.py
class PostViewSet(viewsets.ModelViewSet):
    def create(self, request):
        post = PostService.create_post(request.data)
        serializer = self.get_serializer(post)
        return Response(serializer.data, status=201)
```

### API URL을 모아 두기

프로젝트 전반에 걸친 API를 한데 모으는 방법으로 URL 설정을 이용할 수 있습니다. URLConf를 이용하여 `core/api.py` 또는 `core/apiv1.py`라는 이름으로 구현된 모듈을 하나로 묶어 `urls.py` 안에 포함시킬 수 있습니다.

```python
# core/api_urls.py (또는 core/apiv1.py)
from django.urls import path, include
from rest_framework.routers import DefaultRouter

from posts.viewsets import PostViewSet
from users.viewsets import UserViewSet

router = DefaultRouter()
router.register('posts', PostViewSet)
router.register('users', UserViewSet)

urlpatterns = [
    path('', include(router.urls)),
]

# 메인 urls.py
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/v1/', include('core.api_urls')),  # API URL 모음
]
```

### API 테스트하기

REST API는 반드시 테스트되어야 합니다. Django REST Framework는 API 테스트를 위한 강력한 도구를 제공합니다.

```python
# tests.py
from rest_framework.test import APITestCase
from rest_framework import status
from django.contrib.auth.models import User

class PostAPITest(APITestCase):
    def setUp(self):
        self.user = User.objects.create_user('testuser', 'test@test.com', 'pass')
        
    def test_create_post(self):
        """게시글 생성 테스트"""
        self.client.force_authenticate(user=self.user)
        data = {'title': 'Test Post', 'content': 'Test Content'}
        response = self.client.post('/api/v1/posts/', data)
        
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(response.data['title'], 'Test Post')
    
    def test_list_posts(self):
        """게시글 목록 조회 테스트"""
        response = self.client.get('/api/v1/posts/')
        self.assertEqual(response.status_code, status.HTTP_200_OK)
    
    def test_unauthorized_create(self):
        """인증 없이 생성 시도 테스트"""
        data = {'title': 'Test', 'content': 'Test'}
        response = self.client.post('/api/v1/posts/', data)
        self.assertEqual(response.status_code, status.HTTP_401_UNAUTHORIZED)
```

### API 버저닝하기

API를 구현할 때는 **이용자와 고객에게 시간적 여유를 충분히 두고 이전 API 중단에 대해 경고**함으로써 기존 애플리케이션을 업그레이드할 수 있게 하여 애플리케이션이 중단되는 일이 없게 해야 합니다.

```python
# URL 기반 버저닝
urlpatterns = [
    path('api/v1/', include('core.api_v1_urls')),
    path('api/v2/', include('core.api_v2_urls')),
]

# 헤더 기반 버저닝
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_VERSIONING_CLASS': 'rest_framework.versioning.AcceptHeaderVersioning',
    'DEFAULT_VERSION': 'v1',
    'ALLOWED_VERSIONS': ['v1', 'v2'],
}

# 쿼리 파라미터 기반 버저닝
# /api/posts/?version=v2
REST_FRAMEWORK = {
    'DEFAULT_VERSIONING_CLASS': 'rest_framework.versioning.QueryParameterVersioning',
}
```

## 서비스 지향 아키텍처

**서비스 지향 아키텍처(Service-Oriented Architecture, SOA)** 관점에서 웹 애플리케이션은 독립적이고 분리된 컴포넌트로 구성됩니다. 각 컴포넌트는 독립된 서버 또는 클러스터에서 구동되며 이러한 각 컴포넌트는 서로 커뮤니케이션을 하게 됩니다.

```
SOA 예시:
┌─────────────┐      REST API      ┌─────────────┐
│   Frontend  │ ◄───────────────►  │  Auth API   │
│  (React)    │                    │  (Django)   │
└─────────────┘                    └─────────────┘
       │                                   │
       │ REST API                          │
       ▼                                   ▼
┌─────────────┐                    ┌─────────────┐
│  Posts API  │                    │ Database    │
│  (Django)   │                    └─────────────┘
└─────────────┘
```

많은 엔지니어가 상호 충돌 없이 서로 분리된 컴포넌트를 작업하기 한결 수월해지기 때문에 서비스 지향 아키텍처를 주로 이용합니다. **작은 프로젝트에서는 많은 컴포넌트와의 연동 부분이 더 많은 복잡성을 야기**하기 때문에 장점보다는 많은 문제를 야기할 수 있습니다.

## 외부 API 중단하기

새로운 버전의 API를 위해 이전 버전의 외부 서비스 API를 중단해야 할 때가 되었을 경우 다음과 같은 단계를 따르면 유용할 것입니다.

### 1단계: 사용자들에게 서비스 중지 예고하기

**6개월 정도의 기간 혹은 최소한 한 달 전에는 사용자들에게 미리 공지**를 하는 것이 중요합니다.

```python
# API 응답에 경고 헤더 추가
from rest_framework.response import Response

class OldAPIView(APIView):
    def get(self, request):
        data = {'message': 'This API will be deprecated'}
        response = Response(data)
        response['Warning'] = '299 - "This API will be deprecated on 2025-12-31"'
        response['Sunset'] = '2025-12-31'  # 중단 예정일
        return response
```

### 2단계: 410 에러 뷰로 API 교체하기

최종적으로 API가 중지되었을 때 간단한 **410 에러 뷰를 서비스**합니다. 다음과 같은 정보를 담은 경우 매우 간단한 메시지 정도를 서비스합니다:

- 새 API 엔드포인트의 링크
- 새 API 문서의 링크
- 서비스 중지에 대한 세부 사항을 알려주는 문서로의 링크

```python
# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

class DeprecatedAPIView(APIView):
    def get(self, request, *args, **kwargs):
        return Response({
            'error': 'This API endpoint has been deprecated',
            'message': 'Please use the new API v2',
            'new_endpoint': 'https://api.example.com/v2/posts/',
            'documentation': 'https://docs.example.com/api/v2/',
            'migration_guide': 'https://docs.example.com/migration/'
        }, status=status.HTTP_410_GONE)
    
    post = get
    put = get
    delete = get
```

## API에 접속 제한하기

접속 제한은 **한 사용자가 주어진 시간에 얼마 이상의 요청을 보낼 때 이를 제어**하는 것을 의미합니다.

### 제한 없는 API 접속의 위험

수천 개의 요청을 보내고 이러한 제한 없는 접속은 웹 애플리케이션에 문제를 일으키게 됩니다.

### REST 프레임워크는 반드시 접속 제한을 해야만 합니다

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle'
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',      # 익명 사용자: 하루 100회
        'user': '1000/day',     # 인증 사용자: 하루 1000회
    }
}

# 특정 뷰에만 적용
from rest_framework.throttling import UserRateThrottle

class PostViewSet(viewsets.ModelViewSet):
    throttle_classes = [UserRateThrottle]
    throttle_scope = 'posts'  # 특정 스코프 정의

# settings.py에 스코프별 제한 추가
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_RATES': {
        'posts': '10/minute',
    }
}
```

### 비즈니스 계획으로서의 접속 제한

```python
# 사용자 등급별 접속 제한
from rest_framework.throttling import UserRateThrottle

class PremiumUserThrottle(UserRateThrottle):
    def get_rate(self):
        if self.request.user.is_authenticated and self.request.user.is_premium:
            return '10000/day'  # 프리미엄 사용자
        return '1000/day'  # 일반 사용자

class PostViewSet(viewsets.ModelViewSet):
    throttle_classes = [PremiumUserThrottle]
```

## 자신의 REST API 알리기

REST API 제작을 모두 마치고 다른 개발자들이나 회사가 이를 이용하기 위해서는 다음과 같은 과정이 중요합니다.

### 문서

반드시 **가독성이 좋고 쉽게 이용 가능한 예제 코드를 제공**해야 합니다. Django REST Framework, Sphinx, Markdown 등 다양한 도구를 이용하여 생성하여도 됩니다.

```python
# drf-spectacular를 이용한 자동 API 문서 생성
# settings.py
INSTALLED_APPS = [
    'drf_spectacular',
]

REST_FRAMEWORK = {
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

# urls.py
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView

urlpatterns = [
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    path('api/docs/', SpectacularSwaggerView.as_view(url_name='schema')),
]
```

### 클라이언트 SDK 제공

여러 언어를 제공하는 **SDK(Software Development Kit)**를 제공할 수도 있습니다. Python, JavaScript, Ruby, PHP, Go, Java는 반드시 지원하는 것이 좋습니다.

```python
# Python SDK 예시
class MyAPIClient:
    def __init__(self, api_key):
        self.api_key = api_key
        self.base_url = 'https://api.example.com/v1'
    
    def get_posts(self, page=1):
        url = f'{self.base_url}/posts/?page={page}'
        headers = {'Authorization': f'Token {self.api_key}'}
        response = requests.get(url, headers=headers)
        return response.json()
    
    def create_post(self, title, content):
        url = f'{self.base_url}/posts/'
        headers = {'Authorization': f'Token {self.api_key}'}
        data = {'title': title, 'content': content}
        response = requests.post(url, json=data, headers=headers)
        return response.json()

# 사용 예시
client = MyAPIClient('your-api-key')
posts = client.get_posts()
new_post = client.create_post('Title', 'Content')
```

## 요약 및 베스트 프랙티스

REST API는 현대 웹 개발의 핵심이므로, 표준을 준수하고 사용자 친화적인 API를 설계하는 것이 중요합니다.