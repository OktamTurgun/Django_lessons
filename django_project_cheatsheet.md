# 🚀 Django Loyiha Yaratish - To'liq Qadamlar Ketma-ketligi

---

## 📋 1. Muhitni tayyorlash

### Virtual environment yaratish
```bash
# Virtual environment yaratish
python -m venv env

# Virtual environment ni faollashtirish (Windows)
env\Scripts\activate

# Virtual environment ni faollashtirish (Linux/Mac)
source env/bin/activate
```

### Django ni o'rnatish
```bash
# Django ni o'rnatish
pip install django

# O'rnatilgan versiyani tekshirish
python -m django --version
```

---

## 📋 2. Django loyiha yaratish

### Yangi loyiha yaratish
```bash
# Yangi Django loyiha yaratish
django-admin startproject mysite

# Loyiha papkasiga kirish
cd mysite
```

**Natija:** `mysite` papkasi yaratiladi va ichida quyidagi fayllar bo'ladi:
```
mysite/
├── manage.py
└── mysite/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    ├── asgi.py
    └── wsgi.py
```

### Loyihani sinab ko'rish
```bash
# Server ishga tushirish
python manage.py runserver

# Brauzerda ochish: http://127.0.0.1:8000/
```

---

## 📋 3. App yaratish

### Yangi app yaratish
```bash
# Yangi app yaratish (masalan: blog)
python manage.py startapp blog
```

**Natija:** `blog` papkasi yaratiladi:
```
blog/
├── __init__.py
├── admin.py
├── apps.py
├── models.py
├── tests.py
├── urls.py
└── views.py
```

---

## 📋 4. App ni loyihaga qo'shish

### Settings.py ga app qo'shish
**Fayl:** `mysite/settings.py`

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',  # Qo'shing
]
```

---

## 📋 5. Model yaratish

### Model yozish
**Fayl:** `blog/models.py`

```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200, verbose_name="Sarlavha")
    content = models.TextField(verbose_name="Matn")
    created_at = models.DateTimeField(auto_now_add=True, verbose_name="Yaratilgan sana")
    updated_at = models.DateTimeField(auto_now=True, verbose_name="Yangilangan sana")
    is_published = models.BooleanField(default=False, verbose_name="Nashr etilgan")
    
    def __str__(self):
        return self.title
    
    class Meta:
        verbose_name = "Post"
        verbose_name_plural = "Postlar"
        ordering = ['-created_at']
```

---

## 📋 6. Migratsiya qilish

### Migratsiya buyruqlari
```bash
# Migratsiya faylini yaratish
python manage.py makemigrations

# Migratsiyani ma'lumotlar bazasiga qo'llash
python manage.py migrate

# Migratsiya holatini ko'rish
python manage.py showmigrations
```

---

## 📋 7. Superuser yaratish

### Admin panel uchun superuser
```bash
# Superuser yaratish
python manage.py createsuperuser

# Misol:
# Username: admin
# Email: admin@example.com
# Password: admin123
```

---

## 📋 8. Admin panelga model qo'shish

### Admin.py sozlash
**Fayl:** `blog/admin.py`

```python
from django.contrib import admin
from .models import Post

# Oddiy ro'yxatdan o'tkazish
admin.site.register(Post)

# Yoki maxsus admin klass bilan
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'created_at', 'is_published']
    list_filter = ['is_published', 'created_at']
    search_fields = ['title', 'content']
    list_editable = ['is_published']
    list_per_page = 10
```

---

## 📋 9. URL sozlash

### Asosiy URL sozlash
**Fayl:** `mysite/urls.py`

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),
]
```

### App URL sozlash
**Fayl:** `blog/urls.py` (yangi yarating)

```python
from django.urls import path
from . import views

app_name = 'blog'

urlpatterns = [
    path('', views.post_list, name='post_list'),
    path('<int:pk>/', views.post_detail, name='post_detail'),
]
```

---

## 📋 10. View yaratish

### Views.py da view yozish
**Fayl:** `blog/views.py`

```python
from django.shortcuts import render, get_object_or_404
from .models import Post

def post_list(request):
    posts = Post.objects.filter(is_published=True)
    return render(request, 'blog/post_list.html', {'posts': posts})

def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    return render(request, 'blog/post_detail.html', {'post': post})
```

---

## 📋 11. Template yaratish

### Template papkasini yaratish
```bash
# Template papkasini yaratish
mkdir blog/templates
mkdir blog/templates/blog
```

### Post list template
**Fayl:** `blog/templates/blog/post_list.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Blog</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
    <h1>Blog postlar</h1>
    {% for post in posts %}
        <article>
            <h2><a href="{% url 'blog:post_detail' post.pk %}">{{ post.title }}</a></h2>
            <p>{{ post.content|truncatewords:30 }}</p>
            <small>{{ post.created_at|date:"d.m.Y H:i" }}</small>
        </article>
    {% empty %}
        <p>Hozircha postlar yo'q.</p>
    {% endfor %}
</body>
</html>
```

### Post detail template
**Fayl:** `blog/templates/blog/post_detail.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>{{ post.title }}</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
    <h1>{{ post.title }}</h1>
    <p>{{ post.content }}</p>
    <small>{{ post.created_at|date:"d.m.Y H:i" }}</small>
    <br>
    <a href="{% url 'blog:post_list' %}">← Orqaga</a>
</body>
</html>
```

---

## 📋 12. Loyihani test qilish

### Server ishga tushirish
```bash
# Server ishga tushirish
python manage.py runserver

# Test qilish:
# 1. http://127.0.0.1:8000/admin/ - Admin panel
# 2. http://127.0.0.1:8000/blog/ - Blog sahifasi
```

---

## 🎯 Qo'shimcha sozlamalar

### Static fayllar uchun
**Fayl:** `mysite/settings.py`

```python
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

STATIC_URL = '/static/'
STATICFILES_DIRS = [
    BASE_DIR / "static",
]
STATIC_ROOT = BASE_DIR / 'staticfiles'
```

### Media fayllar uchun
**Fayl:** `mysite/settings.py`

```python
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

### Database sozlamalari
**Fayl:** `mysite/settings.py`

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

### Time zone va til sozlamalari
**Fayl:** `mysite/settings.py`

```python
LANGUAGE_CODE = 'uz'
TIME_ZONE = 'Asia/Tashkent'
USE_I18N = True
USE_TZ = True
```

---

## ✅ Tekshirish ro'yxati

- [ ] Virtual environment yaratildi va faollashtirildi
- [ ] Django o'rnatildi
- [ ] Loyiha yaratildi
- [ ] Server ishga tushdi
- [ ] App yaratildi
- [ ] App loyihaga qo'shildi
- [ ] Model yaratildi
- [ ] Migratsiya qilindi
- [ ] Superuser yaratildi
- [ ] Admin panel sozlandi
- [ ] URL sozlandi
- [ ] View yaratildi
- [ ] Template yaratildi
- [ ] Loyiha test qilindi

---

## 📁 Loyiha tuzilishi (yakuniy)

```
mysite/
├── manage.py
├── db.sqlite3
├── static/
├── media/
├── mysite/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   └── wsgi.py
└── blog/
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── models.py
    ├── tests.py
    ├── urls.py
    ├── views.py
    └── templates/
        └── blog/
            ├── post_list.html
            └── post_detail.html
```

---

## 🚀 Foydali buyruqlar

```bash
# Server ishga tushirish
python manage.py runserver

# Server ishga tushirish (boshqa port)
python manage.py runserver 8080

# Migratsiya
python manage.py makemigrations
python manage.py migrate

# Shell ochish
python manage.py shell

# Test ishga tushirish
python manage.py test

# Static fayllarni yig'ish
python manage.py collectstatic

# Loyiha holatini tekshirish
python manage.py check
```

---

## 📚 Foydali maslahatlar

1. **Virtual environment** har doim faollashtiring
2. **Migratsiya** har safar model o'zgartirsangiz
3. **Admin panel** ma'lumotlarni boshqarish uchun
4. **Template** fayllar papka strukturasi muhim
5. **URL patterns** aniq va tushunarli bo'lishi kerak
6. **View** funksiyalari qisqa va samarali bo'lishi kerak
7. **Model** maydonlari to'g'ri turda bo'lishi kerak

---

**Bu cheatsheet ni har safar yangi Django loyiha yaratganda ishlatishingiz mumkin!** 