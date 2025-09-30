# Django 함수 기반 뷰의 모범적인 이용

Two Scoops of Django 9장 "함수 기반 뷰의 모범적인 이용"을 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

## 함수 기반 뷰의 특징

함수 기반 뷰의 **단순함**은 **코드 재사용을 희생한 결과**입니다. 클래스 기반 뷰처럼 상속 기능이 없어 코드 중복이 발생할 수 있습니다.

```python
# FBV - 상속 불가능
def post_list(request):
    posts = Post.objects.all()
    return render(request, 'posts/list.html', {'posts': posts})

# CBV - 상속 가능
class PostListView(ListView):
    model = Post
    template_name = 'posts/list.html'
```

## FBV 작성 가이드라인

### 1. 뷰 코드는 작을수록 좋다

```python
# ✅ 좋은 예 - 간결한 뷰
def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    return render(request, 'posts/detail.html', {'post': post})

# ❌ 나쁜 예 - 너무 긴 뷰
def massive_view(request):
    # 100줄 이상의 복잡한 로직
    pass
```

### 2. 코드 중복 방지

데코레이터나 헬퍼 함수를 사용하여 중복을 제거합니다.

```python
# ❌ 나쁜 예 - 코드 중복
def post_list(request):
    if not request.user.is_authenticated:
        return redirect('login')
    posts = Post.objects.filter(author=request.user)
    return render(request, 'posts/list.html', {'posts': posts})

def post_create(request):
    if not request.user.is_authenticated:
        return redirect('login')
    # 중복된 인증 체크
    pass

# ✅ 좋은 예 - 데코레이터 사용
@login_required
def post_list(request):
    posts = Post.objects.filter(author=request.user)
    return render(request, 'posts/list.html', {'posts': posts})

@login_required
def post_create(request):
    # 인증 체크 자동 처리
    pass
```

### 3. 프레젠테이션 로직만 처리

뷰는 **프레젠테이션 로직**만 담당하고, **비즈니스 로직**은 모델에서 처리합니다.

```python
# ✅ 좋은 예
def user_profile(request, username):
    user = get_object_or_404(User, username=username)
    context = {
        'profile_user': user,
        'posts': user.get_published_posts(),  # 비즈니스 로직은 모델에서
        'is_own_profile': request.user == user,
    }
    return render(request, 'users/profile.html', context)

# models.py
class User(AbstractUser):
    def get_published_posts(self):
        return self.post_set.filter(is_published=True)
```

### 4. 단순하게 유지

```python
# ✅ 단순한 뷰
@login_required
def dashboard(request):
    return render(request, 'dashboard.html', {
        'recent_posts': request.user.get_recent_posts(),
        'notifications': request.user.get_unread_notifications(),
    })
```

### 5. 커스텀 에러 뷰에 활용

FBV는 에러 페이지 처리에 적합합니다.

```python
def custom_404(request, exception):
    return render(request, 'errors/404.html', status=404)

def custom_500(request):
    return render(request, 'errors/500.html', status=500)

# settings.py
# handler404 = 'myapp.views.custom_404'
# handler500 = 'myapp.views.custom_500'
```

### 6. 복잡한 중첩 조건문 피하기

Early return 패턴을 사용하여 중첩을 줄입니다.

```python
# ❌ 나쁜 예 - 복잡한 중첩
def post_edit(request, pk):
    if request.user.is_authenticated:
        post = get_object_or_404(Post, pk=pk)
        if post.author == request.user:
            if request.method == 'POST':
                # 처리 로직
                pass

# ✅ 좋은 예 - Early return
@login_required
def post_edit(request, pk):
    post = get_object_or_404(Post, pk=pk)
    
    if post.author != request.user:
        return HttpResponseForbidden()
    
    if request.method == 'POST':
        # 처리 로직
        pass
    
    return render(request, 'posts/edit.html', {'post': post})
```

## HTTPRequest 객체 전달하기

뷰에서 코드를 재사용할 때 `request` 객체를 헬퍼 함수에 전달할 수 있습니다.

```python
def get_user_posts(request, status='published'):
    if not request.user.is_authenticated:
        return Post.objects.none()
    
    return Post.objects.filter(author=request.user, status=status)

def post_list(request):
    posts = get_user_posts(request)
    return render(request, 'posts/list.html', {'posts': posts})

def draft_list(request):
    drafts = get_user_posts(request, 'draft')
    return render(request, 'posts/drafts.html', {'posts': drafts})
```

## 편리한 데코레이터

데코레이터를 사용하면 코드가 간결해지지만, **너무 많은 데코레이터**는 복잡성을 증가시킵니다.

### 적절한 데코레이터 사용

```python
# ✅ 적절한 수준
@login_required
@require_http_methods(["GET", "POST"])
def post_create(request):
    if request.method == 'POST':
        form = PostForm(request.POST)
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            post.save()
            return redirect('post_detail', pk=post.pk)
    else:
        form = PostForm()
    
    return render(request, 'posts/create.html', {'form': form})
```

### 자주 사용하는 데코레이터

```python
# 인증 관련
@login_required
def protected_view(request):
    pass

@permission_required('posts.add_post')
def admin_view(request):
    pass

# HTTP 메서드 제한
@require_POST
def delete_post(request, pk):
    post = get_object_or_404(Post, pk=pk, author=request.user)
    post.delete()
    return redirect('post_list')

# 캐시
@cache_page(60 * 15)  # 15분 캐시
def expensive_view(request):
    # 무거운 연산
    pass
```

### 데코레이터 남용 주의

```python
# ❌ 너무 많은 데코레이터 (가독성 저하)
@login_required
@require_http_methods(["GET", "POST"])
@cache_page(60 * 15)
@ratelimit(key='user', rate='10/m')
@csrf_exempt
@permission_required('posts.add_post')
def complex_view(request):
    pass  # 데코레이터가 더 복잡함
```

## HTTPResponse 객체 전달하기

`HttpResponse` 객체도 함수 간에 전달하여 재사용할 수 있습니다.

```python
def add_security_headers(response):
    """응답에 보안 헤더 추가"""
    response['X-Frame-Options'] = 'DENY'
    response['X-Content-Type-Options'] = 'nosniff'
    return response

def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    response = render(request, 'posts/detail.html', {'post': post})
    return add_security_headers(response)

def api_post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    data = {'title': post.title, 'content': post.content}
    response = JsonResponse(data)
    return add_security_headers(response)
```

## 실용적인 FBV 패턴

### 폼 처리 패턴

```python
@login_required
def post_create(request):
    if request.method == 'POST':
        form = PostForm(request.POST)
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            post.save()
            messages.success(request, '게시글이 작성되었습니다.')
            return redirect('post_detail', pk=post.pk)
    else:
        form = PostForm()
    
    return render(request, 'posts/create.html', {'form': form})
```

### AJAX 처리 패턴

```python
@login_required
@require_POST
def toggle_like(request, pk):
    post = get_object_or_404(Post, pk=pk)
    
    if post.likes.filter(id=request.user.id).exists():
        post.likes.remove(request.user)
        liked = False
    else:
        post.likes.add(request.user)
        liked = True
    
    if request.headers.get('Accept') == 'application/json':
        return JsonResponse({
            'liked': liked,
            'total_likes': post.likes.count()
        })
    
    return redirect('post_detail', pk=pk)
```

### 페이지네이션 패턴

```python
from django.core.paginator import Paginator

def post_list(request):
    posts = Post.objects.filter(is_published=True).order_by('-created_at')
    paginator = Paginator(posts, 10)  # 페이지당 10개
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    
    return render(request, 'posts/list.html', {'page_obj': page_obj})
```

## 요약

### FBV가 적합한 경우

- **커스텀 에러 페이지** (404, 500 등)
- **복잡한 비즈니스 로직**이 필요한 경우
- **간단한 API 엔드포인트**
- **CBV로 구현하기 복잡한 뷰**

함수 기반 뷰는 단순함이 장점이지만, 코드 재사용을 위해서는 데코레이터와 헬퍼 함수를 적극 활용하고, 비즈니스 로직은 적절히 분리하여 관리하는 것이 중요합니다.