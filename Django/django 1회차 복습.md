# Django project 1

<br>

- DRF를 쓰지 않은 프로젝트의 소스 코드 분석 연습 (꾸준한 추가 요망)

<br>

## Project Folder  (ex. mycompany/)

<br>

#### [myproject/settings/common.py]

```python
import os
from os.path import abspath, dirname

# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
# __file__: 현재 파일(common.py)
# os.path.abspath(__file__): 인자로 주어지는 경로의 절대 경로
# os.path.dirname(): 인자로 주어지는 파일/폴더의 경로
# BASE_DIR: 장고 프로젝트 root directory
# 현재 파일은 mysite/myproject/settings/ 내에 있다, 고로 BASE_DIR은 root dir
BASE_DIR = dirname(dirname(dirname(abspath(__file__))))


# Quick-start development settings - unsuitable for production
# See https://docs.djangoproject.com/en/3.0/howto/deployment/checklist/

# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = '-6qeh4#l%h$(cx--j*6p(^h_no5w!xf-wf-corei_ena(y!53z'

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True

ALLOWED_HOSTS = []


# Application definition

INSTALLED_APPS = [
    # Django Apps
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.humanize',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # Third Apps
    'bootstrap4',
    'debug_toolbar',
    'django_pydenticon',
    'easy_thumbnails',
    # Local Apps
    'accounts',
    'instagram',
]

MIDDLEWARE = [
    'debug_toolbar.middleware.DebugToolbarMiddleware',
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = 'mycompany.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            os.path.join(BASE_DIR, 'mycompany', 'templates'),
        ],
        # os.path.join; 경로를 병합해서 새 경로 생성
        # http://pythonstudy.xyz/python/article/507-%ED%8C%8C%EC%9D%BC%EA%B3%BC-%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC
        # 즉, BASE_DIR(mysite)/mycompany/templates/에 template 있습니다!
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

WSGI_APPLICATION = 'mycompany.wsgi.application'


# Database
# https://docs.djangoproject.com/en/3.0/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}

# 인증 시 사용할 커스텀 유저를 여기에 설정, settings.AUTH_USER_MODEL로.
# accounts 앱에 있는 User 모델을 사용하겠다!
AUTH_USER_MODEL = "accounts.User"


# Password validation
# https://docs.djangoproject.com/en/3.0/ref/settings/#auth-password-validators

AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]


# Internationalization
# https://docs.djangoproject.com/en/3.0/topics/i18n/

LANGUAGE_CODE = 'en-us'

TIME_ZONE = 'UTC'

USE_I18N = True

USE_L10N = True

USE_TZ = True


# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/3.0/howto/static-files/

STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'mycompany', 'static'),
]  # BASE_DIR/mycompany/static/
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
# root는 BASE_DIR/static (배포 시 필요!)

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

INTERNAL_IPS = ['127.0.0.1']  # django debug toolbar
```

<br>

<br>

<br>

#### [mycompany/settings/dev.py]

```python
[개발용 모아 놓음]

from .common import *
```

<br>

<br>

<br>

#### [mycompany/settings/prod.py]

```python


from .common import *
```

<br>

<br>

<br>

#### [mycompany/templates/]

```python
_form.html
layout.html
```

<br>

<br>

<br>

#### [mycompany/asgi.py]

```python
"""
ASGI config for mycompany project.

It exposes the ASGI callable as a module-level variable named ``application``.

For more information on this file, see
https://docs.djangoproject.com/en/3.0/howto/deployment/asgi/
"""

import os

from django.core.asgi import get_asgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mycompany.settings.prod')
#

application = get_asgi_application()

```

<br>

<br>

<br>

#### [mycompany/urls.py]

```python
from django.conf import settings
from django.conf.urls.static import static
from django.contrib import admin
from django.urls import include, path
from django.views.generic import RedirectView
from django_pydenticon.views import image as pydenticon_image

urlpatterns = [
    path('admin/', admin.site.urls),  # admin site 기본 세팅
    path('accounts/', include('accounts.urls')),
    path('identicon/image/<path:data>/', pydenticon_image, name='pydenticon_image'),  #
    path('instagram/', include('instagram.urls')),
    path('', RedirectView.as_view(pattern_name='instagram:index'), name='root'),
    # localhost:8000/으로 들어오면, 바로 redirect한다. 어디로?
]

if settings.DEBUG:  # settings.py에 Debug=True이면,
    import debug_toolbar
    urlpatterns += [
        path('__debug__/', include(debug_toolbar.urls)),
    ]

    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)  #
```

<br>

<br>

<br>

#### [mycompany/wsgi.py]

```python
"""
WSGI config for mycompany project.

It exposes the WSGI callable as a module-level variable named ``application``.

For more information on this file, see
https://docs.djangoproject.com/en/3.0/howto/deployment/wsgi/
"""

import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mycompany.settings.prod')
#

application = get_wsgi_application()
```

<br>

<br>

<br>

## Requirements/

<br>

#### [requirements/common.txt]

```python
django~=3.0.0

django-bootstrap4
pillow  # ImageField
django_pydenticon
easy-thumbnails
```

<br>

<br>

<br>

#### [requirements/dev.txt]

<br>

```python
-r common.txt

django-debug-toolbar  # 개발용
```

<br>

<br>

<br>

#### [requirements/prod.txt]

<br>

```python
-r common.txt
```

<br>

<br>

<br>

## Instagram App

<br>

#### [instagram/admin.py]

<br>

```python
from django.contrib import admin
from .models import Post, Tag  
# instagram/models.py에서 Post, Tag model 가져옴
# admin website상에 보임


@admin.register(Post)  
# Post model을 admin site에 등록 (admin.site.register()와 동일한 기능)
class PostAdmin(admin.ModelAdmin):  # admin class 정의
    pass
# admin interface에서 model이 보여지는 방식을 바꾸고 싶다면, modeladmin 정의
# ModelAdmin class는 layout 묘사


@admin.register(Tag)
class TagAdmin(admin.ModelAdmin):
    pass
```

<br>

<br>

<br>

#### [instagram/apps.py]

<br>

```python
from django.apps import AppConfig
# Appconfig class: django app 및 구성을 나타내는 class


class InstagramConfig(AppConfig):
    name = 'instagram'
```

<br>

<br>

<br>

#### [instagram/forms.py]

<br>

```python
from django import forms
from .models import Post, Comment  # 우선 model 가져와서


class PostForm(forms.ModelForm):  # modelForm 1
    class Meta:
        model = Post  # model 지정
        fields = ['photo', 'caption', 'location']  # model에서 가져올 field들
        widgets = {
            "caption": forms.Textarea,  # 필드 중 caption은 입력 양식 줌
        }


class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ['message']
        widgets = {  # 커스텀 위젯
            'message': forms.Textarea(attrs={"rows": 2}),
        }
```

<br>

<br>

<br>

#### [instagram/models.py]

https://dgkim5360.tistory.com/entry/django-model-inheritance

https://programmers.co.kr/learn/courses/2/lessons/326

<br>

```python
import re  # 정규식

from django.conf import settings
from django.db import models
from django.urls import reverse


class BaseModel(models.Model):  # 공통이 되는 필드들을 가진 부모 클래스를 만들기 (추상화)
    created_at = models.DateTimeField(auto_now_add=True)  
    # 생성일자, 최초 저장 시 날짜 적용
    updated_at = models.DateTimeField(auto_now=True)  
    # 수정일자, 장고 모델이 save될 때마다 현재 날짜로 갱신

    class Meta:  # BaseModel은 실제로 존재하지 않는 가상의 class, 부모로서만 존재
        abstract = True


class Post(BaseModel):  # 가상의 class 필드, 속성, 함수 등을 모두 물려받은 모델
    author = models.ForeignKey(settings.AUTH_USER_MODEL, related_name='my_post_set',
                               on_delete=models.CASCADE)
    photo = models.ImageField(upload_to='instagram/post/%Y/%m/%d')
    caption = models.CharField(max_length=500)
    tag_set = models.ManyToManyField('Tag', blank=True)
    location = models.CharField(max_length=100)
    like_user_set = models.ManyToManyField(settings.AUTH_USER_MODEL, blank=True,
                                           related_name='like_post_set')
    # author와 like_user_set에서 reverse accessor name 충돌
    # author, like_user_set에서 default가 모두 'post_set'이므로 각각 related_name 설정
    
    # user
    #  -> Post.objects.filter(author=user)
    #  -> user.post_set.all()  # 외래키 관계에서 자동으로 post_set 이름 생성

    def __str__(self):  # 문자열화 함수, 인스턴스 자체를 출력할 때의 형식 지정
        return self.caption

    def extract_tag_list(self):  # 해시태그
        tag_name_list = re.findall(r"#[a-zA-Z\dㄱ-힣]+", self.caption)
        # re.findall(pattern, string, flags)
        # string(self.caption) 중 pattern(위 정규표현식)과 일치하는 모든 부분 찾는다.
        tag_list = []
        for tag_name in tag_name_list:
            tag, _ = Tag.objects.get_or_create(name=tag_name)
            # get_or_create(defaults=None, **kwargs)
            # 객체 존재 시 객체 get, 없으면 create / 반환은 (객체, created) tuple 형식
            # created는 boolean flag로
            # 인스턴스가 get_or_create에 의해 생성되었다면 True, 아니면 False (기존 db에 존재)
            tag_list.append(tag)
        return tag_list

    def get_absolute_url(self): # '/instagram/10/' 같이 string으로 반환
        return reverse("instagram:post_detail", args=[self.pk])

    def is_like_user(self, user):
        return self.like_user_set.filter(pk=user.pk).exists()

    class Meta:  # id 역순으로 ordering
        ordering = ['-id']


class Comment(BaseModel):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    message = models.TextField()

    class Meta:
        ordering = ['-id']


class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)

    def __str__(self):
        return self.name


# class LikeUser(models.Model):
#     post = models.ForeignKey(Post, on_delete=models.CASCADE)
#     user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
```

<br>

<br>

<br>

#### [instagram/urls.py] - name, re_path

<br>

```python
from django.urls import path, re_path
from . import views

app_name = 'instagram'  # namespace, html과 연결 (ex) instagram:~~~

urlpatterns = [
    path('', views.index, name='index'),
    # name은 url reverse를 위함, html과 연결
    path('post_new/', views.post_new, name='post_new'),
    path('post/<int:pk>/', views.post_detail, name='post_detail'),
    path('post/<int:pk>/like', views.post_like, name='post_like'),
    path('post/<int:pk>/unlike', views.post_unlike, name='post_unlike'),
    path('post/<int:post_pk>/comment/new/', views.comment_new, name='comment_new'),
    re_path(r'^(?P<username>[\w.@+-]+)/$', views.user_page, name='user_page'),
]
```

<br>

<br>

<br>

#### [instagram/views.py]

<br>

```python
from datetime import timedelta

from django.contrib import messages
from django.contrib.auth import get_user_model
from django.contrib.auth.decorators import login_required
from django.db.models import Q
from django.shortcuts import render, redirect, get_object_or_404
from django.utils import timezone

from .forms import PostForm, CommentForm
from .models import Post


@login_required  # 로그인 먼저 해라!
def index(request):
    # 위에서 localhost:8000 접속하면 root인 instagram app의 views.index 함수 호출하라고 함
    # 즉, 메인(기본) 페이지 view 함수
    timesince = timezone.now() - timedelta(days=3)
    # 최근 시간에서 3일의 시간을 뺀 시간
    post_list = Post.objects.all()\  # Post모델 객체 전부에서
        .filter(  # 필터링해라
            Q(author=request.user) |
            Q(author__in=request.user.following_set.all())
        )\  # 포스팅 작성자가 자기 자신이거나 following 목록에 있는 유저만 가져오겠다
            # 팔로잉하는 유저들의 포스팅 목록
        .filter(
            created_at__gte=timesince
        )  # 시간보다 크거나 같은 포스팅만 가져오겠다. (최근 3일 이내의 포스팅만 나오겠지)

    suggested_user_list = get_user_model().objects.all()\  
    # get_user_model()로 가져오는게 유연함
        .exclude(pk=request.user.pk)\  # 현 user pk 빼고
        .exclude(pk__in=request.user.follower_set.all())  
        # 이미 follow 한 애들 빼라, pk__in은 pk가 ~~~ 안에 포함되어 있는 경우를 말함

    comment_form = CommentForm()  # 여러 화면단에서 댓글 창이 보이게 됨 (포스팅마다)

    return render(request, "instagram/index.html", {
        "post_list": post_list,
        "suggested_user_list": suggested_user_list,
        "comment_form": comment_form,
    })
	# 요청 오면 post_list, suggested_user_list, comment_form 객체 내용 포함해서
    # instagram/index.html로 넘겨라


@login_required  # 역시 로그인 필요
def post_new(request):  # post 생성할 때 호출되는 함수
    if request.method == 'POST':  # 내용을 담은 POST 요청이라면
        form = PostForm(request.POST, request.FILES)  # 내용 넣어서 폼 인스턴스 만들고
        if form.is_valid():  # 유효성 검사 시행
            post = form.save(commit=False)  # DB에 바로 저장 안 한다, 뭐 하려고?
            post.author = request.user  # 요청해온 유저를 post.author로 지정
            post.save()  # 이제야 DB에 save
            post.tag_set.add(*post.extract_tag_list())  # tag_set에 추출한 거 추가
            messages.success(request, "포스팅을 저장했습니다.")  # message 보여주기
            return redirect(post)  # post 객체로 redirect
    else:  # 생성 페이지에 GET 요청이라면 빈 폼을 주고 (위에 forms.py)
        form = PostForm()

    return render(request, 'instagram/post_form.html', {
        'form': form,
    })
	# 유효성 검사 통과 못 했으면, 작성한 form 내용 넣어서 post_form.html로 이동 (아마 에러 표시?)

def post_detail(request, pk):  # 상세 페이지, 그래서 pk 존재
    post = get_object_or_404(Post, pk=pk)
    comment_form = CommentForm()  # 댓글 창 빈 폼
    return render(request, 'instagram/post_detail.html', {
        'post': post,
        'comment_form': comment_form,
    })
	# 요청 오면 post, comment_form 내용 담아서 post_detail.html로 이동

@login_required
def post_like(request, pk):  # 좋아요 표시, 상세 페이지에 있을테니 pk 존재
    post = get_object_or_404(Post, pk=pk)
    post.like_user_set.add(request.user) # 현 유저를 like_user_set에 add
    messages.success(request, f"포스팅#{post.pk}를 좋아합니다.")  # 메시지
    redirect_url = request.META.get("HTTP_REFERER", "root")
    return redirect(redirect_url)


@login_required
def post_unlike(request, pk):
    post = get_object_or_404(Post, pk=pk)
    post.like_user_set.remove(request.user)
    messages.success(request, f"포스팅#{post.pk}를 좋아요를 취소합니다.")
    redirect_url = request.META.get("HTTP_REFERER", "root")
    return redirect(redirect_url)


@login_required
def comment_new(request, post_pk):  # comment를 생성하는 곳이니 post의 pk가 필요
    post = get_object_or_404(Post, pk=post_pk)

    if request.method == 'POST':  # 댓글 내용 담은 요청이라면
        form = CommentForm(request.POST, request.FILES)
        if form.is_valid():
            comment = form.save(commit=False)  # user, post가 없으니까 추가해줘야
            comment.post = post  # comment 달은 post
            comment.author = request.user  # comment 작성자는 현재 로그인 유저
            comment.save()  # 그리고 나서 save
            return redirect(comment.post)  # post 모델 객체로 이동
    else:
        form = CommentForm()
    return render(request, 'instagram/comment_form.html', {
        'form': form,
    })


def user_page(request, username):
    page_user = get_object_or_404(get_user_model(), username=username, is_active=True)
    post_list = Post.objects.filter(author=page_user)
    # 작성자가 이 페이지 유저여야
    post_list_count = post_list.count()  
    # 실제 데이터베이스에 count 쿼리를 던지게 된다.

    if request.user.is_authenticated:  
        # 현 유저가 인증이 되어있을 때에만
        # request.user는 인증 시 User, 인증 x시 AnonymousUser
        is_follow = request.user.following_set.filter(pk=page_user.pk).exists()
        # following set에 page_user의 pk가 포함되어 있냐? 그러면 is_follow
    else:
        is_follow = False

    return render(request, "instagram/user_page.html", {
        "page_user": page_user,
        "post_list": post_list,
        "post_list_count": post_list_count,
        "is_follow": is_follow,
    })
```

<br>

<br>

<br>

#### [instagram/templates/instagram/_post_card.html]

<br>

```python
{% load bootstrap4 humanize instagram_tags %}

<div class="card">
    <div class="card-header">
        <img src="{{ post.author.avatar_url }}" style="width: 32px; height: 32px;" />
        <a href="{% url 'instagram:user_page' post.author.username %}">
            {{ post.author.name }}
        </a>
    </div>
    <div class="card-body">
        <img src="{{ post.photo.url }}" />

        <div>
            {% if post|is_like_user:user %}
                <a href="{% url 'instagram:post_unlike' post.pk %}" style="color: inherit;">
                    <i class="fa fa-heart"></i>
                </a>
            {% else %}
                <a href="{% url 'instagram:post_like' post.pk %}" style="color: inherit;">
                    <i class="fa fa-heart-o"></i>
                </a>
            {% endif %}
        </div>

        <div class="comment-list mt-3 mb-3">
            {% for comment in post.comment_set.all %}
            <div class="comment">
                <strong>{{ comment.author }}</strong>
                {{ comment.message }}
                <small class="text-muted">{{ comment.created_at|naturaltime }}</small>
            </div>
            {% endfor %}
        </div>

        <div>
            {% for tag in post.tag_set.all %}
                <span class="badge bg-primary">
                    {{ tag.name }}
                </span>
            {% endfor %}
        </div>

        <div>
            <a href="{{ post.get_absolute_url }}" style="" class="text-muted">
                <small>
                    {{ post.created_at|naturaltime }}
                </small>
            </a>
        </div>

    </div>
    <div class="card-footer">
        <form action="{% url 'instagram:comment_new' post.pk %}" method="POST">
            {% csrf_token %}
            {% bootstrap_form comment_form %}
            <br/>
            <input type="submit" value="댓글 쓰기" class="btn btn-primary btn-block" />
        </form>
    </div>
</div>
```

<br>

<br>

<br>

#### [instagram/templates/instagram/comment_form.html]

<br>

```python
{% extends "instagram/layout.html" %}

{% block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-6 offset-sm-3">
                {% include "_form.html" with form_title="댓글 쓰기" submit_label="댓글 쓰기" %}
            </div>
        </div>
    </div>
{% endblock %}
```

<br>

<br>

<br>

#### [instagram/templates/instagram/index.html]

<br>

```python
{% extends "instagram/layout.html" %}

{% block content %}
    <div class="container">
        <div class="row mb-3">
            <div class="col-sm-12">
                <a href="{% url 'instagram:post_new' %}" class="btn btn-primary">새 포스팅 쓰기</a>
            </div>
        </div>
        <div class="row">
            <div class="col-sm-8">
                {% for post in post_list %}
                    <div class="mb-3">
                        {% include "instagram/_post_card.html" %}
                    </div>
                {% empty %}
                    포스팅이 없습니다.
                {% endfor %}
            </div>
            <div class="col-sm-4">
                {% include "instagram/timeline_sidebar.html" %}
            </div>
        </div>
    </div>
{% endblock %}
```

<br>

<br>

<br>

#### [instagram/templates/instagram/layout.html]

<br>

```python
{% extends "layout.html" %}
```

<br>

<br>

<br>

#### [instagram/templates/instagram/post_detail.html]

<br>

```python
{% extends "instagram/layout.html" %}

{% block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-12">
                {% include "instagram/_post_card.html" %}
            </div>
        </div>
    </div>
{% endblock %}
```

<br>

<br>

<br>

#### [instagram/templates/instagram/post_form.html]

<br>

```python
{% extends "instagram/layout.html" %}

{% block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-6 offset-sm-3">
                {% include "_form.html" with form_title="새 포스팅 쓰기" submit_label="새 포스팅 쓰기" %}
            </div>
        </div>
    </div>
{% endblock %}
```

<br>

<br>

<br>

#### [instagram/templates/instagram/timeline_sidebar.html]

<br>

```python

<div class="d-flex p-2 mb-3">
    <div class="mr-2">
        <img src="{{ user.avatar_url }}" style="width: 48px; height: 48px;" class="rounded-circle" />
    </div>
    <div>
        {{ user.username }}<br/>
        <small class="text-muted">{{ user.name }}</small>
    </div>
</div>

<div class="card mb-3">
    <div class="card-header text-muted">
        Stories
    </div>
    <div class="card-body text-muted">
        Stories from people you follow will show up here.
    </div>
</div>

<div class="card mb-3">
    <div class="card-header text-muted">
        Suggestions for you
    </div>
    <div class="card-body text-muted">
        {% for suggested_user in suggested_user_list %}
            {% include "instagram/timeline_sidebar_user_follow.html" %}
        {% empty %}
            추천 친구가 없습니다.
        {% endfor %}
    </div>
</div>
```

<br>

<br>

<br>

#### [instagram/templates/instagram/timeline_sidebar_user_follow.html]

<br>

```python
<div class="d-flex mb-1">
    <div class="mr-3">
        <img src="{{ suggested_user.avatar_url }}" style="width: 32px; height: 32px;" class="rounded-circle" />
    </div>
    <div class="">
        <a href="{% url 'instagram:user_page' suggested_user.username %}">
            {{ suggested_user.username }}
        </a>
    </div>
    <div class="ms-auto">
        <a href="{% url 'user_follow' suggested_user.username %}">Follow</a>
<!--        <a href="{% url 'user_unfollow' suggested_user.username %}">Unfollow</a>-->
    </div>
</div>
```

<br>

<br>

<br>

#### [instagram/templates/instagram/user_page.html]

<br>

```python
{% extends "instagram/layout.html" %}
{% load thumbnail %}

{% block content %}
    <div class="container">
        <div class="row pt-5 pb-5">
            <div class="col-sm-3" style="text-align: center;">
                <img src="{{ page_user.avatar_url }}" class="rounded-circle" style="width: 160px;" />
            </div>
            <div class="col-sm-9">
                {{ page_user.username }}
                <a href="{% url 'profile_edit' %}" class="btn btn-secondary btn-sm">
                    Edit Profile
                </a>

                {% if is_follow %}
                    <a href="{% url 'user_unfollow' page_user.username %}">Unfollow</a>
                {% else %}
                    <a href="{% url 'user_follow' page_user.username %}">Follow</a>
                {% endif %}

                <hr/>
                {{ post_list_count }} posts, 0 followers, 0 following
                <hr/>
                {{ page_user.name }}
            </div>
        </div>
        <div class="row mt-3">
            {% for post in post_list %}
                <div class="col-sm-4 mb-3">
                    <img src="{% thumbnail post.photo 512x512 crop %}" alt="{{ post.caption }}" style="width: 100%;" />
                </div>
            {% endfor %}
        </div>
    </div>
{% endblock %}
```

<br>

<br>

<br>

#### [instagram/templatetags/instagram_tags.py]

<br>

```python
from django import template

register = template.Library()


@register.filter
def is_like_user(post, user):
    return post.is_like_user(user)
```

<br>

<br>

<br>

## Accounts App

<br>

#### [accounts/admin.py]

<br>

```python
from django.contrib import admin
from .models import User


@admin.register(User)
class UserAdmin(admin.ModelAdmin):
    list_display = ['username', 'email', 'website_url', 'is_active', 'is_staff', 'is_superuser']
```

<br>

<br>

<br>

#### [accounts/apps.py]

<br>

```python
from django.apps import AppConfig


class AccountsConfig(AppConfig):
    name = 'accounts'

```

<br>

<br>

<br>

#### [accounts/forms.py]

<br>

```python
from django import forms
from django.contrib.auth.forms import (
    UserCreationForm, PasswordChangeForm as AuthPasswordChangeForm
)
from .models import User


class SignupForm(UserCreationForm):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.fields['email'].required = True  # 필수 필드이다
        self.fields['first_name'].required = True
        self.fields['last_name'].required = True

    class Meta(UserCreationForm.Meta):  # 상속
        model = User
        fields = ['username', 'email', 'first_name', 'last_name']

    def clean_email(self):  # 이메일 유효성 검사
        email = self.cleaned_data.get('email')
        if email:
            qs = User.objects.filter(email=email)
            if qs.exists():
                raise forms.ValidationError("이미 등록된 이메일 주소입니다.")
        return email


class ProfileForm(forms.ModelForm):
    class Meta:
        model = User
        fields = ['avatar', 'first_name', 'last_name', 'website_url', 'bio', 'phone_number', 'gender']


class PasswordChangeForm(AuthPasswordChangeForm):
    def clean_new_password1(self):
        old_password = self.cleaned_data.get('old_password')
        new_password1 = self.cleaned_data.get('new_password1')
        if old_password and new_password1:
            if old_password == new_password1:
                raise forms.ValidationError("새로운 암호는 기존 암호와 다르게 입력해주세요.")
        return new_password1
```

<br>

<br>

<br>

#### [accounts/models.py]

<br>

```python
from django.contrib.auth.models import AbstractUser  
# user model이 가져야 할 특성 지원
# user model을 바꾸려면 settings 내 AUTH_USER_MODEL 설정해야. 뭐로? "accounts.User" 
# (그래서 db.sqlite3 날리고 위 설정해준 뒤 makemigrations 해줌)

# get_user_model vs settings.AUTH_USER_MODEL?
# get_user_model: 실제 model class return / settings.AUTH_USER_MODEL은 string
from django.core.validators import RegexValidator
from django.db import models
from django.shortcuts import resolve_url


class User(AbstractUser):
    class GenderChoices(models.TextChoices):  # django ver 3부터
        MALE = "M", "남성"  # django에 저장되는 값, 실제 보여지는 값 튜플 형태
        FEMALE = "F", "여성"

    follower_set = models.ManyToManyField("self", blank=True)
    following_set = models.ManyToManyField("self", blank=True)

    website_url = models.URLField(blank=True)
    bio = models.TextField(blank=True)
    phone_number = models.CharField(max_length=13, blank=True,
                                    validators=[RegexValidator(r"^010-?[1-9]\d{3}-?\d{4}$")])
    								# Regexvalidator는 정규표현식 validator임
    gender = models.CharField(max_length=1, blank=True, choices=GenderChoices.choices)
    avatar = models.ImageField(blank=True, upload_to="accounts/avatar/%Y/%m/%d",
                               help_text="48px * 48px 크기의 png/jpg 파일을 업로드해주세요.")

    @property # 메서드를 속성처럼 사용 가능
    def name(self):
        return f"{self.first_name} {self.last_name}"

    @property
    def avatar_url(self):
        if self.avatar:
            return self.avatar_url
        else:
            return resolve_url("pydenticon_image", self.username)
```

<br>

<br>

<br>

#### [accounts/urls.py]

<br>

```python
from django.urls import path, re_path
from . import views

urlpatterns = [
    path('login/', views.login, name='login'),  
    # /accounts/login/ (웹브라우저 상 실제 주소) => settings.LOGIN_URL
    # login required 장식자 적용 시 /accounts/login/으로 이동하는 것은 settings.LOGIN_URL 때문
    path('logout/', views.logout, name='logout'),
    path('password_change/', views.password_change, name='password_change'),
    path('signup/', views.signup, name='signup'),
    path('edit/', views.profile_edit, name='profile_edit'),

    re_path(r'^(?P<username>[\w.@+-]+)/follow/$', views.user_follow, name='user_follow'),
    re_path(r'^(?P<username>[\w.@+-]+)/unfollow/$', views.user_unfollow, name='user_unfollow'),
]

# django.conf 내 global_settings.py에 보면 LOGIN_URL = '/accounts/login'이 setting되어 있음. 따라서 accounts app을 만든 것
# django.contrib.auth에 view함수에 CBV로서 로그인 등이 처리되고 있음
```

<br>

<br>

<br>

#### [accounts/views.py]

<br>

```python
from django.contrib import messages
from django.contrib.auth import login as auth_login  
# import 모듈이름 as 변경이름 (login 모듈을 auth_login으로 바꿔 부르겠다)
# django.contrib.auth.login(request, user, backend=None)
# login()은 세션 내에 유저의 ID를 저장함. (django의 session framework 이용해서)
from django.contrib.auth.decorators import login_required
# login_required(redirect_field_name='next', login_url=None)
# user가 login되어 있지 않다면, settings.LOGIN_URL로 redirect
# login되어 있다면, view를 실행
from django.contrib.auth.mixins import LoginRequiredMixin
# 로그인 안 한 유저들이 어떤 요청을 하든 로그인 페이지로 redirect되거나 403 forbidden error를 보게 될 것이다. (depending on the raise_exception parameter)
from django.contrib.auth.views import (
    LoginView, logout_then_login,
    PasswordChangeView as AuthPasswordChangeView,
)
from django.shortcuts import redirect, render, get_object_or_404
from django.urls import reverse_lazy
# reverse_lazy(viewname, urlconf=None, args=None, kwargs=None, current_app=None)
# reverse_lazy는 함수 호출시 곧바로 처리하지 않고, 나중에 해당 변수가 직접 접근되거나 메서드가 호출되었을 때 evaluate한다. 이 때 비로소 reverse 함수가 실행되어 본래 값 반환
# https://nearkim.coffee/posts/django-lazy-function
# evaluate? 쿼리셋을 순회하는 시점에 쿼리셋에 해당하는 DB 레코드들을 실제로 가져오고(fetch), 이것들은 모두 django 모델로 변환
# http://raccoonyy.github.io/using-django-querysets-effectively-translate/


from .forms import SignupForm, ProfileForm, PasswordChangeForm
from .models import User

login = LoginView.as_view(template_name="accounts/login_form.html")


def logout(request):
    messages.success(request, '로그아웃되었습니다.')
    return logout_then_login(request)
    # 유저가 로그아웃하면, 로그인 페이지로 redirect
    # 소스 코드가 다음과 같이 되어있음
    # def logout_then_login(request, login_url=None):
    # 	login_url = resolve_url(login_url or settings.LOGIN_URL)
    #   return LogoutView.as_view(next_page=login_url)(request)


def signup(request):
    if request.method == 'POST':  # signup form을 채워서 오는 요청이라면
        form = SignupForm(request.POST)  # 내용을 채워서 form 객체 생성
        if form.is_valid():  # 유효성 검사를 통과했다면
            signed_user = form.save()  # db에 저장하고 signed_user 변수에 저장
            auth_login(request, signed_user)  # ID 저장
            messages.success(request, "회원가입 환영합니다.")
            next_url = request.GET.get('next', '/')
            # request.GET은 전달받은 것들을 dict 형태로 가져옴
            # request.GET.get은 안의 key에 해당하는 value를 가져옴
            # next 인자를 가져와보고 없으면 /주소로 이동
            # signup 다 넣고 submit 누르면 accounts/login/?next=/으로 감 (로그아웃 상황이므로)
            # 회원가입만 되고 로그인은 안 되어 있는 상황이니
            return redirect(next_url)
        	# pattern name -> url name순
    else:
        form = SignupForm()
    return render(request, 'accounts/signup_form.html', {
        'form': form,
    })
	# 유효성 검사 통과 못 했을 시 form 담아서 signup_form.html로 이동

@login_required
def profile_edit(request):
    if request.method == 'POST':
        form = ProfileForm(request.POST, request.FILES, instance=request.user)
        # instance를 통해 기존의 내용 가져오기
        if form.is_valid():
            form.save()
            messages.success(request, "프로필을 수정/저장했습니다.")
            return redirect("profile_edit")
    else:
        form = ProfileForm(instance=request.user)
    return render(request, "accounts/profile_edit_form.html", {
        "form": form,
    })


class PasswordChangeView(LoginRequiredMixin, AuthPasswordChangeView):
    # PasswordChangeView 속성들
    # 1. template_name: password change form 보여줄 템플릿의 이름
    # 2. success_url: 변경 성공하면 redirect할 url
    # 3. form class: 암호 변경 폼(커스텀)으로 user kwarg 포함된 것 / default가 PasswordChangeForm
    success_url = reverse_lazy("password_change")
    template_name = 'accounts/password_change_form.html'
    form_class = PasswordChangeForm

    def form_valid(self, form):  # 유효성 검사
        messages.success(self.request, "암호를 변경했습니다.")
        return super().form_valid(form)  # ? / view의 response


password_change = PasswordChangeView.as_view()


@login_required
def user_follow(request, username):
    follow_user = get_object_or_404(User, username=username, is_active=True)
    # get_user_model() 안 쓴 이유는 어차피 accounts 앱이라 User 모델 있음, 그대로 참조
    request.user.following_set.add(follow_user)
    # request user가 follow_user를 follow하려는 것
    follow_user.follower_set.add(request.user)
    # 반대로 foller_user를 following 당하는 set에 request.user 추가
    messages.success(request, f'{follow_user}님을 팔로우했습니다.')
    redirect_url = request.META.get("HTTP_REFERER", "root")
    # django HttpRequest.META 속성 중 HTTP_REFERER 있음
    # HTTP_REFERER가 있으면 가져오고 없으면 "root" 가져오기
    return redirect(redirect_url)
    # redirect 자체가 pattern name으로 먼저 시도해보고 없으면 url로 처리하는 기능 제공


@login_required
def user_unfollow(request, username):
    unfollow_user = get_object_or_404(User, username=username, is_active=True)
    request.user.following_set.remove(unfollow_user)
    unfollow_user.follower_set.remove(request.user)
    messages.success(request, f'{unfollow_user}님을 언팔했습니다.')
    redirect_url = request.META.get("HTTP_REFERER", "root")
    return redirect(redirect_url)
```

<br>

<br>

<br>

#### [accounts/templates/accounts/layout.html]

<br>

```python
{% extends "layout.html" %}
```

<br>

<br>

<br>

#### [accounts/templates/accounts/login_form.html]

<br>

```python
{% extends "accounts/layout.html" %}

{% block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-6 offset-sm-3">
                {% include "_form.html" with form_title="로그인" submit_label="로그인" %}
            </div>
        </div>
    </div>
{% endblock %}
```

<br>

<br>

<br>

#### [accounts/templates/accounts/password_change_form.html]

<br>

```python
{% extends "accounts/layout.html" %}

{% block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-6 offset-sm-3">
                {% include "_form.html" with form_title="암호 변경" submit_label="암호 변경" %}
            </div>
        </div>
    </div>
{% endblock %}
```

<br>

<br>

<br>

#### [accounts/templates/accounts/profile_edit_form.html]

<br>

```python
{% extends "accounts/layout.html" %}

{% block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-6 offset-sm-3">
                {% include "_form.html" with form_title="프로필 수정" submit_label="프로필 수정" %}
            </div>
        </div>
    </div>
{% endblock %}
```

<br>

<br>

<br>

#### [accounts/templates/accounts/signup_form.html]

<br>

```python
{% extends "accounts/layout.html" %}

{% block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-6 offset-sm-3">
                {% include "_form.html" with form_title="회원가입" submit_label="회원가입" %}
            </div>
        </div>
    </div>
{% endblock %}
```

<br>

<br>

<br>

## .gitignore

<br>

```python
__pycache__
db.sqlite3
/static
/media
.idea
```

