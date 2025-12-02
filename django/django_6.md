# Django 클래스 기반 뷰의 모범적인 이용

Two Scoops of Django 10장 "클래스 기반 뷰의 모범적인 이용"을 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

Django는 클래스 기반 뷰를 작성하는 표준화된 방법을 제공합니다. 함수 기반 뷰에서는 뷰 함수 자체가 호출 가능한 함수이고, 클래스 기반 뷰에서는 뷰 클래스가 호출 가능한 함수를 반환하는 `as_view()` 클래스 메서드를 제공합니다.

```python
# 함수 기반 뷰
def post_list(request):
    return render(request, 'posts/list.html')

# 클래스 기반 뷰
class PostListView(ListView):
    model = Post
    template_name = 'posts/list.html'

# urls.py에서 사용
urlpatterns = [
    path('posts/', PostListView.as_view(), name='post_list'),
]
```

`django.views.generic.View`에서 해당 메커니즘이 구현되며 모든 클래스 기반 뷰는 이 클래스를 직간접적으로 상속받아 사용합니다.

또한, Django는 대부분의 웹 프로젝트에서 활용되는 **제네릭 클래스 기반 뷰(Generic Class-Based View, GCBV)**를 제공합니다.

## 클래스 기반 뷰를 사용할 때의 가이드라인

- 뷰 코드의 양은 적으면 적을수록 좋다
- 뷰 안에서 같은 코드를 반복적으로 이용하지 말자
- 뷰는 프레젠테이션 로직에서 관리하고 비즈니스 로직은 모델에서 처리하자, 매우 특별한 경우에는 폼에서 처리하자
- 뷰는 간단 명료해야 한다
- 403, 404, 500 에러 핸들링에는 클래스 기반 뷰를 이용하지 않는다. 대신 함수 기반 뷰를 이용한다
- 믹스인은 간단 명료해야 한다

```python
# ✅ 좋은 예 - 간단한 CBV
class PostListView(ListView):
    model = Post
    template_name = 'posts/list.html'
    context_object_name = 'posts'
    paginate_by = 10

# ❌ 나쁜 예 - 복잡한 로직이 포함된 CBV
class ComplexPostView(ListView):
    def get_queryset(self):
        # 복잡한 비즈니스 로직 (모델로 이동해야 함)
        # 100줄의 복잡한 쿼리 로직...
        pass
```

## 클래스 기반 뷰와 믹스인 사용하기

**믹스인**이란 실체화된 클래스가 아니라 상속해 줄 기능들을 제공하는 클래스를 의미합니다. 다중 상속을 해야 할 때 믹스인을 쓰면 클래스에 더 나은 기능과 역할을 제공할 수 있습니다.

믹스인을 이용해서 뷰 클래스를 제작할 때 Kenneth Love가 제안한 상속에 관한 규칙을 따라야 합니다:

- Django가 제공하는 기본 뷰는 **'항상' 오른쪽**으로 진행한다
- 믹스인은 기본 뷰에서부터 **왼쪽**으로 진행한다
- 믹스인은 Python의 기본 객체 타입을 상속해야만 한다

```python
# 올바른 믹스인 순서
class PostCreateView(LoginRequiredMixin, CreateView):  # 믹스인이 왼쪽
    model = Post
    fields = ['title', 'content']

# 커스텀 믹스인 예시
class TitleMixin:
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['page_title'] = getattr(self, 'page_title', '')
        return context

class PostListView(TitleMixin, ListView):
    model = Post
    page_title = "게시글 목록"
```

## 제네릭 클래스 뷰의 사용 목적

| 이름 | 목적 | 예시 |
| --- | --- | --- |
| View | 어디서든 이용 가능한 기본 뷰 | API 엔드포인트 |
| RedirectView | 사용자를 다른 URL로 리다이렉트 | 구 URL → 신 URL |
| TemplateView | Django HTML 템플릿을 보여줄 때 | 정적 페이지 |
| ListView | 객체 목록 | 게시글 목록 |
| DetailView | 객체를 보여줄 때 | 게시글 상세 |
| FormView | 폼 전송 | 연락처 폼 |
| CreateView | 객체를 만들 때 | 게시글 작성 |
| UpdateView | 객체를 업데이트할 때 | 게시글 수정 |
| DeleteView | 객체를 삭제할 때 | 게시글 삭제 |
| Date Views | 시간 순서로 객체를 나열해 보여줄 때 | 블로그 아카이브 |

```python
# 각 뷰의 간단한 사용 예시
class HomeView(TemplateView):
    template_name = 'home.html'

class PostListView(ListView):
    model = Post
    template_name = 'posts/list.html'

class PostDetailView(DetailView):
    model = Post
    template_name = 'posts/detail.html'

class PostCreateView(CreateView):
    model = Post
    fields = ['title', 'content']
    success_url = reverse_lazy('post_list')
```

## Django 클래스 기반 뷰에 대한 일반적인 팁

### 인증된 사용자에게만 클래스 기반 뷰 접근 가능하게 하기

Django 클래스 기반 뷰 문서가 `django.contrib.auth.decorators.login_required` 데코레이터와 클래스 기반 뷰를 이용하는 데 도움이 되긴 하지만, 너무 정형화된 틀에 박혀 있습니다. 

현재는 Django 내장 믹스인을 사용할 수 있습니다:

```python
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    fields = ['title', 'content']
    login_url = '/login/'  # 로그인 페이지 URL

class PostUpdateView(PermissionRequiredMixin, UpdateView):
    model = Post
    fields = ['title', 'content']
    permission_required = 'blog.change_post'
```

### 뷰에서 유효한 폼을 이용하여 커스텀 액션 구현하기

뷰에서 폼의 유효성 검사를 할 때 커스텀 액션을 구현하고자 한다면, `form_valid()`는 제네릭 클래스 기반 뷰가 요청을 보내는 곳에 자리잡게 됩니다.

```python
class PostCreateView(CreateView):
    model = Post
    fields = ['title', 'content']
    
    def form_valid(self, form):
        # 커스텀 로직 추가
        form.instance.author = self.request.user
        messages.success(self.request, '게시글이 성공적으로 생성되었습니다.')
        return super().form_valid(form)
```

### 뷰에서 부적절한 폼을 이용하여 커스텀 액션 구현하기

뷰에서 폼의 부적합성 검사를 할 때 커스텀 액션을 구현하고자 한다면, `form_invalid()`는 제네릭 클래스 기반 뷰가 요청을 보내는 곳에 자리잡게 됩니다.

```python
class PostCreateView(CreateView):
    model = Post
    fields = ['title', 'content']
    
    def form_invalid(self, form):
        # 커스텀 에러 처리
        messages.error(self.request, '입력한 정보를 다시 확인해주세요.')
        return super().form_invalid(form)
```

### 뷰 객체 이용하기

콘텐츠를 렌더링하는 데 클래스 기반 뷰를 이용한다면 자체적인 메서드와 속성을 제공하는 뷰 객체를 이용하여 다른 메서드나 속성에서 호출 가능하게 하는 방법을 고려해 볼 수 있습니다.

```python
class PostListView(ListView):
    model = Post
    template_name = 'posts/list.html'
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        # 뷰 객체의 메서드 활용
        context['featured_posts'] = self.get_featured_posts()
        context['categories'] = self.get_categories()
        return context
    
    def get_featured_posts(self):
        return Post.objects.filter(is_featured=True)[:5]
    
    def get_categories(self):
        return Category.objects.all()
```

## 제네릭 클래스 기반 뷰와 폼 사용하기

### 뷰 + 모델폼

가장 단순하고 일반적인 Django 폼 시나리오입니다. **데이터베이스 모델과 직접 연결된 폼**으로, 모델에 새로운 레코드를 추가하거나 기존 레코드를 수정하는 기능들입니다.

**ModelForm을 사용하는 경우:**
- 데이터베이스에 직접 저장하는 폼
- 모델의 필드와 1:1 대응되는 폼
- CRUD 작업 (생성, 조회, 수정, 삭제)

```python
# ModelForm 사용 - 데이터베이스에 직접 저장
class PostCreateView(CreateView):
    model = Post
    fields = ['title', 'content', 'category']  # 모델 필드와 직접 연결
    template_name = 'posts/create.html'
    success_url = reverse_lazy('post_list')

class PostUpdateView(UpdateView):
    model = Post
    fields = ['title', 'content', 'category']
    template_name = 'posts/update.html'
    
    def get_success_url(self):
        return reverse('post_detail', kwargs={'pk': self.object.pk})

# 커스텀 ModelForm 사용
class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content', 'category']
        widgets = {
            'content': forms.Textarea(attrs={'rows': 10}),
        }

class PostCreateView(CreateView):
    model = Post
    form_class = PostForm  # 커스텀 ModelForm 사용
    template_name = 'posts/create.html'
```

### 뷰 + 폼

**데이터베이스 모델과 직접 연결되지 않는 폼**이 필요한 경우가 있습니다. 주로 계산, 검색, 이메일 발송 등 데이터 처리가 목적인 경우입니다.

**일반 Form을 사용하는 경우:**
- 데이터베이스에 저장하지 않는 폼 (검색, 계산, 이메일 등)
- 여러 모델의 데이터를 조합하는 폼
- 복잡한 유효성 검사가 필요한 폼
- 외부 API와 연동하는 폼

```python
# 일반 Form 사용 - 이메일 발송용
class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    subject = forms.CharField(max_length=200)
    message = forms.CharField(widget=forms.Textarea)

class ContactView(FormView):
    template_name = 'contact.html'
    form_class = ContactForm
    success_url = reverse_lazy('contact_success')
    
    def form_valid(self, form):
        # 이메일 전송 (데이터베이스 저장 X)
        send_mail(
            form.cleaned_data['subject'],
            form.cleaned_data['message'],
            form.cleaned_data['email'],
            ['admin@example.com'],
        )
        return super().form_valid(form)

# 검색 폼 예시
class SearchForm(forms.Form):
    query = forms.CharField(max_length=200)
    category = forms.ModelChoiceField(
        queryset=Category.objects.all(),
        required=False
    )
    date_from = forms.DateField(required=False)
    date_to = forms.DateField(required=False)

class SearchView(FormView):
    template_name = 'search.html'
    form_class = SearchForm
    
    def form_valid(self, form):
        # 검색 결과 처리 (데이터베이스 저장 X)
        query = form.cleaned_data['query']
        results = Post.objects.filter(title__icontains=query)
        
        # 검색 결과를 템플릿에 전달
        context = self.get_context_data(form=form)
        context['results'] = results
        return self.render_to_response(context)
```

## django.views.generic.View 이용하기

모든 뷰에서 `django.views.generic.View`만 이용하여 Django 프로젝트 전부를 구성할 수 있습니다. `django.views.generic.View` 클래스를 GET 메서드와 함께 JSON, PDF 또는 다른 비HTML 콘텐츠를 서비스하기 위해 이용한 경우, 매우 유용합니다.

```python
from django.views.generic import View
from django.http import JsonResponse
import json

class PostAPIView(View):
    def get(self, request, *args, **kwargs):
        posts = Post.objects.all().values('title', 'content', 'created_at')
        return JsonResponse(list(posts), safe=False)
    
    def post(self, request, *args, **kwargs):
        data = json.loads(request.body)
        post = Post.objects.create(
            title=data['title'],
            content=data['content'],
            author=request.user
        )
        return JsonResponse({'id': post.id, 'title': post.title})

class PDFReportView(View):
    def get(self, request, *args, **kwargs):
        # PDF 생성 로직
        response = HttpResponse(content_type='application/pdf')
        response['Content-Disposition'] = 'attachment; filename="report.pdf"'
        # PDF 생성 코드...
        return response
```

많은 커스텀 로직을 처리하고 더 많은 믹스인과 효과를 내야하는 상황이라도 `django.views.generic.View`가 주는 간단 명료함 덕분에 더 복잡한 뷰를 훨씬 간단하게 구성할 수 있습니다.

## 요약 및 베스트 프랙티스

### ✅ CBV 사용 시 해야 할 것들

- **간단하고 명료한 뷰** 작성
- **믹스인을 활용한 코드 재사용**
- **올바른 상속 순서** 준수 (믹스인 → 제네릭 뷰)
- **비즈니스 로직을 모델로 분리**
- **적절한 제네릭 뷰** 선택

### ❌ 피해야 할 것들

- **복잡한 로직을 뷰에 포함**
- **에러 핸들링을 CBV로 처리**
- **잘못된 믹스인 순서**
- **불필요하게 복잡한 믹스인** 작성

클래스 기반 뷰는 코드 재사용성과 확장성을 제공하지만, 단순함을 유지하고 적절한 추상화 수준을 지키는 것이 중요합니다.