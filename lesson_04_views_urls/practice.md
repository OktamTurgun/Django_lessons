# 🛠️ 4-dars: Django Views va URL Patterns - Amaliyot

---

## 📋 Mashg'ulot 1: Function-based Views yaratish

### Vazifa
Blog app uchun oddiy function-based views yarating.

### Qadamlar

**1. Views.py faylini yarating (`blog/views.py`):**
```python
from django.shortcuts import render, get_object_or_404
from django.http import HttpResponse
from .models import Post, Category

def home(request):
    """Bosh sahifa"""
    return HttpResponse("<h1>Xush kelibsiz!</h1>")

def post_list(request):
    """Barcha postlarni ko'rsatish"""
    posts = Post.objects.filter(is_published=True)
    return render(request, 'blog/post_list.html', {'posts': posts})

def post_detail(request, post_id):
    """Bitta postni ko'rsatish"""
    post = get_object_or_404(Post, id=post_id)
    return render(request, 'blog/post_detail.html', {'post': post})

def category_posts(request, category_name):
    """Kategoriya bo'yicha postlar"""
    category = get_object_or_404(Category, name=category_name)
    posts = Post.objects.filter(category=category, is_published=True)
    return render(request, 'blog/category_posts.html', {
        'category': category,
        'posts': posts
    })

def about(request):
    """Haqida sahifasi"""
    return render(request, 'blog/about.html')

def contact(request):
    """Aloqa sahifasi"""
    if request.method == 'POST':
        name = request.POST.get('name')
        email = request.POST.get('email')
        message = request.POST.get('message')
        # Bu yerda email yuborish yoki ma'lumotlarni saqlash logikasi
        return HttpResponse("Xabar yuborildi! Rahmat!")
    
    return render(request, 'blog/contact.html')
```

**2. URL patterns yarating (`blog/urls.py`):**
```python
from django.urls import path
from . import views

app_name = 'blog'

urlpatterns = [
    path('', views.post_list, name='post_list'),
    path('home/', views.home, name='home'),
    path('post/<int:post_id>/', views.post_detail, name='post_detail'),
    path('category/<str:category_name>/', views.category_posts, name='category_posts'),
    path('about/', views.about, name='about'),
    path('contact/', views.contact, name='contact'),
]
```

**3. Asosiy URL ga qo'shing (`mysite/urls.py`):**
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),
]
```

### Natija
Function-based views yaratildi va URL patterns sozlandi.

---

## 📋 Mashg'ulot 2: Template'lar yaratish

### Vazifa
Views uchun template'lar yarating.

### Qadamlar

**1. Template papkalarini yarating:**
```bash
mkdir blog/templates
mkdir blog/templates/blog
```

**2. Base template yarating (`blog/templates/blog/base.html`):**
```html
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Blog{% endblock %}</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 0; padding: 20px; }
        nav { background: #333; padding: 10px; margin-bottom: 20px; }
        nav a { color: white; text-decoration: none; margin-right: 15px; }
        .post { border: 1px solid #ddd; padding: 15px; margin: 10px 0; }
        .error { color: red; }
        .success { color: green; }
    </style>
</head>
<body>
    <nav>
        <a href="{% url 'blog:home' %}">Bosh sahifa</a>
        <a href="{% url 'blog:post_list' %}">Postlar</a>
        <a href="{% url 'blog:about' %}">Haqida</a>
        <a href="{% url 'blog:contact' %}">Aloqa</a>
    </nav>
    
    <main>
        {% block content %}
        {% endblock %}
    </main>
    
    <footer>
        <p>&copy; 2024 Blog. Barcha huquqlar himoyalangan.</p>
    </footer>
</body>
</html>
```

**3. Post list template (`blog/templates/blog/post_list.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}Barcha postlar{% endblock %}

{% block content %}
<h1>Blog postlar ({{ posts.count }} ta)</h1>

{% for post in posts %}
    <article class="post">
        <h2><a href="{% url 'blog:post_detail' post.id %}">{{ post.title }}</a></h2>
        <p>{{ post.content|truncatewords:30 }}</p>
        <small>
            Kategoriya: {{ post.category.name }} | 
            Sana: {{ post.created_at|date:"d.m.Y H:i" }}
        </small>
    </article>
{% empty %}
    <p>Hozircha postlar yo'q.</p>
{% endfor %}
{% endblock %}
```

**4. Post detail template (`blog/templates/blog/post_detail.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}{{ post.title }}{% endblock %}

{% block content %}
<article class="post">
    <h1>{{ post.title }}</h1>
    <p><strong>Kategoriya:</strong> {{ post.category.name }}</p>
    <p><strong>Sana:</strong> {{ post.created_at|date:"d.m.Y H:i" }}</p>
    <hr>
    <div>{{ post.content|linebreaks }}</div>
</article>

<a href="{% url 'blog:post_list' %}">← Orqaga</a>
{% endblock %}
```

**5. Category posts template (`blog/templates/blog/category_posts.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}{{ category.name }} - Postlar{% endblock %}

{% block content %}
<h1>{{ category.name }} kategoriyasi ({{ posts.count }} ta post)</h1>
<p>{{ category.description }}</p>

{% for post in posts %}
    <article class="post">
        <h2><a href="{% url 'blog:post_detail' post.id %}">{{ post.title }}</a></h2>
        <p>{{ post.content|truncatewords:30 }}</p>
        <small>{{ post.created_at|date:"d.m.Y H:i" }}</small>
    </article>
{% empty %}
    <p>Bu kategoriyada postlar yo'q.</p>
{% endfor %}

<a href="{% url 'blog:post_list' %}">← Barcha postlar</a>
{% endblock %}
```

**6. About template (`blog/templates/blog/about.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}Haqida{% endblock %}

{% block content %}
<h1>Biz haqida</h1>
<p>Bu blog Django framework yordamida yaratilgan.</p>
<p>Bu yerda dasturlash, texnologiya va boshqa mavzularda maqolalar chop etiladi.</p>
{% endblock %}
```

**7. Contact template (`blog/templates/blog/contact.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}Aloqa{% endblock %}

{% block content %}
<h1>Biz bilan bog'laning</h1>

<form method="POST">
    {% csrf_token %}
    <div>
        <label for="name">Ismingiz:</label><br>
        <input type="text" id="name" name="name" required>
    </div>
    <br>
    <div>
        <label for="email">Email:</label><br>
        <input type="email" id="email" name="email" required>
    </div>
    <br>
    <div>
        <label for="message">Xabar:</label><br>
        <textarea id="message" name="message" rows="5" required></textarea>
    </div>
    <br>
    <button type="submit">Yuborish</button>
</form>
{% endblock %}
```

### Natija
Barcha template'lar yaratildi va views bilan bog'landi.

---

## 📋 Mashg'ulot 3: Class-based Views

### Vazifa
Class-based views yarating va ularni URL patterns ga qo'shing.

### Qadamlar

**1. Class-based views qo'shing (`blog/views.py`):**
```python
from django.views.generic import ListView, DetailView, CreateView, UpdateView, DeleteView
from django.contrib.auth.mixins import LoginRequiredMixin
from django.urls import reverse_lazy

# Mavjud function-based views...

class PostListView(ListView):
    model = Post
    template_name = 'blog/post_list_cbv.html'
    context_object_name = 'posts'
    ordering = ['-created_at']
    paginate_by = 5

class PostDetailView(DetailView):
    model = Post
    template_name = 'blog/post_detail_cbv.html'
    context_object_name = 'post'

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    template_name = 'blog/post_form.html'
    fields = ['title', 'content', 'category', 'is_published']
    success_url = reverse_lazy('blog:post_list_cbv')

class PostUpdateView(LoginRequiredMixin, UpdateView):
    model = Post
    template_name = 'blog/post_form.html'
    fields = ['title', 'content', 'category', 'is_published']
    success_url = reverse_lazy('blog:post_list_cbv')

class PostDeleteView(LoginRequiredMixin, DeleteView):
    model = Post
    template_name = 'blog/post_confirm_delete.html'
    success_url = reverse_lazy('blog:post_list_cbv')
```

**2. URL patterns ga qo'shing (`blog/urls.py`):**
```python
from django.urls import path
from . import views

app_name = 'blog'

urlpatterns = [
    # Function-based views
    path('', views.post_list, name='post_list'),
    path('home/', views.home, name='home'),
    path('post/<int:post_id>/', views.post_detail, name='post_detail'),
    path('category/<str:category_name>/', views.category_posts, name='category_posts'),
    path('about/', views.about, name='about'),
    path('contact/', views.contact, name='contact'),
    
    # Class-based views
    path('cbv/', views.PostListView.as_view(), name='post_list_cbv'),
    path('cbv/post/<int:pk>/', views.PostDetailView.as_view(), name='post_detail_cbv'),
    path('cbv/post/new/', views.PostCreateView.as_view(), name='post_create'),
    path('cbv/post/<int:pk>/edit/', views.PostUpdateView.as_view(), name='post_update'),
    path('cbv/post/<int:pk>/delete/', views.PostDeleteView.as_view(), name='post_delete'),
]
```

**3. Class-based views uchun template'lar yarating:**

**Post list CBV template (`blog/templates/blog/post_list_cbv.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}Postlar (CBV){% endblock %}

{% block content %}
<h1>Blog postlar (Class-based View)</h1>

{% for post in posts %}
    <article class="post">
        <h2><a href="{% url 'blog:post_detail_cbv' post.pk %}">{{ post.title }}</a></h2>
        <p>{{ post.content|truncatewords:30 }}</p>
        <small>
            Kategoriya: {{ post.category.name }} | 
            Sana: {{ post.created_at|date:"d.m.Y H:i" }}
        </small>
    </article>
{% empty %}
    <p>Hozircha postlar yo'q.</p>
{% endfor %}

{% if is_paginated %}
    <div>
        {% if page_obj.has_previous %}
            <a href="?page={{ page_obj.previous_page_number }}">← Oldingi</a>
        {% endif %}
        
        <span>{{ page_obj.number }} / {{ page_obj.paginator.num_pages }}</span>
        
        {% if page_obj.has_next %}
            <a href="?page={{ page_obj.next_page_number }}">Keyingi →</a>
        {% endif %}
    </div>
{% endif %}

<a href="{% url 'blog:post_create' %}">+ Yangi post</a>
{% endblock %}
```

**Post form template (`blog/templates/blog/post_form.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}
    {% if form.instance.pk %}Postni tahrirlash{% else %}Yangi post{% endif %}
{% endblock %}

{% block content %}
<h1>
    {% if form.instance.pk %}
        "{{ form.instance.title }}" ni tahrirlash
    {% else %}
        Yangi post yaratish
    {% endif %}
</h1>

<form method="POST">
    {% csrf_token %}
    <div>
        <label for="{{ form.title.id_for_label }}">Sarlavha:</label><br>
        {{ form.title }}
        {% if form.title.errors %}
            <div class="error">{{ form.title.errors }}</div>
        {% endif %}
    </div>
    <br>
    <div>
        <label for="{{ form.content.id_for_label }}">Matn:</label><br>
        {{ form.content }}
        {% if form.content.errors %}
            <div class="error">{{ form.content.errors }}</div>
        {% endif %}
    </div>
    <br>
    <div>
        <label for="{{ form.category.id_for_label }}">Kategoriya:</label><br>
        {{ form.category }}
        {% if form.category.errors %}
            <div class="error">{{ form.category.errors }}</div>
        {% endif %}
    </div>
    <br>
    <div>
        <label>
            {{ form.is_published }} Nashr etilgan
        </label>
    </div>
    <br>
    <button type="submit">Saqlash</button>
    <a href="{% url 'blog:post_list_cbv' %}">Bekor qilish</a>
</form>
{% endblock %}
```

**Post delete template (`blog/templates/blog/post_confirm_delete.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}"{{ object.title }}" ni o'chirish{% endblock %}

{% block content %}
<h1>Postni o'chirish</h1>
<p>"{{ object.title }}" postini o'chirishni xohlaysizmi?</p>

<form method="POST">
    {% csrf_token %}
    <button type="submit" style="background: red; color: white;">O'chirish</button>
    <a href="{% url 'blog:post_list_cbv' %}">Bekor qilish</a>
</form>
{% endblock %}
```

### Natija
Class-based views yaratildi va ular uchun template'lar sozlandi.

---

## 📋 Mashg'ulot 4: HTTP metodlari bilan ishlash

### Vazifa
GET va POST so'rovlari bilan ishlashni o'rganing.

### Qadamlar

**1. Search view yarating (`blog/views.py`):**
```python
def search_posts(request):
    """Postlarni qidirish"""
    query = request.GET.get('q', '')
    if query:
        posts = Post.objects.filter(
            title__icontains=query
        ) | Post.objects.filter(
            content__icontains=query
        )
    else:
        posts = Post.objects.none()
    
    return render(request, 'blog/search_results.html', {
        'posts': posts,
        'query': query
    })

def create_post_simple(request):
    """Oddiy post yaratish"""
    if request.method == 'POST':
        title = request.POST.get('title')
        content = request.POST.get('content')
        category_id = request.POST.get('category')
        
        if title and content and category_id:
            category = Category.objects.get(id=category_id)
            post = Post.objects.create(
                title=title,
                content=content,
                category=category
            )
            return redirect('blog:post_detail', post_id=post.id)
    
    categories = Category.objects.all()
    return render(request, 'blog/create_post_simple.html', {
        'categories': categories
    })
```

**2. URL patterns ga qo'shing (`blog/urls.py`):**
```python
urlpatterns = [
    # ... mavjud URL patterns ...
    path('search/', views.search_posts, name='search_posts'),
    path('create-simple/', views.create_post_simple, name='create_post_simple'),
]
```

**3. Search template yarating (`blog/templates/blog/search_results.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}Qidiruv natijalari{% endblock %}

{% block content %}
<h1>Qidiruv natijalari</h1>

<form method="GET" action="{% url 'blog:search_posts' %}">
    <input type="text" name="q" value="{{ query }}" placeholder="Qidirish...">
    <button type="submit">Qidirish</button>
</form>

{% if query %}
    <h2>"{{ query }}" uchun natijalar ({{ posts.count }} ta)</h2>
    
    {% for post in posts %}
        <article class="post">
            <h2><a href="{% url 'blog:post_detail' post.id %}">{{ post.title }}</a></h2>
            <p>{{ post.content|truncatewords:30 }}</p>
            <small>{{ post.created_at|date:"d.m.Y" }}</small>
        </article>
    {% empty %}
        <p>Hech qanday natija topilmadi.</p>
    {% endfor %}
{% endif %}

<a href="{% url 'blog:post_list' %}">← Orqaga</a>
{% endblock %}
```

**4. Simple create template (`blog/templates/blog/create_post_simple.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}Yangi post{% endblock %}

{% block content %}
<h1>Yangi post yaratish</h1>

<form method="POST">
    {% csrf_token %}
    <div>
        <label for="title">Sarlavha:</label><br>
        <input type="text" id="title" name="title" required>
    </div>
    <br>
    <div>
        <label for="content">Matn:</label><br>
        <textarea id="content" name="content" rows="10" required></textarea>
    </div>
    <br>
    <div>
        <label for="category">Kategoriya:</label><br>
        <select id="category" name="category" required>
            <option value="">Kategoriya tanlang</option>
            {% for category in categories %}
                <option value="{{ category.id }}">{{ category.name }}</option>
            {% endfor %}
        </select>
    </div>
    <br>
    <button type="submit">Yaratish</button>
    <a href="{% url 'blog:post_list' %}">Bekor qilish</a>
</form>
{% endblock %}
```

### Natija
HTTP metodlari bilan ishlash o'rgatildi.

---

## 📋 Mashg'ulot 5: Error handling

### Vazifa
Xatolarni to'g'ri boshqarishni o'rganing.

### Qadamlar

**1. Error handling view'lar yarating (`blog/views.py`):**
```python
from django.http import Http404
from django.shortcuts import render

def custom_404(request, exception):
    """404 xatosi uchun maxsus sahifa"""
    return render(request, 'blog/404.html', status=404)

def custom_500(request):
    """500 xatosi uchun maxsus sahifa"""
    return render(request, 'blog/500.html', status=500)

def test_error(request):
    """Xatoni sinab ko'rish uchun"""
    raise Http404("Bu sahifa mavjud emas")
```

**2. Error template'lar yarating:**

**404 template (`blog/templates/blog/404.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}Sahifa topilmadi{% endblock %}

{% block content %}
<div style="text-align: center; padding: 50px;">
    <h1>404 - Sahifa topilmadi</h1>
    <p>Axtirgan sahifa mavjud emas yoki o'chirilgan.</p>
    <a href="{% url 'blog:post_list' %}">Bosh sahifaga qaytish</a>
</div>
{% endblock %}
```

**500 template (`blog/templates/blog/500.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}Server xatosi{% endblock %}

{% block content %}
<div style="text-align: center; padding: 50px;">
    <h1>500 - Server xatosi</h1>
    <p>Kechirasiz, serverda muammo bor. Iltimos, keyinroq urinib ko'ring.</p>
    <a href="{% url 'blog:post_list' %}">Bosh sahifaga qaytish</a>
</div>
{% endblock %}
```

**3. URL patterns ga qo'shing (`blog/urls.py`):**
```python
urlpatterns = [
    # ... mavjud URL patterns ...
    path('test-error/', views.test_error, name='test_error'),
]
```

### Natija
Error handling to'g'ri sozlandi.

---

## 📋 Mashg'ulot 6: Qo'shimcha funksiyalar

### Vazifa
Qo'shimcha funksiyalar qo'shing.

### Qadamlar

**1. Archive view yarating (`blog/views.py`):**
```python
from django.db.models import Q
from datetime import datetime

def post_archive(request, year, month):
    """Yil va oy bo'yicha arxiv"""
    posts = Post.objects.filter(
        created_at__year=year,
        created_at__month=month,
        is_published=True
    ).order_by('-created_at')
    
    return render(request, 'blog/archive.html', {
        'posts': posts,
        'year': year,
        'month': month
    })

def popular_posts(request):
    """Mashhur postlar (eng ko'p ko'rilgan)"""
    # Bu yerda view_count maydoni bo'lishi kerak
    posts = Post.objects.filter(is_published=True).order_by('-created_at')[:5]
    return render(request, 'blog/popular_posts.html', {'posts': posts})

def category_list(request):
    """Barcha kategoriyalar"""
    categories = Category.objects.all()
    return render(request, 'blog/category_list.html', {'categories': categories})
```

**2. URL patterns ga qo'shing (`blog/urls.py`):**
```python
urlpatterns = [
    # ... mavjud URL patterns ...
    path('archive/<int:year>/<int:month>/', views.post_archive, name='post_archive'),
    path('popular/', views.popular_posts, name='popular_posts'),
    path('categories/', views.category_list, name='category_list'),
]
```

**3. Archive template (`blog/templates/blog/archive.html`):**
```html
{% extends 'blog/base.html' %}

{% block title %}{{ year }}/{{ month }} - Arxiv{% endblock %}

{% block content %}
<h1>{{ year }}/{{ month }} - Arxiv</h1>
<p>{{ posts.count }} ta post topildi</p>

{% for post in posts %}
    <article class="post">
        <h2><a href="{% url 'blog:post_detail' post.id %}">{{ post.title }}</a></h2>
        <p>{{ post.content|truncatewords:30 }}</p>
        <small>{{ post.created_at|date:"d.m.Y" }}</small>
    </article>
{% empty %}
    <p>Bu oyda postlar yo'q.</p>
{% endfor %}

<a href="{% url 'blog:post_list' %}">← Orqaga</a>
{% endblock %}
```

### Natija
Qo'shimcha funksiyalar qo'shildi.

---

## ✅ Tekshirish ro'yxati

- [ ] Function-based views yaratildi
- [ ] Class-based views yaratildi
- [ ] URL patterns sozlandi
- [ ] Template'lar yaratildi
- [ ] HTTP metodlari bilan ishlash
- [ ] Error handling sozlandi
- [ ] Qidiruv funksiyasi qo'shildi
- [ ] Arxiv funksiyasi qo'shildi
- [ ] Barcha sahifalar ishlaydi

---

## 🎉 Natija

Siz Django Views va URL Patterns bilan to'liq ishlashni o'rgandingiz:
- Function-based va Class-based views
- URL patterns va path converter'lar
- Template'lar bilan ishlash
- HTTP metodlari
- Error handling
- Qo'shimcha funksiyalar

**Keyingi dars:** Django Templates va Static Files 