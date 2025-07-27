# 📝 4-dars: Django Views va URL Patterns

---

## 1. Django Views nima?
- **View** — Django'da web sahifasini ko'rsatish uchun ishlatiladigan Python funksiyasi yoki klassi.
- Foydalanuvchi so'rovini qabul qiladi va HTTP javobini qaytaradi.
- Ma'lumotlar bazasidan ma'lumotlarni olish, template'lar bilan ishlash va foydalanuvchiga natija ko'rsatish uchun ishlatiladi.
- Batafsil: [Django Views documentation](https://docs.djangoproject.com/en/stable/topics/http/views/)

**View turlari:**
- **Function-based views** — oddiy Python funksiyalari
- **Class-based views** — Python klasslari
- **Generic views** — Django'ning tayyor view'lari

---

## 2. Function-based Views

### Oddiy view yaratish
**Fayl:** `blog/views.py`

```python
from django.shortcuts import render
from django.http import HttpResponse
from .models import Post

def home(request):
    """Bosh sahifa"""
    return HttpResponse("<h1>Xush kelibsiz!</h1>")

def post_list(request):
    """Barcha postlarni ko'rsatish"""
    posts = Post.objects.all()
    return render(request, 'blog/post_list.html', {'posts': posts})

def post_detail(request, post_id):
    """Bitta postni ko'rsatish"""
    post = Post.objects.get(id=post_id)
    return render(request, 'blog/post_detail.html', {'post': post})

def about(request):
    """Haqida sahifasi"""
    return render(request, 'blog/about.html')
```

### View parametrlari
```python
def post_detail(request, post_id, category=None):
    """
    request — HTTP so'rov ma'lumotlari
    post_id — URL dan olingan parametr
    category — ixtiyoriy parametr
    """
    post = Post.objects.get(id=post_id)
    return render(request, 'blog/post_detail.html', {
        'post': post,
        'category': category
    })
```

---

## 3. Class-based Views

### Class-based view yaratish
```python
from django.views.generic import ListView, DetailView, CreateView, UpdateView, DeleteView
from django.contrib.auth.mixins import LoginRequiredMixin
from .models import Post

class PostListView(ListView):
    model = Post
    template_name = 'blog/post_list.html'
    context_object_name = 'posts'
    ordering = ['-created_at']
    paginate_by = 10

class PostDetailView(DetailView):
    model = Post
    template_name = 'blog/post_detail.html'
    context_object_name = 'post'

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    template_name = 'blog/post_form.html'
    fields = ['title', 'content', 'category']
    success_url = '/blog/'

class PostUpdateView(LoginRequiredMixin, UpdateView):
    model = Post
    template_name = 'blog/post_form.html'
    fields = ['title', 'content', 'category']
    success_url = '/blog/'

class PostDeleteView(LoginRequiredMixin, DeleteView):
    model = Post
    template_name = 'blog/post_confirm_delete.html'
    success_url = '/blog/'
```

### Class-based view afzalliklari
- **Qisqa kod** — kamroq kod yozish kerak
- **Tayyor funksiyalar** — CRUD amallari avtomatik
- **Xavfsizlik** — mixin'lar orqali ruxsatlar
- **Qayta ishlatish** — boshqa loyihalarda ishlatish mumkin

---

## 4. URL Patterns

### URL nima?
- **URL (Uniform Resource Locator)** — web sahifasining manzili
- Django'da URL patterns orqali so'rovlarni view'larga yo'naltiriladi
- Har bir URL pattern ma'lum bir view'ga bog'lanadi

### Asosiy URL sozlash
**Fayl:** `mysite/urls.py`

```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),
    path('', include('home.urls')),
]

# Development uchun static va media fayllar
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

### App URL sozlash
**Fayl:** `blog/urls.py`

```python
from django.urls import path
from . import views

app_name = 'blog'  # URL namespace

urlpatterns = [
    # Function-based views
    path('', views.post_list, name='post_list'),
    path('post/<int:post_id>/', views.post_detail, name='post_detail'),
    path('about/', views.about, name='about'),
    
    # Class-based views
    path('posts/', views.PostListView.as_view(), name='post_list_cbv'),
    path('post/<int:pk>/', views.PostDetailView.as_view(), name='post_detail_cbv'),
    path('post/new/', views.PostCreateView.as_view(), name='post_create'),
    path('post/<int:pk>/edit/', views.PostUpdateView.as_view(), name='post_update'),
    path('post/<int:pk>/delete/', views.PostDeleteView.as_view(), name='post_delete'),
]
```

---

## 5. URL Pattern turlari

### Path converter'lar
```python
# Integer
path('post/<int:post_id>/', views.post_detail, name='post_detail')

# String
path('category/<str:category_name>/', views.category_posts, name='category_posts')

# Slug (harflar, raqamlar, tire)
path('post/<slug:post_slug>/', views.post_detail, name='post_detail')

# UUID
path('post/<uuid:post_uuid>/', views.post_detail, name='post_detail')

# Path (to'liq yo'l)
path('files/<path:file_path>/', views.serve_file, name='serve_file')
```

### Regex patterns
```python
from django.urls import re_path

urlpatterns = [
    # Regex pattern
    re_path(r'^post/(?P<year>\d{4})/(?P<month>\d{2})/(?P<day>\d{2})/$', 
            views.post_archive, name='post_archive'),
]
```

---

## 6. Template'da URL ishlatish

### URL tag'lari
```html
<!-- Asosiy URL -->
<a href="{% url 'blog:post_list' %}">Barcha postlar</a>

<!-- Parametr bilan URL -->
<a href="{% url 'blog:post_detail' post.id %}">{{ post.title }}</a>

<!-- Ko'p parametr bilan -->
<a href="{% url 'blog:post_archive' year=2024 month=01 day=15 %}">Arxiv</a>

<!-- Namespace bilan -->
<a href="{% url 'blog:post_create' %}">Yangi post</a>
```

### Reverse URL
```python
from django.urls import reverse
from django.shortcuts import redirect

def create_post(request):
    # Post yaratish logikasi...
    return redirect(reverse('blog:post_list'))

def edit_post(request, post_id):
    # Post tahrirlash logikasi...
    return redirect(reverse('blog:post_detail', args=[post_id]))
```

---

## 7. HTTP metodlari bilan ishlash

### GET va POST so'rovlari
```python
from django.shortcuts import render, redirect
from django.http import HttpResponse

def contact(request):
    if request.method == 'POST':
        # POST so'rovni qayta ishlash
        name = request.POST.get('name')
        email = request.POST.get('email')
        message = request.POST.get('message')
        
        # Ma'lumotlarni saqlash yoki email yuborish
        return HttpResponse("Xabar yuborildi!")
    
    else:
        # GET so'rovni qayta ishlash
        return render(request, 'blog/contact.html')
```

### Template'da form
```html
<form method="POST" action="{% url 'blog:contact' %}">
    {% csrf_token %}
    <input type="text" name="name" placeholder="Ismingiz">
    <input type="email" name="email" placeholder="Email">
    <textarea name="message" placeholder="Xabar"></textarea>
    <button type="submit">Yuborish</button>
</form>
```

---

## 8. Context va Template

### Context yaratish
```python
def post_list(request):
    posts = Post.objects.all()
    categories = Category.objects.all()
    
    context = {
        'posts': posts,
        'categories': categories,
        'total_posts': posts.count(),
        'user': request.user,
    }
    
    return render(request, 'blog/post_list.html', context)
```

### Template'da context ishlatish
```html
<h1>Blog ({{ total_posts }} ta post)</h1>

{% for post in posts %}
    <article>
        <h2>{{ post.title }}</h2>
        <p>{{ post.content|truncatewords:30 }}</p>
        <small>{{ post.created_at|date:"d.m.Y" }}</small>
    </article>
{% empty %}
    <p>Hozircha postlar yo'q.</p>
{% endfor %}

{% if user.is_authenticated %}
    <a href="{% url 'blog:post_create' %}">Yangi post</a>
{% endif %}
```

---

## 9. Error handling

### 404 xatosi
```python
from django.shortcuts import get_object_or_404

def post_detail(request, post_id):
    post = get_object_or_404(Post, id=post_id)
    return render(request, 'blog/post_detail.html', {'post': post})
```

### Custom error sahifalari
**Fayl:** `templates/404.html`
```html
<!DOCTYPE html>
<html>
<head>
    <title>Sahifa topilmadi</title>
</head>
<body>
    <h1>404 - Sahifa topilmadi</h1>
    <p>Axtirgan sahifa mavjud emas.</p>
    <a href="{% url 'blog:post_list' %}">Bosh sahifaga qaytish</a>
</body>
</html>
```

---

## 10. Amaliyot uchun tavsiyalar

### View'lar uchun
1. **Qisqa va aniq** — har bir view bitta vazifani bajarishi kerak
2. **Xavfsizlik** — foydalanuvchi ma'lumotlarini tekshiring
3. **Performance** — ma'lumotlarni samarali oling
4. **DRY** — kodni takrorlamang

### URL'lar uchun
1. **Tushunarli** — URL'lar aniq va tushunarli bo'lishi kerak
2. **SEO-friendly** — qidiruv tizimlari uchun yaxshi
3. **Namespace** — app'lar uchun namespace ishlating
4. **Versioning** — API uchun versiya qo'shing

### Template'lar uchun
1. **Base template** — asosiy template yarating
2. **Include** — kichik qismlarni alohida fayllarga ajrating
3. **Filters** — template filter'lardan foydalaning
4. **Security** — XSS hujumlaridan himoyalang

---

## 11. Foydali maslahatlar

- **View'lar** — business logic uchun ishlating
- **Template'lar** — presentation uchun ishlating
- **URL'lar** — RESTful prinsiplariga amal qiling
- **Error handling** — barcha xatolarni ko'rib chiqing
- **Testing** — view'lar uchun test yozing
- **Documentation** — kodni hujjatlashtiring

---

**Keyingi dars:**  
5-dars: Django Templates va Static Files  
Bu darsda siz Django template'larini yaratish, static fayllar bilan ishlash va frontend dizaynini o'rganasiz. 