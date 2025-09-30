# Django 쿼리와 데이터베이스 레이어

Two Scoops of Django 7장 "쿼리와 데이터베이스 레이어"를 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

## Django ORM의 특징

다른 ORM(Object Relational Mapping)과 마찬가지로 Django는 여러 종류의 각기 다른 데이터베이스를 데이터베이스 종류와 독립적인 형태로 객체화합니다. 이를 통해 개발자는 SQL을 직접 작성하지 않고도 파이썬 코드로 데이터베이스 작업을 수행할 수 있습니다.

## 안전한 단일 객체 조회

### get_object_or_404() 사용하기

단일 객체를 가져와 작업하는 상세 페이지 같은 **뷰에서는** `get()` 대신 `get_object_or_404()`를 사용해야 합니다.

```python
# ❌ 나쁜 예 - 뷰에서 get() 사용
def post_detail(request, pk):
    try:
        post = Post.objects.get(pk=pk)
    except Post.DoesNotExist:
        # 수동으로 404 처리해야 함
        raise Http404("게시글을 찾을 수 없습니다")
    return render(request, 'post_detail.html', {'post': post})

# ✅ 좋은 예 - get_object_or_404() 사용
from django.shortcuts import get_object_or_404

def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    return render(request, 'post_detail.html', {'post': post})
```

> ⚠️ **주의사항**: `get_object_or_404()`는 **뷰에서만** 사용해야 합니다. 모델 메서드, 헬퍼 함수 등 다른 곳에서는 일반적인 예외 처리를 사용하세요.

```python
# ❌ 모델 메서드에서 get_object_or_404() 사용하지 말 것
class PostManager(models.Manager):
    def get_published_post(self, pk):
        return get_object_or_404(Post, pk=pk, is_published=True)  # 잘못됨

# ✅ 모델에서는 일반 예외 처리 사용
class PostManager(models.Manager):
    def get_published_post(self, pk):
        return self.get(pk=pk, is_published=True)  # DoesNotExist 발생 가능
```

## 예외 처리하기

### ObjectDoesNotExist vs DoesNotExist

```python
from django.core.exceptions import ObjectDoesNotExist

# ObjectDoesNotExist - 모든 모델에서 사용 가능
try:
    post = Post.objects.get(pk=999)
except ObjectDoesNotExist:
    print("객체가 존재하지 않습니다")

# DoesNotExist - 특정 모델에서만 사용 가능
try:
    post = Post.objects.get(pk=999)
except Post.DoesNotExist:
    print("게시글이 존재하지 않습니다")
```

### MultipleObjectsReturned 처리

```python
# 여러 객체가 반환될 수 있는 경우
try:
    user = User.objects.get(email="user@example.com")
except User.DoesNotExist:
    print("사용자가 존재하지 않습니다")
except User.MultipleObjectsReturned:
    print("동일한 이메일을 가진 사용자가 여러 명 있습니다")
    user = User.objects.filter(email="user@example.com").first()
```

## 지연 연산으로 쿼리 명확하게 하기

Django ORM의 **지연 연산(Lazy Evaluation)**을 활용하면 코드를 더 읽기 쉽게 만들 수 있습니다. 지연 연산은 데이터가 정말 필요하기 전까지는 Django가 SQL을 실행하지 않는 특징입니다.

```python
# ❌ 한 줄에 모든 것을 압축 (읽기 어려움)
posts = Post.objects.filter(is_published=True).select_related('author').prefetch_related('tags').order_by('-created_at')[:10]

# ✅ 여러 줄로 나누어 가독성 향상 (같은 쿼리)
posts = Post.objects.filter(is_published=True)
posts = posts.select_related('author')
posts = posts.prefetch_related('tags')
posts = posts.order_by('-created_at')
recent_posts = posts[:10]  # 이 시점에서 실제 SQL 실행

# 더 읽기 쉬운 버전
published_posts = Post.objects.filter(is_published=True)
posts_with_author = published_posts.select_related('author')
posts_with_tags = posts_with_author.prefetch_related('tags')
sorted_posts = posts_with_tags.order_by('-created_at')
recent_posts = sorted_posts[:10]
```

## 고급 쿼리 도구 활용하기

### 쿼리 표현식 (Query Expressions)

쿼리 표현식을 사용하면 데이터베이스 레벨에서 연산을 수행할 수 있어 성능이 크게 향상됩니다.

```python
from django.db.models import F, Value
from django.db.models.functions import Concat

# ❌ 파이썬에서 처리 (느림)
for product in Product.objects.all():
    product.discounted_price = product.price * 0.9
    product.save()

# ✅ 데이터베이스에서 처리 (빠름)
Product.objects.update(
    discounted_price=F('price') * 0.9
)

# 필드 간 비교
expensive_products = Product.objects.filter(
    price__gt=F('cost') * 2  # 가격이 원가의 2배보다 큰 상품
)

# 문자열 연결
User.objects.update(
    full_name=Concat('first_name', Value(' '), 'last_name')
)
```

### 데이터베이스 함수들

Django에서 제공하는 데이터베이스 함수들을 활용하면 성능과 이식성을 모두 얻을 수 있습니다.

```python
from django.db.models.functions import Upper, Lower, Length, Substr, Coalesce

# 대소문자 변환
users = User.objects.annotate(
    upper_name=Upper('username'),
    lower_email=Lower('email')
)

# 문자열 길이 계산
long_posts = Post.objects.annotate(
    content_length=Length('content')
).filter(content_length__gt=1000)

# NULL 값 처리
products = Product.objects.annotate(
    display_name=Coalesce('nickname', 'name', Value('이름 없음'))
)

# 부분 문자열 추출
User.objects.annotate(
    name_initial=Substr('first_name', 1, 1)  # 이름 첫 글자
)
```

## Raw SQL 사용 시 주의사항

이용하려는 쿼리를 ORM으로 표현할 수 있다면 **반드시 ORM을 이용**해야 합니다. Raw SQL은 앱의 이식성을 떨어뜨리고 보안 위험을 증가시킵니다.

```python
# ✅ ORM 사용 (권장)
active_users = User.objects.filter(
    is_active=True,
    last_login__gte=timezone.now() - timedelta(days=30)
).count()

# ❌ Raw SQL (불가피한 경우만)
from django.db import connection

def complex_report():
    with connection.cursor() as cursor:
        cursor.execute("""
            SELECT COUNT(*) FROM users 
            WHERE is_active = %s 
            AND last_login >= %s
        """, [True, timezone.now() - timedelta(days=30)])
        return cursor.fetchone()[0]
```

### Raw SQL을 사용해야 하는 경우

1. ORM으로 표현할 수 없는 복잡한 쿼리
2. 성능상 Raw SQL이 현저히 빠른 경우
3. 데이터베이스 특정 기능을 사용해야 하는 경우

## 데이터베이스 트랜잭션

Django는 기본적으로 각 ORM 쿼리마다 자동으로 커밋을 수행합니다. 하지만 여러 데이터베이스 작업을 하나의 단위로 처리해야 할 때는 **트랜잭션**을 사용해야 합니다.

### 트랜잭션의 ACID 특성
- **원자성(Atomic)**: 모든 작업이 성공하거나 모두 실패
- **일관성(Consistent)**: 데이터베이스 규칙 유지
- **독립성(Isolated)**: 동시 실행되는 트랜잭션 간 간섭 없음
- **지속성(Durable)**: 커밋된 데이터는 영구 보존

### 각 HTTP 요청을 트랜잭션으로 처리

```python
# settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'OPTIONS': {
            'ATOMIC_REQUESTS': True,  # 모든 뷰를 트랜잭션으로 처리
        },
        # ... 기타 설정
    }
}
```

**장점:**
- 뷰의 모든 데이터베이스 작업이 자동으로 보호됨
- 간단하고 실수할 여지가 적음

**단점:**
- 읽기 전용 뷰에도 트랜잭션이 적용되어 성능 저하 가능
- 오직 에러 발생 시에만 롤백됨

### 명시적 트랜잭션 선언 (권장)

명시적 트랜잭션을 사용하면 성능과 안전성을 모두 확보할 수 있습니다.

```python
from django.db import transaction

# 함수 데코레이터로 사용
@transaction.atomic
def transfer_money(from_account, to_account, amount):
    from_account.balance -= amount
    from_account.save()
    
    to_account.balance += amount
    to_account.save()

# 컨텍스트 매니저로 사용
def create_blog_post(title, content, author):
    with transaction.atomic():
        post = Post.objects.create(
            title=title,
            content=content,
            author=author
        )
        
        # 알림 생성
        Notification.objects.create(
            user=author,
            message=f"새 글 '{title}'이 발행되었습니다"
        )
        
        return post

# 뷰에서 사용
@transaction.atomic
def create_order(request):
    if request.method == 'POST':
        # 주문 생성과 재고 차감이 하나의 트랜잭션으로 처리
        order = Order.objects.create(user=request.user)
        
        for item_data in request.POST.getlist('items'):
            product = Product.objects.get(id=item_data['product_id'])
            if product.stock < item_data['quantity']:
                raise ValueError("재고가 부족합니다")
            
            OrderItem.objects.create(
                order=order,
                product=product,
                quantity=item_data['quantity']
            )
            
            # 재고 차감
            product.stock -= item_data['quantity']
            product.save()
        
        return redirect('order_success', order_id=order.id)
```

### 트랜잭션 가이드라인

**✅ 트랜잭션을 사용해야 하는 경우:**
- 여러 모델에 걸친 데이터 변경
- 데이터 일관성이 중요한 비즈니스 로직
- 중간에 실패하면 모든 변경을 되돌려야 하는 작업

```python
# 예: 주문 처리 (재고 차감 + 주문 생성)
@transaction.atomic
def process_order(user, items):
    order = Order.objects.create(user=user)
    
    for item in items:
        product = item['product']
        quantity = item['quantity']
        
        # 재고 확인 및 차감
        if product.stock < quantity:
            raise ValueError(f"{product.name} 재고 부족")
        
        product.stock -= quantity
        product.save()
        
        # 주문 항목 생성
        OrderItem.objects.create(
            order=order,
            product=product,
            quantity=quantity
        )
    
    return order
```

**❌ 트랜잭션이 불필요한 경우:**
- 단순 읽기 작업
- 단일 모델의 단순 생성/수정
- 외부 API 호출을 포함하는 작업

```python
# 읽기 전용 - 트랜잭션 불필요
def get_user_posts(user_id):
    return Post.objects.filter(author_id=user_id).select_related('author')

# 단일 생성 - 트랜잭션 불필요  
def create_simple_post(title, content):
    return Post.objects.create(title=title, content=content)
```

### Savepoint 사용하기

복잡한 트랜잭션에서 부분 롤백이 필요한 경우 savepoint를 사용할 수 있습니다.

```python
@transaction.atomic
def complex_operation():
    # 메인 작업
    user = User.objects.create(username="newuser")
    
    # 부분 작업을 savepoint로 보호
    try:
        with transaction.atomic():  # savepoint 생성
            risky_operation()
    except Exception:
        # risky_operation만 롤백되고 user 생성은 유지
        logger.warning("부분 작업 실패, 메인 작업은 계속")
    
    # 후속 작업
    Profile.objects.create(user=user)
```

## 성능 최적화 팁

### 1. N+1 쿼리 문제 해결

```python
# ❌ N+1 문제 (1 + N번의 쿼리)
posts = Post.objects.all()
for post in posts:
    print(f"작성자: {post.author.name}")  # 각 반복마다 DB 쿼리

# ✅ select_related 사용 (1번의 JOIN 쿼리)
posts = Post.objects.select_related('author')
for post in posts:
    print(f"작성자: {post.author.name}")

# ✅ prefetch_related 사용 (M2M, 역참조)
posts = Post.objects.prefetch_related('tags')
for post in posts:
    print(f"태그: {', '.join(tag.name for tag in post.tags.all())}")
```

### 2. 불필요한 데이터 조회 방지

```python
# ❌ 모든 필드 조회
posts = Post.objects.all()

# ✅ 필요한 필드만 조회
posts = Post.objects.only('title', 'created_at')

# ✅ 특정 필드 제외
posts = Post.objects.defer('content')  # content 필드 제외

# ✅ 집계 쿼리 사용
post_count = Post.objects.filter(is_published=True).count()
```

## 요약

### ✅ 해야 할 것들
- 뷰에서는 `get_object_or_404()` 사용
- 지연 연산을 활용하여 가독성 높은 쿼리 작성
- 쿼리 표현식과 데이터베이스 함수 적극 활용
- 데이터 일관성이 중요한 작업은 반드시 트랜잭션 사용
- N+1 쿼리 문제 방지를 위한 `select_related`, `prefetch_related` 사용

### ❌ 피해야 할 것들
- 뷰가 아닌 곳에서 `get_object_or_404()` 사용
- 불필요한 Raw SQL 사용
- 모든 HTTP 요청을 트랜잭션으로 처리 (`ATOMIC_REQUESTS=True`)
- N+1 쿼리 문제 방치
- 외부 API 호출을 트랜잭션 내부에 포함

Django ORM을 효과적으로 활용하면 안전하고 성능이 우수한 데이터베이스 작업을 수행할 수 있습니다. 특히 트랜잭션과 쿼리 최적화는 실제 서비스에서 매우 중요한 부분입니다.