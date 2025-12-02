# Form 패턴들

Two Scoops of Django 12장 "Form 패턴들"을 현재 Django 버전에 맞춰 정리하고 보완한 내용입니다.

Django Form은 강력한 동시에 유연하며 자유자재로 확장 가능한 구조로 되어 있습니다. 덕분에 Django Form은 Django admin과 클래스 기반 뷰에서 광범위하게 이용되고 있으며 대부분의 Django API 프레임워크에서 **ModelForm** 또는 그와 비슷한 종류의 구현이 유효성 검사를 위해 쓰이고 있습니다.

## 패턴 1: 간단한 ModelForm과 기본 유효성 검사기

Django에서는 기본으로 데이터 유효성 검사기를 제공합니다. 하지만 기본 유효성 검사기만으로는 충분하다고 보기 어렵습니다.

```python
# 기본 ModelForm 사용
class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content', 'email']
        widgets = {
            'content': forms.Textarea(attrs={'rows': 5}),
        }

# 기본 유효성 검사기 활용
class UserForm(forms.ModelForm):
    class Meta:
        model = User
        fields = ['username', 'email', 'age']
    
    # 필드 레벨에서 기본 검사 활용
    age = forms.IntegerField(
        min_value=0, 
        max_value=120,
        help_text="0-120 사이의 나이를 입력하세요"
    )
    
    email = forms.EmailField(
        help_text="유효한 이메일 주소를 입력하세요"
    )
```

## 패턴 2: ModelForm에서 커스텀 폼 필드 유효성 검사기 이용하기

Django에서 커스텀 필드 유효성 검사기는 입력된 인자들이 테스트를 통과하지 못했을 경우 에러를 일으키는 간단한 함수로 되어 있습니다.

```python
from django.core.exceptions import ValidationError

# 커스텀 유효성 검사 함수
def validate_positive_number(value):
    if value <= 0:
        raise ValidationError('양수만 입력 가능합니다.')

def validate_korean_name(value):
    import re
    if not re.match(r'^[가-힣]+$', value):
        raise ValidationError('한글 이름만 입력 가능합니다.')

# ModelForm에서 커스텀 검사기 사용
class ProductForm(forms.ModelForm):
    class Meta:
        model = Product
        fields = ['name', 'price', 'owner_name']
    
    # 필드별 커스텀 검사기 적용
    price = forms.DecimalField(
        validators=[validate_positive_number],
        max_digits=10, 
        decimal_places=2
    )
    
    owner_name = forms.CharField(
        max_length=50,
        validators=[validate_korean_name]
    )

# 클래스 기반 검사기
class MinLengthValidator:
    def __init__(self, min_length):
        self.min_length = min_length
    
    def __call__(self, value):
        if len(value) < self.min_length:
            raise ValidationError(
                f'최소 {self.min_length}글자 이상 입력해야 합니다.'
            )

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content']
    
    title = forms.CharField(
        validators=[MinLengthValidator(5)]
    )
```

## 패턴 3: 유효성 검사의 clean 메서드 오버라이딩하기

다음과 같이 유효성 검사를 해야 하는 경우 모두 커스텀 로직으로 `clean()` 또는 `clean_<field_name>()` 메서드를 오버라이딩할 수 있는 최적의 경우입니다:

- **다중 필드에 대한 유효성 검사**
- **이미 유효성 검사가 끝난 데이터베이스의 데이터가 포함된 유효성 검사**

기본 또는 커스텀 필드 유효성 검사기가 실행된 후, Django는 다음 과정으로 `clean()` 메서드 또는 `clean_<field_name>()` 메서드를 이용하여 입력된 데이터의 유효성을 검사하는 절차를 진행합니다.

### clean() 메서드 활용

`clean()` 메서드는 어떤 특별한 필드에 대한 정의도 가지고 있지 않기 때문에 **두 개 또는 그 이상의 필드들에 대해 서로 간의 유효성을 검사하는 공간**이 됩니다.

```python
class EventForm(forms.ModelForm):
    class Meta:
        model = Event
        fields = ['title', 'start_date', 'end_date', 'price', 'discount_price']
    
    def clean(self):
        cleaned_data = super().clean()
        start_date = cleaned_data.get('start_date')
        end_date = cleaned_data.get('end_date')
        price = cleaned_data.get('price')
        discount_price = cleaned_data.get('discount_price')
        
        # 다중 필드 검사 1: 날짜 유효성
        if start_date and end_date and start_date >= end_date:
            raise forms.ValidationError(
                '시작일은 종료일보다 빨라야 합니다.'
            )
        
        # 다중 필드 검사 2: 가격 유효성
        if price and discount_price and discount_price >= price:
            raise forms.ValidationError(
                '할인가는 정가보다 낮아야 합니다.'
            )
        
        return cleaned_data
```

### clean_<field_name>() 메서드 활용

clean 유효성 검사 상태는 **영속 데이터(persistent data)에 대해 유효성을 검사하기에 좋은 장소**입니다. 이미 유효성 검사를 일부 마친 데이터에 대해 불필요한 데이터베이스 연동을 줄일 수 있습니다.

```python
class UserRegistrationForm(forms.ModelForm):
    class Meta:
        model = User
        fields = ['username', 'email', 'phone']
    
    def clean_username(self):
        username = self.cleaned_data['username']
        
        # 데이터베이스 중복 검사
        if User.objects.filter(username=username).exists():
            raise forms.ValidationError(
                '이미 사용 중인 사용자명입니다.'
            )
        
        # 추가 검사
        if username.lower() in ['admin', 'root', 'system']:
            raise forms.ValidationError(
                '사용할 수 없는 사용자명입니다.'
            )
        
        return username
    
    def clean_email(self):
        email = self.cleaned_data['email']
        
        # 데이터베이스 중복 검사
        if User.objects.filter(email=email).exists():
            raise forms.ValidationError(
                '이미 등록된 이메일입니다.'
            )
        
        return email
```

## 패턴 4: 폼 필드 동적 수정하기 (두 개의 CBV, 두 개의 폼, 한 개의 모델)

하나의 모델에 두 개의 뷰와 폼이 엮여 있는 경우 이를 위해 Django 폼을 수정하여 특별한 동작(custom behavior)을 하는 특수한 폼을 생성합니다.

Django Form을 사용할 때 반드시 다음 사항을 기억해야 합니다: **실체화된 폼 객체는 유사 딕셔너리 객체인 fields 속성 안에 그 필드들을 저장합니다.** 따라서 Form으로 필드의 정의를 복사, 붙이기 하는 대신에 간단하게 ModelForm의 `__init__()` 메서드에서 새로운 속성을 적용하면 됩니다.

```python
# 하나의 모델
class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    is_published = models.BooleanField(default=False)
    tags = models.CharField(max_length=500, blank=True)

# 기본 폼
class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ['title', 'content', 'tags']

# 작성자용 폼 - 더 많은 필드
class AuthorArticleForm(ArticleForm):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        # 동적으로 필드 수정
        self.fields['is_published'] = forms.BooleanField(
            required=False,
            label='바로 발행하기'
        )
        self.fields['content'].widget.attrs.update({
            'rows': 15,
            'class': 'advanced-editor'
        })

# 관리자용 폼 - 모든 권한
class AdminArticleForm(ArticleForm):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        # 관리자 전용 필드 추가
        self.fields['author'] = forms.ModelChoiceField(
            queryset=User.objects.filter(is_active=True),
            label='작성자 변경'
        )
        self.fields['is_published'] = forms.BooleanField(
            required=False,
            label='발행 상태'
        )

# 뷰에서 사용
class AuthorArticleCreateView(CreateView):
    model = Article
    form_class = AuthorArticleForm
    
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

class AdminArticleCreateView(CreateView):
    model = Article
    form_class = AdminArticleForm
```

결국 중요한 점은 **Django Form도 결국 Python 클래스이고 또한 객체로 실체화되고 슈퍼클래스가 되어 다른 클래스를 상속하기도 한다**는 것입니다.

## 패턴 5: 재사용 가능한 검색 믹스인 뷰

검색 기능은 많은 뷰에서 공통으로 필요한 기능입니다. 이를 재사용 가능한 믹스인으로 만들어 활용할 수 있습니다.

```python
# 검색 폼
class SearchForm(forms.Form):
    q = forms.CharField(
        max_length=200,
        widget=forms.TextInput(attrs={
            'placeholder': '검색어 입력',
            'class': 'form-control'
        }),
        label='검색'
    )

# 검색 믹스인
class SearchMixin:
    search_fields = []  # 하위 클래스에서 정의
    
    def get_queryset(self):
        queryset = super().get_queryset()
        query = self.request.GET.get('q')
        
        if query and self.search_fields:
            from django.db.models import Q
            search_query = Q()
            
            for field in self.search_fields:
                search_query |= Q(**{f'{field}__icontains': query})
            
            queryset = queryset.filter(search_query)
        
        return queryset
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['search_form'] = SearchForm(
            initial={'q': self.request.GET.get('q', '')}
        )
        return context

# 실제 뷰에서 믹스인 활용
class PostListView(SearchMixin, ListView):
    model = Post
    template_name = 'posts/list.html'
    search_fields = ['title', 'content']  # 검색할 필드 지정
    paginate_by = 10

class UserListView(SearchMixin, ListView):
    model = User
    template_name = 'users/list.html'
    search_fields = ['username', 'first_name', 'last_name']
    paginate_by = 20

# 더 고급 검색 믹스인
class AdvancedSearchMixin(SearchMixin):
    date_field = None  # 날짜 필터링할 필드
    
    def get_queryset(self):
        queryset = super().get_queryset()
        
        # 날짜 범위 필터링
        date_from = self.request.GET.get('date_from')
        date_to = self.request.GET.get('date_to')
        
        if self.date_field and date_from:
            queryset = queryset.filter(**{f'{self.date_field}__gte': date_from})
        
        if self.date_field and date_to:
            queryset = queryset.filter(**{f'{self.date_field}__lte': date_to})
        
        return queryset

class ArticleListView(AdvancedSearchMixin, ListView):
    model = Article
    search_fields = ['title', 'content']
    date_field = 'created_at'
```

## 요약
Django Form의 유연성과 확장성을 활용하면 복잡한 요구사항도 깔끔하게 해결할 수 있습니다. 각 패턴을 상황에 맞게 조합하여 사용하는 것이 중요합니다.