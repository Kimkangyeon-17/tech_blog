# Django 함수 기반 뷰와 클래스 기반 뷰

Two Scoops of Django 8장 "함수 기반 뷰와 클래스 기반 뷰"를 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

## 8. 함수 기반 뷰와 클래스 기반 뷰

### Django 뷰의 두 가지 방식

Django에서는 **함수 기반 뷰(Function-Based Views, FBV)**와 **클래스 기반 뷰(Class-Based Views, CBV)**를 모두 지원합니다.

```python
# 함수 기반 뷰 (FBV)
def post_list(request):
    posts = Post.objects.all()
    return render(request, 'posts/list.html', {'posts': posts})

# 클래스 기반 뷰 (CBV)
from django.views.generic import ListView

class PostListView(ListView):
    model = Post
    template_name = 'posts/list.html'
    context_object_name = 'posts'
```

### FBV vs CBV 선택 기준

**클래스 기반 뷰(CBV) 우선 사용** - 다음의 경우에만 함수 기반 뷰(FBV) 사용을 고려:

- CBV로 구현했을 때 특별히 더 복잡해지는 경우
- 커스텀 에러 뷰들 (404, 500 등)
- 매우 간단한 뷰

```python
# ✅ CBV가 적합한 경우 - CRUD 작업
class PostCreateView(CreateView):
    model = Post
    fields = ['title', 'content']
    template_name = 'posts/create.html'

# ✅ FBV가 적합한 경우 - 커스텀 에러 뷰
def custom_404_view(request, exception):
    return render(request, 'errors/404.html', status=404)

# ✅ FBV가 적합한 경우 - 복잡한 비즈니스 로직
def complex_payment_process(request):
    if request.method == 'POST':
        # 복잡한 결제 처리 로직
        # 여러 외부 API 호출
        # 복잡한 조건 분기
        pass
    return render(request, 'payment/process.html')
```

## URLconf와 뷰 로직 분리

### URLconf의 역할

Django로 오는 요청들은 `urls.py` 모듈 내의 URLconf를 통해 뷰로 라우팅됩니다. Django의 디자인 철학에 따라 **뷰와 URL, 모델 사이에는 느슨한 결합**이 유지되어야 합니다.

### 느슨한 결합 유지하기

```python
# ❌ 나쁜 예 - URL에 로직 포함
from django.urls import path

urlpatterns = [
    # URL에서 직접 쿼리 실행 (잘못됨)
    path('posts/', lambda request: render(request, 'posts.html', 
         {'posts': Post.objects.filter(is_published=True)})),
]

# ✅ 좋은 예 - 뷰와 URL 분리
from django.urls import path
from . import views

urlpatterns = [
    path('posts/', views.post_list, name='post_list'),
    path('posts/<int:pk>/', views.post_detail, name='post_detail'),
    path('posts/create/', views.post_create, name='post_create'),
]
```

### URL 이름 작성 방법

URL 이름은 **짧고, 명확하고, 반복을 피해서** 작성해야 합니다.

```python
# ✅ 좋은 URL 이름
urlpatterns = [
    path('posts/', views.PostListView.as_view(), name='post_list'),
    path('posts/<int:pk>/', views.PostDetailView.as_view(), name='post_detail'),
    path('posts/create/', views.PostCreateView.as_view(), name='post_create'),
    
    # 앱별 네임스페이스 활용
    path('blog/', include('blog.urls', namespace='blog')),
]

# ❌ 나쁜 URL 이름  
urlpatterns = [
    path('posts/', views.PostListView.as_view(), name='posts_list_view_page'),
    path('posts/<int:pk>/', views.PostDetailView.as_view(), name='post_detail_page_view'),
]
```

### URLconf에서 뷰를 문자열로 지목하지 말자

현재 Django 버전에서는 뷰를 문자열로 지목하는 방식이 제거되었습니다.

```python
# ❌ 예전 방식 (Django 2.0+ 에서 제거됨)
# urlpatterns = [
#     path('posts/', 'myapp.views.post_list'),
# ]

# ✅ 현재 방식 - 직접 import
from django.urls import path
from . import views

urlpatterns = [
    path('posts/', views.post_list, name='post_list'),
    # 또는 CBV의 경우
    path('posts/', views.PostListView.as_view(), name='post_list'),
]
```

## 뷰에서 비즈니스 로직 분리하기

뷰에서 비즈니스 로직을 분리하면 코드 재사용성이 높아지고 테스트가 쉬워집니다.

```python
# ❌ 나쁜 예 - 뷰에 비즈니스 로직 포함
def order_create(request):
    if request.method == 'POST':
        # 비즈니스 로직이 뷰에 포함됨
        total = 0
        for item in request.POST.getlist('items'):
            product = Product.objects.get(id=item['id'])
            if product.stock < item['quantity']:
                messages.error(request, '재고가 부족합니다')
                return redirect('cart')
            total += product.price * item['quantity']
            product.stock -= item['quantity']
            product.save()
        
        order = Order.objects.create(user=request.user, total=total)
        return redirect('order_success')
    
    return render(request, 'orders/create.html')

# ✅ 좋은 예 - 비즈니스 로직 분리
# services.py (또는 models.py에 메서드로)
class OrderService:
    @staticmethod
    def create_order(user, items):
        total = 0
        order_items = []
        
        for item_data in items:
            product = Product.objects.get(id=item_data['id'])
            if product.stock < item_data['quantity']:
                raise ValueError(f'{product.name} 재고가 부족합니다')
            
            total += product.price * item_data['quantity']
            order_items.append({
                'product': product,
                'quantity': item_data['quantity']
            })
        
        # 트랜잭션으로 처리
        with transaction.atomic():
            order = Order.objects.create(user=user, total=total)
            for item in order_items:
                OrderItem.objects.create(
                    order=order,
                    product=item['product'],
                    quantity=item['quantity']
                )
                item['product'].stock -= item['quantity']
                item['product'].save()
        
        return order

# views.py
def order_create(request):
    if request.method == 'POST':
        try:
            items = request.POST.getlist('items')
            order = OrderService.create_order(request.user, items)
            return redirect('order_success', order_id=order.id)
        except ValueError as e:
            messages.error(request, str(e))
            return redirect('cart')
    
    return render(request, 'orders/create.html')
```

## Django 뷰의 본질

기본적으로 Django의 뷰는 **HTTP 요청 객체를 받아서 HTTP 응답 객체로 변환하는 함수**입니다.

```python
# 뷰의 기본 구조
def my_view(request):
    # 요청 처리 로직
    context = {'data': 'some data'}
    return HttpResponse('Hello, World!')  # 또는 render(), redirect() 등
```

## 요약
Django의 뷰는 HTTP 요청을 HTTP 응답으로 변환하는 단순한 역할에 충실해야 하며, 각각의 장점을 살려 FBV와 CBV를 적절히 선택하여 사용하는 것이 중요합니다.