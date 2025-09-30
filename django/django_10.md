# Django Form의 기초

Two Scoops of Django 11장 "Django Form의 기초"를 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

Django의 대부분은 Form을 이용해야 할 것이고 Form 중에서도 특히 **ModelForm을 사용해야 할 것**입니다.

Django Form에서 기억해야 할 가장 중요한 것은 **어떠한 데이터든 간에 입력 데이터라고 한다면 Django Form을 이용하여 유효성 검사를 해야 한다**는 것입니다.

```python
# ✅ 좋은 예 - Form을 통한 유효성 검사
class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content']

def create_post(request):
    if request.method == 'POST':
        form = PostForm(request.POST)
        if form.is_valid():  # 유효성 검사
            form.save()
            return redirect('post_list')
    else:
        form = PostForm()
    return render(request, 'posts/create.html', {'form': form})

# ❌ 나쁜 예 - 직접 데이터 처리 (보안 위험)
def bad_create_post(request):
    if request.method == 'POST':
        # 유효성 검사 없이 직접 처리 (위험!)
        Post.objects.create(
            title=request.POST['title'],
            content=request.POST['content']
        )
```

## Django Form을 이용하여 모든 입력 데이터에 대한 유효성 검사하기

Django Form은 **Python 딕셔너리의 유효성을 검사하는 데 최상의 도구**입니다.

```python
# 사용자 입력 데이터 유효성 검사
class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    age = forms.IntegerField(min_value=0, max_value=120)

# 딕셔너리 데이터도 검사 가능
data = {
    'name': 'John Doe',
    'email': 'john@example.com',
    'age': 25
}

form = ContactForm(data)
if form.is_valid():
    clean_data = form.cleaned_data
    # 검증된 데이터 사용
else:
    errors = form.errors
    # 에러 처리
```

## HTML 폼에서 POST 메서드 이용하기

**데이터를 변경하는 모든 HTML 폼은 POST 메서드를 이용하여 데이터를 전송**하게 됩니다. 폼에서 POST 메서드를 이용하지 않는 유일한 경우는 **검색 폼**입니다. 검색 폼은 데이터를 변경하지 않기 때문에 GET 메서드를 이용할 수 있습니다.

```html
<!-- ✅ 데이터 변경 - POST 메서드 사용 -->
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">저장</button>
</form>

<!-- ✅ 검색 폼 - GET 메서드 사용 -->
<form method="get">
    <input type="text" name="q" placeholder="검색어 입력">
    <button type="submit">검색</button>
</form>
```

```python
# POST - 데이터 변경
class PostCreateView(CreateView):
    model = Post
    fields = ['title', 'content']

# GET - 검색 (데이터 변경 없음)
def search_posts(request):
    query = request.GET.get('q', '')
    if query:
        posts = Post.objects.filter(title__icontains=query)
    else:
        posts = Post.objects.none()
    return render(request, 'posts/search.html', {'posts': posts})
```

## 데이터를 변경하는 HTTP 폼은 언제나 CSRF 보안을 이용해야 한다

Django에서는 **CSRF(Cross-Site Request Forgery Protection, 사이트 간 위조 요청 방지)**가 내장되어 있습니다. CSRF 보안을 사용하지 않으면 치명적인 보안 문제를 일으킬 수 있으므로 항상 Django CSRF 보안을 이용하는 것을 추천합니다.

```html
<!-- 항상 CSRF 토큰 포함 -->
<form method="post">
    {% csrf_token %}
    <!-- 폼 필드들 -->
</form>
```

```python
# 뷰에서 CSRF 보안 확인
def create_post(request):
    if request.method == 'POST':
        form = PostForm(request.POST)
        if form.is_valid():  # CSRF 토큰도 자동 검증됨
            form.save()
            return redirect('success')
    return render(request, 'create.html', {'form': form})
```

### AJAX를 통해 데이터 추가하기

AJAX를 통해 데이터를 추가할 때는 반드시 Django의 CSRF 보안을 이용해야 합니다. AJAX를 통해 데이터를 보낼 때 **HTTP 헤더에 X-CSRFToken을 설정**해야 합니다.

```javascript
// CSRF 토큰 가져오기
function getCookie(name) {
    let cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        const cookies = document.cookie.split(';');
        for (let i = 0; i < cookies.length; i++) {
            const cookie = cookies[i].trim();
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}

// AJAX 요청에 CSRF 토큰 포함
const csrftoken = getCookie('csrftoken');

fetch('/api/posts/', {
    method: 'POST',
    headers: {
        'X-CSRFToken': csrftoken,
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        title: 'New Post',
        content: 'Post content'
    })
});
```

```python
# AJAX 요청을 처리하는 뷰
from django.views.decorators.csrf import csrf_protect
from django.http import JsonResponse

@csrf_protect
def create_post_ajax(request):
    if request.method == 'POST':
        form = PostForm(request.POST)
        if form.is_valid():
            post = form.save()
            return JsonResponse({'status': 'success', 'id': post.id})
        else:
            return JsonResponse({'status': 'error', 'errors': form.errors})
```

## Django의 Form 인스턴스 속성을 추가하는 방법

때때로 Django Form의 `clean()`, `clean_FOO()`, `save()` 메서드에 추가로 폼 인스턴스 속성이 필요할 때가 있습니다. 이럴 경우 `request.user` 객체를 이용하면 됩니다.

```python
class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content']
    
    def __init__(self, *args, **kwargs):
        # request.user를 폼에 전달
        self.user = kwargs.pop('user', None)
        super().__init__(*args, **kwargs)
    
    def clean_title(self):
        title = self.cleaned_data['title']
        # user 정보를 활용한 유효성 검사
        if self.user and Post.objects.filter(
            author=self.user, title=title
        ).exists():
            raise forms.ValidationError('이미 같은 제목의 글이 있습니다.')
        return title
    
    def save(self, commit=True):
        instance = super().save(commit=False)
        if self.user:
            instance.author = self.user
        if commit:
            instance.save()
        return instance

# 뷰에서 사용
def create_post(request):
    if request.method == 'POST':
        form = PostForm(request.POST, user=request.user)  # user 전달
        if form.is_valid():
            form.save()
            return redirect('post_list')
    else:
        form = PostForm(user=request.user)
    return render(request, 'posts/create.html', {'form': form})
```

## Form이 유효성을 검사하는 방법 알아두기

`form.is_valid()`가 호출될 때 다음과 같은 순서로 여러 일이 진행됩니다:

### 1단계: form.full_clean() 호출
폼이 데이터를 받으면 `form.is_valid()`는 `form.full_clean()` 메서드를 호출합니다.

### 2단계: 각 필드 유효성 검사
`form.full_clean()`은 폼 필드들과 각각의 필드 유효성을 하나하나 검사하면서 다음과 같은 과정을 수행합니다:

- **a.** 필드에 들어온 데이터에 대해 `to_python()`을 이용하여 Python 형식으로 변환하거나 변환할 때 문제가 생기면 `ValidationError`를 일으킵니다
- **b.** 커스텀 유효성 검사기(validator)를 포함한 각 필드에 특별한 유효성을 검사합니다. 문제가 있을 때 `ValidationError`를 일으킵니다
- **c.** 폼에 `clean_<field>()` 메서드가 있으면 이를 실행합니다

### 3단계: form.clean() 실행
`form.full_clean()`이 `form.clean()` 메서드를 실행합니다.

### 4단계: ModelForm의 경우 추가 처리
ModelForm 인스턴스의 경우 `form.post_clean()`이 다음 작업을 합니다:

- **a.** `form.is_valid()`가 True나 False로 설정되어 있는 것과 관계없이 ModelForm의 데이터를 모델 인스턴스로 설정합니다
- **b.** 모델의 `clean()` 메서드를 호출합니다. 참고로 ORM을 통해 모델 인스턴스를 저장할 때는 모델의 `clean()` 메서드가 호출되지 않습니다

```python
# 유효성 검사 과정 예시
class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content', 'email']
    
    def clean_title(self):
        # 2c단계: 개별 필드 검사
        title = self.cleaned_data['title']
        if len(title) < 5:
            raise forms.ValidationError('제목은 5글자 이상이어야 합니다.')
        return title
    
    def clean(self):
        # 3단계: 전체 폼 검사
        cleaned_data = super().clean()
        title = cleaned_data.get('title')
        content = cleaned_data.get('content')
        
        if title and content and title in content:
            raise forms.ValidationError('제목이 내용에 포함되어서는 안 됩니다.')
        
        return cleaned_data

# 모델에서의 clean 메서드
class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    
    def clean(self):
        # 4b단계: 모델 레벨 검사
        if self.title and self.title.lower() == 'test':
            raise ValidationError('제목으로 "test"는 사용할 수 없습니다.')
```

이러한 과정은 복잡해 보이지만 실제로는 매우 단순하며 여기서의 모든 기능이 입력된 데이터가 어떻게 처리되는지 좀 더 쉽게 이해시켜주는 역할을 한다는 것을 기억해야 합니다.

## 요약

Django Form은 웹 애플리케이션의 보안과 데이터 무결성을 보장하는 핵심 도구이므로, 모든 사용자 입력에 대해 적절한 Form을 설계하고 활용하는 것이 중요합니다.