# Django 중간점검 (~ Ch5. Django Form)

<br>

목표: windows 기준, 마지막까지 갈 즈음에는 처음부터 끝까지 내 힘으로 ubuntu를 통해 웹을 만들고자 한다.

<br>

현재는 내 복습용

출발

<br>

## [기본 사항]

<br>

<br>

### 1. Django setup

<br>

```python
1. Python 설치

2. 가상환경 진입
 ex) C:\venvs> python -m venv (virtual environment name)
     C:\venvs\mysite\Scripts> activate

3. 가상환경 내 django 설치
 ex) pip install django == 3.1.x, python -m pip install --upgrade pip
```

<br>

### 2. Django project 생성

<br>

```python
1. project를 모아둘 root directory 생성 (ex. C:\Dev)

2. django-admin startproject (project name) ex. myproject
 - project name으로 생성된 directory, 다른 이름으로 변경 가능하다.

3. python -m django startproject (project name) ex. myproject
 - 코드 중 해당 project name을 참조하고 있는 코드가 있기에 이름 변경 불가능

# 여기까지 완료할 경우,
Dev/myproject 밑에는
a. manage.py
b. myproject(dir)
  - __init__.py; package import 시 import 대상
  - settings.py; django 기본 설정을 덮어쓰고, 새롭게 지정할 설정들
  - urls.py; 최상위 url 설정
  - wsgi.py; 실서비스에서의 웹 서비스 진입점
```

<br>

### 3.  프로젝트 초기화 , 개발서버 구동

<br>

```python
C:\Dev\myproject로 이동 (manage.py 있는 곳)

1. python manage.py makemigrations
2. python manage.py migrate
3. python manage.py createsuperuser
4. python manage.py runserver

이후
http://localhost:8000/admin/
```

<br>

### 4. 새로운 앱 만들기

<br>

- 장고 앱은 재사용성을 목적으로 한 파이썬 package(소스코드 dir)
- 하나의 앱 이름은 현재 project 상에서 유일해야 한다.

<br>

```python
1. python manage.py startapp (app name)

2. settings.py 내 installed_apps에 (app name) 추가

3. 해당 app 내 urls.py 만들기
 - 여기서 쓰는 urlpattern에서는 path에 views 함수를 요청하라고 적힘

4. 최상위 urls.py(ex. myproject) 내 urlpatterns에 path 등록 (include 적용해서)
 - ex) path('pybo/', include('pybo.urls'))의 경우,
 pybo/로 시작되는 모든 page 요청을 모두 pybo/urls.py 내 있는 URL 매핑을 참고해 처리하라.
 - 순차적으로 mapping list를 훑으며 URL 매칭을 하는데, 없을 경우 404 PageNotFound 에러.
 - 참고로 settings.py 내 root.urlconf에 최상위 모듈 있음
```

<br>

### 5. Model 만들기 (Django 내장 ORM인 Django Model 이용)

<br>

- DB 테이블명: default "app name_model name"

<br>

```python
1. model class 작성

2. python manage.py makemigrations (app name)
 - model class로부터 migration file 생성

3. python manage.py migrate (app name)
 - migration file을 database에 적용

4. model 활용
```

<br>

<br>

<br>

## [추가 사항들]

<br>

### 1. Django admin

<br>

- model class 등록을 통해, 조회/추가/수정/삭제 웹 UI를 제공

<br>

```python
[model class를 admin에 등록하기]

위치: app/admin.py

from django.contrib import admin
from .models import Item

@admin.register(Item)
class ItemAdmin(admin.ModelAdmin):
	pass

# 추후 여러 속성들을 ItemAdmin 클래스 밑에 추가할 수 있음
```

<br>

- 여기서 그럼 admin.py에서 작성한 뒤에는 저장만 하면 끝인가?????

<br>

<br>

### 2. Django View

<br>

- Function Based View, Class Based View가 있음

<br>

```python
FBV의 예

from django.shortcuts import get_object_or_404, render


def post_detail(request: HttpRequest, id) -> HttpResponse:
    post = get_object_or_404(Post, id=id)
    return render(request, 'blog/post_detail.html', {
		'post': post,
	})
```

- get_object_or_404의 첫 번째 인자는 django model 이름을 가리키고, 두 번째 인자는 키워드를 전달 받아서 model manager의 get 함수에 전달된다. 이 때, id에 해당하는 건이 없으면 404 에러를 반환한다.
- render의 경우, 첫 번째 인자는 request 객체를 의미한다. 그 request를 받으면 context에 있는 post 모델 데이터 post를 'blog/post_detail.html' (템플릿) 파일에 적용해 HTML 코드로 변환한다.
  - 'post'는 template에서 사용할 키, 우측 post는 전달할 data이다.
- http://pythonstudy.xyz/python/article/307-Django-%ED%85%9C%ED%94%8C%EB%A6%BF-Template



<br>

<br>

#### [CBV API (Base Views)]

<br>

- View
- TemplateView ; 아래의 3개 class를 상속 받음
  - TemplateResponseMixin (Class)
  - ContextMixin (Class)
  - View (Class)

<br>

```python
[myproject/urls.py]

from django.views.generic import TemplateView

urlpatterns = [
    path('', TemplateView.as_view(template_name='root.html'), name='root'),
]

# template name 부분이 **kwargs, template_name이 key, root_html이 value

# 추후 RedirectView 이용하여 재변경 (localhost:8000 누르면 /instagram/으로 바로 넘어감)

from django.views.generic import TemplateView, RedirectView

urlpatterns = [
    path('', RedirectView.as_view(url='/instagram/'), name='root'),
]

혹은

urlpatterns = [
    path('', RedirectView.as_view(pattern_name='instagram:post_list'), name='root'),
]


[settings.py]
TEMPLATES = [
    {
        'DIRS': [
            os.path.join(BASE_DIR, 'myproject', 'templates')
        ]
    }
]


[myproject/templates/root.html]

Instagram.
```

<br>

<br>

- RedirectView
  - View (Class)

<br>

```python
- pattern name(url reverse를 수행할 문자열)

[instagram/urls.py]

path('', views.post_list, name='post_list')  # 이 name 부분이 pattern_name
```

<br>

<br>

#### [CBV API (Generic display views)]

<br>

- DetailView ; 1개 모델의 1개 object에 대한 template 처리
  - SingleObjectTemplateResponseMixin
    - TemplateResponseMixin
  - BaseDetailView
    - SingleObjectMixin
    - View

<br>

```python
[DetailView]
- 모델명 '소문자' 이름의 Model instance를 template에 전달함
- template_name이 지정되지 않았다면 모델명으로부터 template 경로 유추  # 그냥 냅둬라


post_detail = DetailView.as_view(
    model=Post,
    queryset=Post.objects.filter(is_public=True))

혹은


class PostDetailView(DetailView):
    model = Post
    # queryset = Post.objects.filter(is_public=True)
    
    def get_queryset(self):
        qs = super().get_queryset()  # super()는 재정의시 사용
        if not self.request.user.is_authenticated:  # 로그인 안 되어 있으면
            qs = qs.filter(is_public=True)  # 공개된 것만 봐라
        return qs
    
post_detail = PostDetailView.as_view()
```

<br>

- ListView
  - MultipleObjectTemplateResponseMixin; template_name 미지정 시 모델명으로 템플릿 경로 유추함
    - TemplateResponseMixin
  - BaseListMixin
    - MultipleObjectMixin; Paginator가 적용된 쿼리셋 획득
      - ContextMixin
    - View

<br>

```python
[ListView]
- 페이징 처리 지원


post_list = ListView.as_view(model=Post, paginate_by=10)

혹은

class PostListView(ListView):
    model = Post
    paginate_by = 10
    
post_list3 = PostListView.as_view()

혹은

class PostListView(ListView):
    model = Post
    paginate_by = 10
    
    def get_queryset(self):
        qs = super().get_queryset()
        qs = qs.filter(...)
        return qs
    
post_list4 = PostListView.as_view()


[instagram/templates/instagram/post_list.html]

# 중략
</table>

{{ is_paginated }}
{{ page_obj }}  # 몇 페이지인지 나옴


혹은 bootstrap 사용

{% load bootstrap4 %}  # 맨 위

# 중략

{% if is_paginated %}
	{% bootstrap_pagination page_obj size="small" justify_content="center" %}  
    # 페이징 처리, 페이징 부분 중간으로 이동
{% endif %}

```

<br>

<br>

#### [CBV API (Generic date views)]

<br>

- 날짜 관련 filtering

```python
[instagram/views.py]

from django.views.generic import ArchiveIndexView

post_archive = ArchiveIndexView.as_view(
    model=Post, 
    date_field='created_at', 
    pagninate_by=10)

post_archive_year = YearArchiveView.as_view(
    model=Post, 
    date_field='created_at',
	make_object_list=True)


[instagram/urls.py]

urlpatterns = [
    path('archive/', views.post_archive, name='post_archive'),
    path('archive/<year:year>/', views.post_archive_year, name='post_archive_year'),
]


[instagram/template/instagram/post_archive.html]

<h2>latest</h2>

{{ latest }}

<h2>date_list</h2>

{% for date in date_list %}
	{{ date.year }}  
    # 혹은 {{ date|date:"Y"}}
    # |는 장고에서 filter 역할
{% endfor %}


[instagram/template/instagram/post_archive_year.html]

{{ year }}

{{ previous_year }}

{{ next_year }}

{{ date_list }}

{{ object_list }}
```

<br>

<br>

#### [CBV API (Generic editing views)]

<br>

- FormView
- CreateView
- UpdateView
- DeleteView

<br>

```python
[def post_new(request) 부분 CBV로] [instagram/views.py]

from django.views.generic import CreateView, UpdateView, DeleteView

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    form_class = PostForm
    
    def form_valid(self, form):
        self.object = form.save(commit=False)
        self.object.author = self.request.user
        messages.success(self.request, '포스팅을 저장했습니다.')
        return super().form_valid(form)
    
post_new = PostCreateView.as_view()


class PostUpdateView(LoginRequiredMixin, UpdateView):
    model = Post
    form_class = PostForm
    
    def form_valid(self, form):
        messages.success(self.request, '포스팅을 수정했습니다.')
        return super().form_valid(form)
    
    
post_edit = PostUpdateView.as_view()


class PostDeleteView(LoginRequiredMixin, DeleteView):
    model = Post
    success_url = '/instagram/'  # 여기로 넘어감
    # success_url = reverse('instagram:post_list'), but 오류
    # success_url = reverse_lazy('instagram:post_list') 시 reverse를 success_url이 실	제 사용될 때 reverse 실행
    # def get_success_url(self):
        # return reverse('instagram:post_list')
    
post_delete = PostDeleteView.as_view()
```

<br>

<br>

#### [View Decorator (어떤 함수를 감싸는 함수)]

```python
[기본 Decorator]

1. django.views.decorators.http

- require_http_methods
- require_GET
- require_POST
- require_safe

2. django.contrib.auth.decorators

- user_passes_test (ex. 회원 등급 관련)
- login_required
- permission_required

3. django.contrib.admin.views.decorators

- staff_member_required
```

<br>

- CBV에 장식자 입히는 법

```python
1. 요청을 처리하는 함수에 장식자 입히기

[예시]

from django.contrib.auth.decorators import login_required
from django.views.generic import TemplateView


class SecretView(TemplateView)
	template_name = 'myapp/secret.html'

view_fn = SecretView.as_view()  # 여기까지 CBV

secret_view = login_required(view_fn)  # 장식자 입힘


2. View 클래스 내 dispatch 재정의하는 방식으로 장식자 입히기

[예시]

from django.contrib.auth.decorators import login_required
from django.util.decorators import method_decorator
from django.views.generic import TemplateView


class SecretView(TemplateView):
	template_name = 'myapp/secret.html'

    # 클래스 내 함수(즉, 멤버함수)에는 method_decorator를 활용해 장식자 입힌다.
	@method_decorator(login_required)
	def dispatch(self, *args, **kwargs):
		return super().dispatch(*args, **kwargs)

secret_view = SecretView.as_view()


3. 클래스에 직접 장식자 입히기

[예시]

from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decorator
from django.views.generic import TemplateView


# 클래스에 직접 적용, name에는 어떤 멤버함수에 적용할 것인가 적어주기
@method_decorator(login_required, name='dispatch')
class SecretView(TemplateView):
    template_name = 'myapp/secret.html'
    
secret_view = SecretView.as_view()
```

<br>

<br>

#### [URL Reverse]

```python
[URL Reverse 하는 4가지 함수]

1) reverse 함수
- 매핑 URL 없으면 NoReverseMatch 예외 발생
# instagram: 이 부분은 namespace 역할, instagram/urls.py에 app_name 지정함
ex) reverse('instagram:post_list', args=[100])
ex) reverse('instagram:post_list', kwargs={'pk': 100})

2) URL template tag
- 내부적으로 reverse 함수 사용, 문자열 url
ex) {% url 'instagram:post_list'  100 %}
ex) {% url 'instagram:post_list' pk=100 %}

3) resolve_url 함수
- 내부적으로 reverse 함수 사용, 매핑 URL이 없으면 "인자 문자열"을 그대로 리턴
- 밑과 같이 적지만, 사실상 1)의 reverse 함수에 적힌대로 내부적으로 진행됨
ex) resolve_url('instagram:post_list', 100)  # 순서대로 인자 넣는 방식
ex) resolve_url('instagram:post_list', pk=100)  # 인자 지정해 넣는 방식
ex) resolve_url('/instagram/100/')

4) redirect 함수
- 내부적으로 resolve_url 함수 사용, 매핑 URL이 없으면 "인자 문자열"을 그대로 URL로 사용
ex) redirect('instagram:post_list', 100)
ex) redirect('instagram:post_list', pk=100)
ex) redirect('/instagram/100/')
```

<br>

- urls.py 내 path('instagram/', ..., )에서 instagram/를 참조하는 html이 많았다고 하자. 이 때 주소명(url)이 변경이 되면 참조되는 링크들에서 주소명을 모두 바꿔주어야 함. 웹 상에서 다 깨짐
- 개발자가 일일이 url 계산할 필요가 없음. URL reverse가 주는 혜택.
- html에서 url 템플릿 태그 등을 쓰는 방식으로 해결. 그러면 path에서 url이 바뀌어봤자 깨지는 현상이 발생하지 않음
- 그런데 일일이 또 html 가서 URL reverse 함수 적어줘야 하나? 귀찮다. 해결법은 아래

<br>

<br>

#### [URL Reverse 편하게 가자]

<br>

- 모델 객체에 대한 detail 주소 계산. how? 모델 클래스에 get_absolute_url() 구현
  - resolve_url 함수는 가장 먼저 get_absolute_url()함수의 존재 여부를 확인한 뒤, 존재하면 reverse를 수행하지 않고 리턴값을 바로 return

```python
def resolve_url(to, *args, **kwargs):
    if hasattr(to, 'get_absolute_url'):  # has attribution?
        return to.get_absolute_url()
    
    # 중략
    try:
        return reverse(to, args=args, kwargs=kwargs)
    except NoReverseMatch:
        # 나머지 코드 생략
```

<br>

- 어떻게 사용하나?

```python
[app/models.py]

from django.urls import reverse


class Post(models.Model):
    # 중략
    def get_absolute_url(self):
        return reverse('instagram:post_detail', args=[self.pk])
    
    
# 이런 식으로 model을 만들 때 제일 먼저 get_absolute_url() 함수를 적어주면 된다.


[post_list.html] # why? 내용을 누르면 detail page로 넘어가는 부분이니까

# 중략
<a href="{{ post.get_absolute_url }}"> 
	{{ post.message }}
</a>
# 중략


# {% url 'instagram:post_detail' post.pk %}와 동일
```

<br>

- 내가 메인화면에 있는 (instagram) 상태에서 메시지를 클릭하게 되면, 
  - post_list.html의 href에 따라 get_absolute_url을 사용하게 되며
  - 함수에 의해 reverse가 url을 추적하여 이동

- 효과가?
  - model 구현 시 여러 detail page를 구현하게 되는데, 이 때 매번 reverse 함수를 일일이 넣기가 귀찮으므로 html에는 get_absolute_url로 넘기도록 설정하고 아예 model.py에서 reverse 함수리턴값을 return하도록 만드는 것

<br>

<br>

<br>

### 3. Django Form

<br>

- 언제 사용?
  - HTML Form (Client); 클라이언트에서 사용자에게 입력폼 제공하고, 이를 서버로 전송할 때
    - 처음에 화면 띄우려고 서버에 요청할 때 GET 요청
  - Django Form (Server)
    - 위의 GET 요청을 받아 HTML Form 생성하는 기능 제공
    - 클라이언트에서 전송받은 입력값(POST)에 대해서는 유효성 검사 수행 및 데이터베이스에 저장하는 기능 수행

<br>

#### [HTML Form]

<br>

- <form></form>태그 이용

  - action은 어떤 URL로 보낼 것이냐
  - method는 어떤 방식으로 보낼 것이냐?
    - GET 방식
    - POST 방식; 파일 업로드 수행한다면 반드시 enctype을 multipart/form-data로 지정

  - enctype은 어떤 인코딩 방식을 사용할 것이냐?
    - GET 요청에서는 urlencoded(ex. key=value&key=value&...) 방식 강제(QueryString)
    - POST 요청에서는 multipart/form-data 등 파일 전송이 가능한 방식, QueryString도 가능하기는 함.

<br>

#### [Form]

<br>

- Form은 HTML form과의 interaction
- Model은 DB와의 interaction
  - ModelForm? Model로부터 어떤 field 정보를 가져오겠다고만 정의하면, modelform이 model로부터 field 정보를 가져와 form field를 구성 (model이 바뀌면 modelform도 알아서 바뀜)

```python
[example]

def post_new(request):
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES)  # 인자 순서 지켜야
        if form.is_valid():  # 유효성 검사 통과 시
            post = POST(**form.cleaned_data)
            post.save()
            return redirect(post)
    else:  # GET 요청이면
        form = PostForm()  # 빈 form  
            
        return render(request, 'instagram/post_form.html', {
            'form': form,
        })
```

<br>

- Form 구현

```python
[forms.py]

from django import forms
from .models import Post


class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = '__all__'
        
        
# 추후 작성자 항목 제외

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['message', 'photo', 'tag_set', 'is_public']
        
        
# 유효성 검사 추가

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['message', 'photo', 'tag_set', 'is_public']
        
    def clean_message(self):
        message = self.cleaned_data.get('message')
        if message:
            message = re.sub(r'[a-zA-Z]+', '', message)
        return message

[models.py]

from django.core.validators import MinLengthValidator


class Post(models.Model):
    # 중략
    messages = models.TextField(validators=[MinLengthValidator(10)])
    # 중략
    

< Create >

1. [instagram/urls.py]

urlpatterns = [
    path('new/', views.post_new, name='post_new'),
]


2. [instagram/views.py]

def post_new(request):
    pass


3. [instagram/templates/instagram/post_list.html]  # new/로 넘어가는 interface 만들기

<a href="{% url 'instagram:post_new' %}" 
class="btn btn-primary btn-block mt-3 mb-3">새 포스팅</a>

<table> # 중략


4. [instagram/views.py]  # /new로 넘어갈 때 error, 2번 수정

from .forms import PostForm


def post_new(request):
    form = PostForm()
    return render(request, 'instagram/post_form.html', {
        'form': form,
    })


5. [instagram/templates/instagram/post.form.html]
# 저장 키 먹히지 않음, 4번 수정


<form action="" method="POST" enctype="multipart/form-data">
	{% csrf_token %}
    <table>  # 역할?
    	{{ form }}
    </table>
    <input type="submit" value="저장" />  # 저장 키 생성
</form>


6. [instagram/views.py]
# django 통해 model 내역에 맞춰 user에게 form을 보여주고 그에 대해 저장하는 기능 구현

from django.shortcuts import redirect

def post_new(request):
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = form.save(commit=True)  # ModelForm.save(commit=True)
# Form의 cleaned_data를 Model instance 생성에 사용하고, 그 instance를 return

# model form 내부에서 model instance를 만드는데 객체 생성하고 필요한 attribute를 값에 할당한 다음, model instance.save()를 호출해야지만 실제 database에 insert query든 update query든 전달이 될텐데 그 model instance.save()를 호출할지 말지에 대한 flag가 commit. default는 commit=True.

# commit=False로 할 시 NoReverseMatch error 등장. 아직 저장되지 않은 포스팅을 가지고 redirect하려고 하니까. get_absolute_url의 args인자에 None이 들어가기에 no reverse match 발생
            return redirect(post)
    else:
        form = PostForm()
        
    return render(request, 'instagram/post_form.html', {
        'form': form,
    })        


< Edit >

1. [instagram/urls.py]

urlpatterns = [
    path('<int:pk>/edit/', views.post_edit, name='post_edit'),
]


2. [instagram/views.py]

def post_edit(request, pk):
    pass


3. [instagram/templates/instagram/post_detail.html]
# edit을 위해 detail page 넘어갔을 때 '수정' 키를 만들기 위해서
# 이 과정에서 수정 누르면 valueerror 등장, 2번에서 pass로만 구현해놔서 그럼

# 중략
목록
</a>

<a href="{% url 'instagram:post_edit' post.pk %}" class="btn btn-info">
	수정
</a>


4. [instagram/views.py]
# 수정 내용이 한 줄로 나옴. 이는 post.detail.html에서 {{ post.message|linebreaks }}로 해결한다.


@login_required
def post_new(request):
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            # request.user를 외래키로 할당하려면 이 view가 로그인 상황을 보장받아야 함 				(@login_required)
            post.save()
            return redirect(post)
    else:
        form = PostForm()
        
    return render(request, 'instagram/post_form.html', {
        'form': form,
    })

@login_required
def post_edit(request, pk):
    # pk에 해당하는 Post instance 구해오기
    post = get_object_or_404(Post, pk=pk)
    
    if post.author != request.user:  # 작성자 check tip
        messages.error(request, '작성자만 수정할 수 있습니다.')
        return redirect(post)
    
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES, instance=post)
        if form.is_valid():
            post = form.save()
            return redirect(post)
    else:
        form = PostForm(instance=post)
        
    return render(request, 'instagram/post_form.html', {
        'form': form,
    })


< Delete >

1. [instagram/views.py]

@login_required
def post_delete(request, pk):
    post = get_object_or_404(Post, pk=pk)
    if request.method == 'POST':
        post.delete()
        messages.success(request, '포스팅을 삭제했습니다.')
        return redirect('instagram:post_list')
    return render(request, 'instagram/post_confirm_delete.html', {
        'post': post,
    })
    
2. [instagram/urls.py]

urlpatterns = [
    path('<int:pk>/delete/', views.post_delete, name='post_delete'),
]


3. [instagram/templates/instagram/post_detail.html]

<a href ~~~~>
수정
</a>

<a href="{% url 'instagram:post_delete' post.pk %}" class="btn btn-danger">
	삭제
</a>


4. [instagram/templates/instagram/post_confirm_delete.html]

{% extends "instagram/layout.html" %}

{% block content %}
	<form action="" method="post">
    	{% csrf_token %}
        
        <div class="alert alert-danger">
        	정말 삭제하시겠습니까?
        </div>
        
        <a href="{{ post.get_absolute_url }}" class="btn btn-secondary">
        	내용보기
        </a>
        <input type="submit" value="삭제하겠습니다." class="btn btn-danger" />
        
    </form>
{% endblock content %}
```

